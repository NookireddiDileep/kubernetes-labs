# Kubernetes Components

## Control Plane

- API Server
- Scheduler
- Controller Manager
- Datastore: SQLite via kine (single-server K3s default; embedded etcd only with `--cluster-init` for HA)

---

## Worker Node

- kubelet
- kube-proxy (runs inside the `k3s` process, not as a separate Pod)
- containerd

---

## Add-ons Installed

- CoreDNS
- Metrics Server
- Traefik
- Local Path Provisioner
- ServiceLB (klipper-lb)
- Network policy controller (kube-router based)
- Flannel CNI (built into the `k3s` process)

---

## Verification

```bash
kubectl get pods -A
```
