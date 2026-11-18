# 🚨 Scenario 08 - Network Policy Troubleshooting

## 🎯 Objective

Learn how to implement, verify, troubleshoot, and understand Kubernetes **NetworkPolicies** that control Pod-to-Pod communication within a cluster.

This lab demonstrates how NetworkPolicies are used to restrict network traffic and highlights the dependency on the underlying Container Network Interface (CNI) plugin for policy enforcement.

---

# 📚 Learning Objectives

After completing this lab, you will be able to:

- Understand Kubernetes NetworkPolicy concepts
- Verify Pod-to-Pod communication
- Implement an Ingress NetworkPolicy
- Troubleshoot blocked network traffic
- Verify NetworkPolicy enforcement
- Understand the role of CNI plugins in NetworkPolicy implementation
- Explain NetworkPolicy behavior during technical interviews

---

# 🏗️ Lab Environment

| Component | Details |
|------------|---------|
| Kubernetes Distribution | K3s |
| Kubernetes Version | v1.36.2+k3s1 |
| Operating System | Ubuntu 24.04 LTS |
| Container Runtime | containerd |
| Cloud Platform | Google Cloud Platform (GCP) |
| CNI | Flannel (default K3s) |
| NetworkPolicy enforcement | K3s embedded network policy controller (kube-router netpol), on by default |

---

# 📁 Lab Files

```
scenario-08-network-policy/
│
├── README.md
├── nginx.yaml
├── busybox.yaml
├── network-policy.yaml
└── screenshots/
    ├── 01-connectivity-before-policy.png
    ├── 02-network-policy-created.png
    ├── 03-connectivity-test.png
    ├── 04-describe-network-policy.png
    └── 05-cleanup.png
```

---

# 📖 Problem Statement

By default, Pods within the same Kubernetes namespace can communicate freely with one another.

A **NetworkPolicy** allows administrators to control and restrict network communication between Pods.

In this lab, an Ingress NetworkPolicy is created for the NGINX Pod to demonstrate how traffic can be restricted based on defined policy rules.

---

# 📝 Deploy the NGINX Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

Deploy:

```bash
kubectl apply -f nginx.yaml
```

Expose the Pod:

```bash
kubectl expose pod nginx --port=80
```

---

# 📝 Deploy the BusyBox Client

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: busybox
    image: busybox
    command:
      - sleep
      - "3600"
```

Deploy:

```bash
kubectl apply -f busybox.yaml
```

---

# 🔍 Verify Connectivity Before Policy

Access the NGINX service from the BusyBox Pod.

```bash
kubectl exec busybox -- wget -qO- http://nginx
```

Expected output:

```
Welcome to nginx!
```

This confirms that Pod-to-Pod communication is working before any NetworkPolicy is applied.

---

# 📝 Create the NetworkPolicy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector:
    matchLabels:
      app: nginx
  policyTypes:
    - Ingress
```

Apply:

```bash
kubectl apply -f network-policy.yaml
```

---

# 🔍 Verify Connectivity After Applying Policy

```bash
kubectl exec busybox -- wget -qO- --timeout=5 http://nginx
```

## Expected Behavior

The connection is **blocked**. In this lab `wget` failed straight away with `Connection refused` (exit code 1) instead of returning the NGINX page; see `screenshots/03-connectivity-test.png`.

