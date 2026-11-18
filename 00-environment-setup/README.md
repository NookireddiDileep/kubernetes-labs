# Module 00 — Environment Setup

How the lab cluster behind modules 00–01 was built: a single-node **K3s** cluster on an Ubuntu VM in Google Cloud. These pages are written as a **rebuild guide**, so anyone, including future me, can recreate it.

> **Status: retired (Nov 2026).** The VM was deleted after module 01 was finished. The facts below come from the cluster while it ran (see the evidence link); the commands rebuild the same setup.

## The cluster

| | |
|---|---|
| **Cloud** | Google Cloud, region `asia-south1` (Mumbai, from the node's subnet) |
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
| 06 | Tear-down | ✅ VM deleted, Nov 2026 |
| — | [Troubleshooting log](troubleshooting.md) | Ongoing |

Helm is installed in module 03, not here.

## Security and cost baseline

- **Access:** `kubectl` ran on the VM itself over SSH, so the Kubernetes API (`6443`) and NodePorts never needed to be exposed. For a rebuild, keep it that way (see [02](02-vm-creation.md#firewall)).
- **Cost:** the VM was stopped between sessions (only the disk is billed while stopped) and deleted when the module was done, which stops all charges.

## Not recorded before tear-down

The exact zone, boot-disk type and a `kubectl get pods -A` capture were not saved before the VM was deleted. The rebuild commands use placeholders for these. On the next rebuild, record them here.
