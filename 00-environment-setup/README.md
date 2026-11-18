# Module 00 — Environment Setup

How the lab cluster behind every module in this repo was built: a single-node **K3s** cluster on an Ubuntu VM in Google Cloud. The goal is that anyone, including future me, can rebuild it from these pages.

## The cluster

| | |
|---|---|
| **Cloud** | Google Cloud, region `asia-south1` (Mumbai) |
| **VM** | `platform-engineer-lab`, `e2-standard-4` (4 vCPU, 16 GB RAM), 100 GB disk |
| **OS** | Ubuntu 24.04.4 LTS, kernel `6.17.0-1021-gcp` |
| **Kubernetes** | K3s `v1.36.2+k3s1`, single server node (runs the control plane and workloads) |
| **Runtime** | containerd `2.3.2-k3s2` (bundled with K3s) |
| **Bundled add-ons** | Flannel CNI, CoreDNS, Traefik ingress, ServiceLB, local-path storage, metrics-server, network policy controller |

**Evidence:** `kubectl get nodes -o wide` showing the node `Ready` on this version: [screenshot](../01-kubernetes-foundation/labs/lab-01-first-deployment/screenshots/01-cluster-nodes.png).

## Steps

| # | Step | Status |
|---|---|---|
| 01 | [GCP project, billing and budget](01-gcp-project.md) | ✅ |
| 02 | [VM creation, access and firewall](02-vm-creation.md) | ✅ |
| 03 | [GitHub SSH access](03-github-ssh.md) | ✅ |
| 04 | [Linux preparation and Git identity](04-linux-preparation.md) | ✅ |
| 05 | [K3s installation, kubeconfig and validation](05-k3s-installation.md) | ✅ Cluster built and validated |
| — | [Troubleshooting log](troubleshooting.md) | Ongoing |

Helm is installed in module 03, not here.

## Security and cost baseline

- **Access:** SSH only (ideally via IAP). The Kubernetes API (`6443`) and NodePorts (`30000–32767`) are **not** opened to the internet; `kubectl` runs on the VM.
- **Cost:** the VM is stopped after every session. The persistent disk keeps the cluster, so K3s comes back on the next start. A budget alert guards against surprises.

## Still to add

- [ ] Exact zone (e.g. `asia-south1-a`) and boot-disk type
- [ ] Screenshot of `kubectl get pods -A` right after install (all system pods `Running`)
- [ ] Any K3s problems hit during setup, in [troubleshooting.md](troubleshooting.md)