Flannel itself does not enforce NetworkPolicies, but default K3s ships an **embedded network policy controller** (kube-router's netpol library) that turns NetworkPolicy objects into iptables/ipset rules on each node. It is on unless K3s is started with `--disable-network-policy`.

---

# 🔬 Root Cause Analysis

A Kubernetes NetworkPolicy defines traffic rules but **does not enforce them by itself**.

Enforcement is done by a policy engine: either the CNI plugin itself (Calico, Cilium, Antrea, OVN-Kubernetes) or a separate controller next to a CNI that has none.

This lab uses default K3s: Flannel for pod networking plus the embedded kube-router network policy controller for enforcement. The `deny-all-ingress` policy selects `app: nginx` and allows no ingress, so traffic from BusyBox to NGINX is rejected. On a cluster with plain Flannel and no policy controller, the same policy would be accepted by the API server but silently ignored.

---

# 🔄 NetworkPolicy Workflow

```
BusyBox Pod
      │
      ▼
NGINX Service
      │
      ▼
NetworkPolicy Evaluated
      │
      ▼
Policy engine (CNI or separate controller,
on K3s: embedded kube-router netpol)
      │
      ├────────► Engine present
      │               │
      │               ▼
      │         Traffic Allowed / Denied
      │
      └────────► No engine (e.g. plain Flannel)
                      │
                      ▼
               Policy ignored, traffic continues
```

---

# 🔍 Investigation Commands

Check NetworkPolicies:

```bash
kubectl get networkpolicy
```

Describe the policy:

```bash
kubectl describe networkpolicy deny-all-ingress
```

Verify Pod labels:

```bash
kubectl get pods --show-labels
```

Test connectivity:

```bash
kubectl exec busybox -- wget -qO- http://nginx
```

---

# 🛠️ Cleanup

Delete the NetworkPolicy:

```bash
kubectl delete networkpolicy deny-all-ingress
```

Verify:

```bash
kubectl get networkpolicy
```

---

# 📸 Screenshots

| Screenshot | Description |
|------------|-------------|
| 01-connectivity-before-policy.png | Successful communication before policy |
| 02-network-policy-created.png | NetworkPolicy creation |
| 03-connectivity-test.png | Connectivity test after policy |
| 04-describe-network-policy.png | NetworkPolicy details |
| 05-cleanup.png | NetworkPolicy removed |

---

# 💡 Production Troubleshooting Workflow

```
Communication Failure
        │
        ▼
Verify Pods
        │
        ▼
Verify Services
        │
        ▼
Check NetworkPolicies
        │
        ▼
Describe Policy
        │
        ▼
Verify Pod Labels
        │
        ▼
Check CNI Support
        │
        ▼
Identify Root Cause
```

---

# 🎯 Key Learning Outcomes

- Kubernetes NetworkPolicies define traffic rules.
- NetworkPolicy enforcement depends on the installed CNI plugin.
- Pod labels determine which workloads are affected.
- Traffic should always be verified before and after policy changes.
- Default K3s **does** enforce NetworkPolicies: Flannel carries the traffic and the embedded kube-router controller enforces the rules. Plain Flannel on its own would not.

---

# 🚀 Production Best Practices

- Use NetworkPolicies to implement a Zero Trust networking model.
- Apply the principle of least privilege for Pod communication.
- Validate Pod labels before creating policies.
- Verify CNI compatibility before deploying NetworkPolicies.
- Test policies in non-production environments before rollout.
- Document all ingress and egress rules for operational visibility.

---

# 🎤 Interview Questions

### Q1. What is a Kubernetes NetworkPolicy?

A NetworkPolicy defines how Pods are allowed to communicate with other Pods and network endpoints.

---

### Q2. Which Kubernetes component enforces a NetworkPolicy?

A **policy engine** on each node: usually the CNI plugin (Calico, Cilium, Antrea, OVN-Kubernetes), or a separate controller when the CNI has none. On K3s that is the embedded kube-router network policy controller.

---

### Q3. Does Kubernetes enforce NetworkPolicies by default?

No. Kubernetes provides the NetworkPolicy API, but enforcement depends on a compatible CNI plugin.

---

### Q4. Does default K3s (with Flannel) enforce NetworkPolicies?

Yes. Flannel alone does not, but K3s adds an embedded network policy controller based on kube-router, enabled by default (`--disable-network-policy` turns it off, for example when installing Calico or Cilium). This lab proved it: after the policy was applied, `wget` from BusyBox to NGINX was refused.

---

### Q5. Which CNI plugins support NetworkPolicies?

Examples include:

- Calico
- Cilium
- Antrea
- Weave Net (with policy support)

---

### Q6. How do you troubleshoot a NetworkPolicy issue?

1. Verify Pod status.
2. Verify Services.
3. Check Pod labels.
4. List NetworkPolicies.
5. Describe the policy.
6. Verify CNI capabilities.
7. Test connectivity before and after policy changes.

---

# 🏁 Conclusion

This lab demonstrated how Kubernetes NetworkPolicies control Pod communication and emphasized that policy enforcement depends on the cluster's CNI plugin.

Understanding NetworkPolicies, Pod selectors, and CNI behavior is an essential skill for Platform Engineers, Kubernetes Administrators, OpenShift Engineers, and Site Reliability Engineers managing secure production Kubernetes environments.
