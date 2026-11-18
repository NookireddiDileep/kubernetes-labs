# 04 — Linux Preparation and Git Identity

## Objective

Update the OS and install the tools used across the labs.

## Packages

```bash
sudo apt update && sudo apt -y upgrade
sudo apt install -y git curl wget jq vim tree htop dnsutils net-tools
```

| Package | Used for |
|---|---|
| `git` | Version control for this repo |
| `curl`, `wget` | Installing K3s; testing Services and Ingress |
| `jq` | Reading `kubectl -o json` output |
| `vim`, `tree`, `htop` | Editing manifests, viewing lab folders, watching load |
| `dnsutils` (`dig`, `nslookup`) | DNS checks from the node |
| `net-tools` (`netstat`) | Port checks (`ss` from iproute2 also works) |

## Git identity

```bash
git config --global user.name "Dileep Nookireddi"
git config --global user.email "<github-email>"
git config --global init.defaultBranch main
```

## Verification

```bash
git config --global --list
jq --version && dig -v
```

## Notes

- Ubuntu cloud images on GCP have no swap configured, which is what Kubernetes expects.
- Reboot once after `apt upgrade` if a new kernel was installed (`/var/run/reboot-required` exists).
