# Kubernetes Components

## Control Plane

- API Server
- Scheduler
- Controller Manager
- Embedded etcd (K3s)

---

## Worker Node

- kubelet
- kube-proxy (implementation may vary in K3s)
- containerd

---

## Add-ons Installed

- CoreDNS
- Metrics Server
- Traefik
- Local Path Provisioner

---

## Verification

```bash
kubectl get pods -A
```
