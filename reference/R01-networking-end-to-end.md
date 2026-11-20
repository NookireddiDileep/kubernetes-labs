# R01 — Kubernetes networking end to end

> Concept note in my own words (November 2026), upstream Kubernetes. Plain language first, then the mechanism, then the L4 interview angle. OpenShift's own networking (OVN-Kubernetes, Routes, IngressController) is in [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/openshift-vs-kubernetes-architecture.md); this note covers the vendor-neutral model underneath.

**Contents**

1. [The big picture](#1-the-big-picture)
2. [Pod networking and CNI](#2-pod-networking-and-cni)
3. [Services](#3-services)
4. [kube-proxy: how a virtual IP becomes a real pod](#4-kube-proxy-how-a-virtual-ip-becomes-a-real-pod)
5. [DNS with CoreDNS](#5-dns-with-coredns)
6. [Getting traffic in: NodePort, LoadBalancer, Ingress, Gateway API](#6-getting-traffic-in-nodeport-loadbalancer-ingress-gateway-api)
7. [NetworkPolicy](#7-networkpolicy)
8. [Troubleshooting playbook](#8-troubleshooting-playbook)
9. [L4 interview angle](#9-l4-interview-angle)
10. [Labs](#10-labs)

---

## 1. The big picture

**Plain-language version:** think of the cluster as **a city**.

- **Pods are houses.** Every house gets its own street address (pod IP), but houses are rebuilt often and get a **new address** each time.
- **The CNI plugin builds the roads** so any house can reach any other house directly.
- **A Service is a company's switchboard number.** Callers dial one number that never changes; the switchboard forwards the call to whichever house is open today.
- **kube-proxy is the switchboard wiring** installed on every street (node) so the number works from anywhere.
- **CoreDNS is the phone book:** you look up `payments` and get the switchboard number.
- **Ingress / Gateway API is the reception desk at the city gate:** visitors from outside are directed to the right company by name and path.
- **NetworkPolicy is the security guard:** rules on who may knock on which door.

Kubernetes networking solves **four problems**:

| # | Problem | Solved by |
|---|---|---|
| 1 | Container ↔ container in the same pod | Shared network namespace: they talk over `localhost` |
| 2 | Pod ↔ pod, any node | CNI plugin: flat pod network, no NAT |
| 3 | Pod ↔ Service (stable address for changing pods) | Service virtual IP + EndpointSlices + kube-proxy (or an eBPF replacement) |
| 4 | Outside world ↔ Service | NodePort, LoadBalancer, Ingress, Gateway API |

…plus **DNS** to find things by name, and **NetworkPolicy** to restrict them.

---

## 2. Pod networking and CNI

**The Kubernetes network model (the rules every CNI must follow):**

1. Every pod gets its **own IP** address.
2. Pods can reach all other pods **without NAT**, on any node.
3. Agents on a node (kubelet, system daemons) can reach all pods on that node.

**What happens when a pod starts:**

```text
kubelet ──► container runtime (containerd / CRI-O)
              ├─ creates the pod sandbox ("pause" container) with its own network namespace
              └─ calls the CNI plugin (binary in /opt/cni/bin, config in /etc/cni/net.d)
                    ├─ creates a veth pair: one end eth0 in the pod, one end on the host
                    ├─ IPAM assigns a pod IP from the node's pod CIDR
                    └─ programs routes / bridge / overlay so other nodes can reach it
```

All containers in the pod join the **pause container's** network namespace. That's why they share one IP and `localhost`.

**How pod traffic crosses nodes:**

| Approach | How | Examples | Trade-off |
|---|---|---|---|
| **Overlay** | Encapsulate pod packets in UDP between nodes (VXLAN, Geneve) | Flannel (VXLAN, K3s default), OVN-Kubernetes (Geneve) | Works on any network; extra header lowers MTU, small CPU cost |
| **Routed / BGP** | Pod CIDRs announced as real routes | Calico (BGP mode), cloud VPC-native routing | No encapsulation, best performance; needs network team cooperation |
| **eBPF dataplane** | Packet handling in kernel eBPF programs | Cilium | High performance, can replace kube-proxy, deep observability (Hubble); more kernel-version sensitive |

**MTU matters:** an overlay adds ~50 bytes (VXLAN) or more (Geneve); the pod MTU must be smaller than the node network's MTU, or large packets fail while small ones (ping, DNS) work. A classic "SSH works, file transfer hangs" symptom.

```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name} {.spec.podCIDR}{"\n"}{end}'
kubectl exec <pod> -- ip addr ; kubectl exec <pod> -- ip route
ls /etc/cni/net.d/ ; ip -d link show flannel.1        # on a node
```

---

## 3. Services

A **Service** gives a set of pods (chosen by a **label selector**) one stable **virtual IP** (ClusterIP) and DNS name.

```yaml
apiVersion: v1
kind: Service
metadata: { name: web, namespace: shop }
spec:
  selector: { app: web }          # which pods
  ports:
  - name: http
    port: 80                      # the Service's port
    targetPort: 8080              # the container's port (number or named port)
```

**How it tracks pods:** the **EndpointSlice controller** watches pods matching the selector and writes their IPs (only **Ready** pods count as serving) into **EndpointSlice** objects. Everything that load-balances reads EndpointSlices, not pods.

| Type | Reachable from | Use |
|---|---|---|
| `ClusterIP` (default) | Inside the cluster | Service-to-service traffic |
| `NodePort` | `<any node IP>:30000–32767` | Simple external access, or a target for an external LB |
| `LoadBalancer` | External IP from a cloud LB, MetalLB, or K3s ServiceLB | Exposing one service directly |
| `ExternalName` | — (DNS CNAME only) | Alias to an external DNS name |
| **Headless** (`clusterIP: None`) | DNS returns pod IPs directly | StatefulSets, client-side load balancing, databases |

**Traffic policies (senior-level detail):**

| Field | Values | Effect |
|---|---|---|
| `externalTrafficPolicy` (NodePort/LB) | `Cluster` (default) | Any node accepts and may forward to a pod on another node: even spread, but **client source IP is SNAT-ed** and an extra hop is added |
| | `Local` | Only nodes **with a local pod** answer (LB health checks find them); **client source IP preserved**, no extra hop, but load can be uneven |
| `internalTrafficPolicy` | `Cluster` / `Local` | Same idea for in-cluster traffic (e.g. talk only to the node-local agent) |
| `sessionAffinity` | `ClientIP` | Sticky to one pod per client IP |
| `trafficDistribution` | `PreferClose` | Prefer endpoints in the same zone (topology-aware routing) |

---

## 4. kube-proxy: how a virtual IP becomes a real pod

**Plain-language version:** a ClusterIP **isn't on any network card**. It's a fake number. On every node, kube-proxy writes **rewrite rules**: "anything sent to 10.43.12.7:80, change the destination to one of these real pod IPs".

**Mechanism:** kube-proxy (a DaemonSet, or built into the `k3s` process) **watches Services and EndpointSlices** and programs the node's kernel:

| Mode | How | Notes |
|---|---|---|
| `iptables` | NAT chains per Service; random selection by probability rules | Long-time default; rule updates get slow with tens of thousands of Services |
| `ipvs` | Kernel IPVS virtual servers, hash tables | Faster lookups at large scale; older alternative |
| `nftables` | nftables sets/maps, incremental updates | Modern replacement for iptables mode (GA in v1.33); preferred for new large clusters |
| **No kube-proxy** | eBPF programs (e.g. Cilium kube-proxy replacement) | Fastest; the CNI takes over Service load balancing |

**Packet walk (iptables mode):** a pod runs `curl http://10.43.12.7:80`

1. Packet leaves the pod's `eth0` → host side of the veth.
2. In the **source node's** netfilter, kube-proxy's rules match `10.43.12.7:80` and **DNAT** it to, say, `10.42.1.15:8080` (a backend pod), chosen at random.
3. **conntrack** records the translation.
4. The CNI routes the packet to `10.42.1.15` (same node, or across nodes via overlay/routes).
5. The reply comes back; conntrack **reverses the NAT**, so the client sees a reply from `10.43.12.7`.

Key consequence: load balancing happens **per connection, on the client's node**. Long-lived connections (gRPC, HTTP/2 keep-alive) stick to one pod; scaling up doesn't rebalance them. The fix is an L7-aware proxy, mesh, or client-side balancing.

```bash
kubectl get svc web -o wide ; kubectl get endpointslices -l kubernetes.io/service-name=web
# on a node (iptables mode):
sudo iptables -t nat -L KUBE-SERVICES -n | grep 10.43.12.7
sudo conntrack -L -d 10.43.12.7 2>/dev/null | head
```

---

## 5. DNS with CoreDNS

**CoreDNS** runs as a Deployment behind the `kube-dns` Service. Every pod's `/etc/resolv.conf` points at that Service IP.

| Record | Name | Returns |
|---|---|---|
| Service (ClusterIP) | `web.shop.svc.cluster.local` | The ClusterIP |
| Headless Service | `db.shop.svc.cluster.local` | All Ready pod IPs |
| StatefulSet pod | `db-0.db.shop.svc.cluster.local` | That pod's IP |
| Named port (SRV) | `_http._tcp.web.shop.svc.cluster.local` | Port + target |

**The `ndots:5` trap (classic L4 topic):** pod resolv.conf has `search shop.svc.cluster.local svc.cluster.local cluster.local …` and `options ndots:5`. Any name with **fewer than 5 dots** is tried with every search suffix first. So `api.example.com` (2 dots) causes several failed lookups before the real one: extra latency and DNS load at scale.

Fixes: use **FQDNs with a trailing dot** (`api.example.com.`), lower `ndots` in the pod's `dnsConfig`, and run **NodeLocal DNSCache** on large clusters.

Other DNS failure patterns: CoreDNS pods overloaded (scale them, check `loop`/upstream errors in logs), NetworkPolicy blocking UDP/TCP 53 to `kube-system`, conntrack races on UDP.

```bash
kubectl run dnstest --rm -it --image=busybox:1.36 --restart=Never -- nslookup web.shop
kubectl exec <pod> -- cat /etc/resolv.conf
kubectl -n kube-system logs deploy/coredns
```

---

## 6. Getting traffic in: NodePort, LoadBalancer, Ingress, Gateway API

| Option | Layer | How it works | Limits |
|---|---|---|---|
| NodePort | L4 | Port 30000–32767 open on every node | Odd ports; you still need something in front |
| LoadBalancer | L4 | Cloud LB / MetalLB / K3s ServiceLB gives an external IP per Service | One IP (and often one paid LB) per Service |
| **Ingress** | L7 HTTP(S) | `Ingress` object read by an **Ingress controller** (Traefik in K3s, HAProxy, NGINX variants…) | HTTP only; advanced features hidden in controller-specific annotations; one object mixes infra and app concerns |
| **Gateway API** | L4–L7 | Role-oriented objects read by a Gateway controller (Envoy Gateway, Cilium, Istio, Traefik, NGINX Gateway Fabric, cloud LBs…) | The modern standard; check which features your implementation supports |

**Gateway API objects (who owns what):**

```text
GatewayClass   (infra provider)   which implementation
Gateway        (platform team)    listeners: ports, hostnames, TLS; which namespaces may attach routes
HTTPRoute      (app team)         paths, headers, weights → backend Services
(+ GRPCRoute, TLSRoute, TCPRoute, ReferenceGrant for cross-namespace references)
```

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata: { name: web, namespace: shop }
spec:
  parentRefs: [{ name: public-gw, namespace: gateway-infra }]
  hostnames: ["shop.example.com"]
  rules:
  - matches: [{ path: { type: PathPrefix, value: /api } }]
    backendRefs:
    - { name: api-v1, port: 80, weight: 90 }
    - { name: api-v2, port: 80, weight: 10 }     # canary built in, no annotations
```

**Why it matters in 2026:** the community **ingress-nginx** controller was retired: SIG Network announced it in November 2025 and the project reached end of life in **March 2026** (no more releases or security fixes). Teams are migrating to **Gateway API** (the `ingress2gateway` tool converts existing Ingress objects) or to another maintained Ingress controller. The Ingress **API** itself still exists; only that controller is retired.

---

## 7. NetworkPolicy

**Plain-language version:** by default every door is open. A NetworkPolicy puts a guard on the doors of the pods it selects, with a list of who may enter (ingress) or where they may go (egress).

**Rules that trip people up:**

1. **Default allow:** without any policy, all traffic is allowed.
2. A pod becomes **isolated for a direction** (ingress or egress) as soon as **any** policy selects it for that direction; then only what some policy allows gets through.
3. Policies are **additive** (union of allows). There's no "deny" rule in the standard API.
4. Egress policies must **allow DNS** (UDP/TCP 53 to CoreDNS), or everything appears broken.
5. Rules match **pods and namespaces by label**, or IP blocks; not Service names.
6. **Enforcement needs a policy engine:** Calico, Cilium, OVN-Kubernetes, or a separate controller (K3s embeds one based on kube-router). With plain Flannel and no controller, policies are accepted but ignored.

```yaml
# default-deny ingress for a namespace, then allow only the frontend to reach the API
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: default-deny-ingress, namespace: shop }
spec: { podSelector: {}, policyTypes: [Ingress] }
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: api-from-frontend, namespace: shop }
spec:
  podSelector: { matchLabels: { app: api } }
  policyTypes: [Ingress]
  ingress:
  - from: [{ podSelector: { matchLabels: { app: frontend } } }]
    ports: [{ protocol: TCP, port: 8080 }]
```

Newer cluster-wide policy APIs (AdminNetworkPolicy / BaselineAdminNetworkPolicy) let platform teams set rules tenants can't override; support depends on the CNI.

---

## 8. Troubleshooting playbook

**"Service not reachable" — work from the client outwards, stop at the first broken hop:**

1. **DNS:** `nslookup <svc>.<ns>` from a pod. Fails → CoreDNS pods, `kube-dns` endpoints, NetworkPolicy on port 53.
2. **Service → endpoints:** `kubectl get endpointslices -l kubernetes.io/service-name=<svc>`. Empty → selector/label mismatch or no **Ready** pods (readiness probe).
3. **Ports:** Service `port` → `targetPort` → container actually listening on that port and on `0.0.0.0`, not `127.0.0.1` (`kubectl exec <pod> -- ss -lntp`).
4. **Pod IP directly:** `curl <podIP>:<port>` from another pod. Works by pod IP but not via ClusterIP → kube-proxy/eBPF rules on the client node.
5. **Cross-node only fails:** CNI/overlay between nodes: firewall for VXLAN (UDP 8472 for Flannel) / Geneve (6081), MTU, node routes.
6. **NetworkPolicy:** does any policy select the destination (ingress) or source (egress) without allowing this flow?
7. **External path:** LB/NodePort reachability, `externalTrafficPolicy: Local` with no pod on the hit node, Ingress/Gateway controller logs, route/host match, TLS.
8. **Under load only:** conntrack table full (`dmesg`), DNS `ndots` amplification, CoreDNS saturation.

```bash
kubectl run net-debug --rm -it --image=nicolaka/netshoot --restart=Never -- bash
#   dig, curl, nc, tcpdump, mtr, iperf3 inside the cluster network
kubectl debug -it <pod> --image=nicolaka/netshoot --target=<container>   # join a running pod's namespaces
```

---

## 9. L4 interview angle

1. **Walk me through what happens when a pod curls a Service name.**
   DNS lookup to CoreDNS via the `kube-dns` ClusterIP (with search domains and ndots) → returns the ClusterIP → packet DNAT-ed on the **client's node** by kube-proxy rules (or eBPF) to one Ready endpoint from EndpointSlices → conntrack entry → CNI delivers it (overlay or routed) → reply reverse-NAT-ed.

2. **ClusterIP doesn't respond to ping. Is the Service broken?**
   No. The ClusterIP is a virtual IP implemented by NAT rules for the Service's ports only; there's no interface to answer ICMP. Test with the real port.

3. **We scaled from 2 to 10 pods but load stays on the first 2. Why?**
   Long-lived connections (gRPC/HTTP keep-alive) are balanced once, at connect time, by L4 NAT. New pods only get new connections. Use L7 load balancing (mesh/Gateway), client-side balancing, or connection max-age.

4. **`externalTrafficPolicy: Local` vs `Cluster`?**
   Local preserves client source IP and avoids an extra hop but can unbalance load and needs health checks; Cluster spreads evenly but SNATs the source IP.

5. **iptables vs IPVS vs nftables vs eBPF kube-proxy?**
   iptables: simple but slow rule sync at scale; IPVS: hash-based, scaled better; nftables: the modern replacement with incremental updates; eBPF (Cilium): removes kube-proxy entirely, fastest, more observability.

6. **DNS latency is high cluster-wide. What's your hypothesis?**
   `ndots:5` amplification for external names, CoreDNS under-provisioned, conntrack/UDP issues. Measure, then FQDNs, NodeLocal DNSCache, scale CoreDNS.

7. **Cross-node pod traffic fails, same-node works.**
   Overlay ports blocked between nodes, MTU mismatch, wrong node routes, or a NetworkPolicy using IP blocks. Test with large and small packets.

8. **We applied a NetworkPolicy and the app broke completely.**
   Egress isolation without allowing DNS, or the policy selected more pods than intended (empty `podSelector: {}` selects all).

9. **Why Gateway API over Ingress?**
   Role separation (platform owns Gateways, apps own Routes), portable features instead of annotations (traffic splitting, header matching, cross-namespace with ReferenceGrant), more protocols. And the widely used ingress-nginx controller is retired.

10. **Design networking for a 500-node multi-tenant cluster.**
    CNI choice by scale and policy needs (eBPF dataplane, kube-proxy replacement), non-overlapping pod/service CIDRs sized for growth, default-deny per namespace plus admin policies, Gateway API with shared gateways per trust zone, NodeLocal DNSCache, topology-aware routing for cross-zone cost, flow observability.

---

## 10. Labs

**Evidence already in this repo:**

| Topic | Lab |
|---|---|
| Services, ClusterIP, EndpointSlices | [Lab 02](../01-kubernetes-foundation/labs/lab-02-services/README.md) |
| Ingress through Traefik (K3s) | [Lab 12](../01-kubernetes-foundation/labs/lab-12-ingress/README.md) |
| NetworkPolicy enforced by K3s's embedded controller | [Lab 13 scenario 08](../01-kubernetes-foundation/labs/lab-13-production-troubleshooting/scenario-08-network-policy/README.md) |

**Planned for module 05 (networking deep dive):**

- [ ] Packet walk: trace a Service call with `iptables`/`nft` rules and `conntrack` on the node
- [ ] Compare kube-proxy iptables vs nftables mode on a kubeadm cluster
- [ ] `ndots` experiment: measure lookups for `api.example.com` vs `api.example.com.`
- [ ] Gateway API with a maintained implementation: Gateway + HTTPRoute + 90/10 split
- [ ] Migrate an Ingress with `ingress2gateway`
- [ ] Default-deny namespace that breaks DNS, then fix it
- [ ] MTU mismatch on a two-node overlay: reproduce "small works, large hangs"

## Sources

- Kubernetes docs: [Cluster networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/), [Services](https://kubernetes.io/docs/concepts/services-networking/service/), [Virtual IPs and Service proxies](https://kubernetes.io/docs/reference/networking/virtual-ips/), [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/), [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/), [Gateway API](https://gateway-api.sigs.k8s.io/)
- Kubernetes blog: [Ingress NGINX retirement: what you need to know](https://kubernetes.io/blog/2025/11/11/ingress-nginx-retirement/) (Nov 2025)
