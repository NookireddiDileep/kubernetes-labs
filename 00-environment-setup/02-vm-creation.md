# 02 — VM Creation, Access and Firewall

## Objective

One Ubuntu VM sized to run K3s plus every lab workload in this repo.

## VM specification

| Parameter | Value | Why |
|---|---|---|
| Name | `platform-engineer-lab` | Node name in the cluster |
| Machine type | `e2-standard-4` (4 vCPU, 16 GB) | Room for K3s, system add-ons and lab workloads, including OOM and quota tests |
| Image | Ubuntu 24.04 LTS (`ubuntu-2404-lts-amd64`) | Long-term support; K3s supports it out of the box |
| Boot disk | 100 GB | Container images, local-path volumes and logs |
| Region | `asia-south1` (Mumbai) | Closest to me, lower latency |

## Create the VM

```bash
gcloud compute instances create platform-engineer-lab \
  --zone=<zone> \
  --machine-type=e2-standard-4 \
  --image-family=ubuntu-2404-lts-amd64 \
  --image-project=ubuntu-os-cloud \
  --boot-disk-size=100GB
```

## Access

```bash
gcloud compute ssh platform-engineer-lab --zone=<zone>
# or, with no public SSH exposure, through Identity-Aware Proxy:
gcloud compute ssh platform-engineer-lab --zone=<zone> --tunnel-through-iap
```

## Firewall

| Port | Open to the internet? | Reason |
|---|---|---|
| 22 (SSH) | Only from IAP (`35.235.240.0/20`) or my IP | Admin access |
| 6443 (Kubernetes API) | **No** | `kubectl` runs on the VM |
| 30000–32767 (NodePorts) | **No** | Test with `curl` from the VM or `kubectl port-forward` |
| 80/443 (Traefik) | **No** by default | Open only for a specific Ingress test, then close |

```bash
gcloud compute firewall-rules list     # check nothing beyond SSH is open
```

## Verification

```bash
hostnamectl          # hostname, OS, kernel
nproc && free -h     # 4 CPUs, ~16 GB
df -h /              # ~100 GB root disk
ip -br addr          # private IP on the default VPC
```

## Cost control

- Stop the VM after each session: `gcloud compute instances stop platform-engineer-lab --zone=<zone>`. Billing for vCPU/RAM stops; only the disk is charged.
- The persistent disk keeps everything. K3s runs as a systemd service and starts on boot.
- The ephemeral external IP can change after a stop/start. That's fine here because nothing connects to the API from outside the VM.
