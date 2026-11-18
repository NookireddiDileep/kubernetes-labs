# 05 — K3s Installation, kubeconfig and Validation

## Objective

Install a single-node Kubernetes cluster on the VM, run `kubectl` as a normal user, and prove the cluster is healthy before any lab starts.

> The original cluster ran from July to November 2026 and has been deleted. The **Validation** result below is from that cluster; everything else is the guide to rebuild it.

## Why K3s?

| Need | How K3s meets it |
|---|---|
| One small VM | A single binary; control plane and workloads on one node |
| Real Kubernetes | CNCF-certified: same APIs, `kubectl` and manifests as any cluster |
| Batteries included | containerd, Flannel CNI, CoreDNS, Traefik ingress, ServiceLB, local-path storage, metrics-server and a network policy controller, all installed by default |
| Easy rebuild | One install command, one uninstall script |

**Trade-off:** a single node can't show multi-node scheduling, node failure or control-plane HA. Those are the focus of [module 02](../02-incident-drills/README.md) (starting) and are practised on OpenShift in [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records).

## Install (pinned to the version this cluster runs)

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.36.2+k3s1 sh -
```

This installs the `k3s` binary, the `kubectl`/`crictl` symlinks and a `k3s` systemd service, then starts the server. Pinning the version means a rebuild gives the same cluster the labs were run on.

```bash
sudo systemctl status k3s --no-pager      # active (running), enabled on boot
k3s --version
```

## kubeconfig for a normal user

K3s writes the admin kubeconfig to `/etc/rancher/k3s/k3s.yaml`, readable by root only. Copy it for your own user instead of making it world-readable:

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown "$(id -u):$(id -g)" ~/.kube/config
chmod 600 ~/.kube/config
echo 'export KUBECONFIG=~/.kube/config' >> ~/.bashrc && source ~/.bashrc
```

The file holds cluster-admin credentials. It lives outside the repo, and the repo's `.gitignore` also blocks `kubeconfig*` and `*.kubeconfig` files as a safety net: never copy it in.

## Validation

```bash
kubectl get nodes -o wide        # node Ready, version v1.36.2+k3s1
kubectl get pods -A              # all kube-system pods Running or Completed
kubectl get storageclass         # local-path (default)
kubectl top nodes                # metrics-server answers
```

Result on the original cluster: node `platform-engineer-lab` **Ready**, role `control-plane`, `v1.36.2+k3s1`, Ubuntu 24.04.4 LTS, kernel `6.17.0-1021-gcp`, containerd `2.3.2-k3s2`. [Screenshot](../01-kubernetes-foundation/labs/lab-01-first-deployment/screenshots/01-cluster-nodes.png)

Expected `kube-system` pods on a default install: `coredns`, `local-path-provisioner`, `metrics-server`, `traefik`, `svclb-traefik-*`, and the completed `helm-install-traefik*` jobs.

## Where things live

| What | Path / command |
|---|---|
| Service | `systemctl status k3s`, logs: `journalctl -u k3s -f` |
| Admin kubeconfig | `/etc/rancher/k3s/k3s.yaml` |
| Optional config file | `/etc/rancher/k3s/config.yaml` |
| Data, images, datastore | `/var/lib/rancher/k3s/` |
| Auto-deployed manifests | `/var/lib/rancher/k3s/server/manifests/` |
| local-path volumes | `/var/lib/rancher/k3s/storage/` |
| Container runtime CLI | `sudo k3s crictl ps` |
| Uninstall | `/usr/local/bin/k3s-uninstall.sh` |

## Interview questions

**How is K3s different from kubeadm Kubernetes?**
Same Kubernetes APIs, packaged as one binary that runs all control-plane components in a single process and bundles containerd, CNI, DNS, ingress, storage and metrics. A single server uses an embedded SQLite datastore by default; embedded etcd is used for HA (`--cluster-init`). kubeadm gives you separate static-pod components and leaves CNI, ingress and storage to you.

**Where is etcd in this cluster?**
There isn't one: a single K3s server stores cluster state in SQLite under `/var/lib/rancher/k3s/server/db/`. Embedded etcd is turned on with `--cluster-init` for multi-server HA.

**Why can't a normal user run `kubectl` right after install?**
`/etc/rancher/k3s/k3s.yaml` is root-only. Copy it to `~/.kube/config` with your ownership (or use `--write-kubeconfig-mode`, which is less safe because it widens access to admin credentials).

**What happens to the cluster when the VM is stopped and started?**
K3s is a systemd service, so it starts on boot; state and local-path volumes are on the persistent disk. Pods are recreated by their controllers. Bare pods without a controller are not restarted.
