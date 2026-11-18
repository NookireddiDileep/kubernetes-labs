# Troubleshooting Log — Environment Setup

Real problems hit while building this environment, in the order they happened. Each entry: symptom → root cause → fix.

## Issue 1: GitHub SSH authentication failed

| | |
|---|---|
| **Symptom** | `ssh -T git@github.com` → `Permission denied (publickey).` |
| **Root cause** | The public key had not been added to the GitHub account. |
| **Fix** | Added the SSH public key under GitHub → Settings → SSH and GPG keys; `ssh -T` then authenticated. |
| **Status** | ✅ Resolved |

## Issue 2: Git commit failed

| | |
|---|---|
| **Symptom** | `git commit` → `Author identity unknown` |
| **Root cause** | `user.name` and `user.email` were not set. |
| **Fix** | `git config --global user.name ...` and `user.email ...` ([04](04-linux-preparation.md#git-identity)). |
| **Status** | ✅ Resolved |

---

## Quick checks if the cluster misbehaves

Not incidents I hit, but the first commands to run on this setup:

| Symptom | Check | Usual cause |
|---|---|---|
| `kubectl`: `permission denied` on `k3s.yaml` | `ls -l /etc/rancher/k3s/k3s.yaml` | kubeconfig not copied for the user ([05](05-k3s-installation.md#kubeconfig-for-a-normal-user)) |
| `kubectl`: `connection refused` on `127.0.0.1:6443` | `systemctl status k3s`, `journalctl -u k3s -e` | K3s service not running (e.g. right after VM start, or failed start) |
| `kubectl top` → `Metrics API not available` | `kubectl -n kube-system get pods -l k8s-app=metrics-server` | metrics-server still starting; wait a minute after boot |
| PVC stays `Pending` | `kubectl describe pvc <name>` | `local-path` binds on first consumer; create the pod first |
| Node `NotReady` / pods evicted | `kubectl describe node`, `df -h` | Disk pressure on the 100 GB disk; prune images with `sudo k3s crictl rmi --prune` |
