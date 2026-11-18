# Troubleshooting

## Issue

kubectl command not found

### Resolution

Configure kubeconfig and install kubectl.

---

## Issue

Node NotReady

### Checks

- systemctl status k3s
- kubectl get nodes
- journalctl -u k3s

---

## Issue

Pod CrashLoopBackOff

### Checks

- kubectl logs
- kubectl describe pod
