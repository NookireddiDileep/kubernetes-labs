# ☸️ Kubernetes Labs

**Hands-on Kubernetes engineering: build, break, troubleshoot and document real clusters, workloads, networking, storage and security. From foundations to production-grade operations.**

Every lab is done on a cluster I built and run myself, with manifests, validation screenshots and written observations. Failures are part of the lab: each troubleshooting scenario is reproduced, diagnosed and fixed.

| | |
|---|---|
| **Environment** | Single-node **K3s** on an Ubuntu 24.04 VM in Google Cloud (containerd runtime) |
| **Built by** | Nookireddi Dileep, senior Linux/HPC infrastructure engineer moving into Kubernetes/OpenShift platform engineering |
| **Status** | Module 01 ✅ complete · next modules planned |

> **Scope, honestly:** a single-node lab cluster. Multi-node behaviour (scheduling across nodes, node failure, real HA) is practised on multi-node OpenShift clusters in my [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records) repo.

---

## Modules

| # | Module | What's inside | Status |
|---|---|---|---|
| 00 | [Environment setup](00-environment-setup/README.md) | GCP VM, Linux preparation, Git/SSH, K3s installation, setup troubleshooting | ✅ |
| 01 | [Kubernetes foundation](01-kubernetes-foundation/README.md) | 13 hands-on labs + 9 production troubleshooting scenarios | ✅ |
| 02 | Helm package management | Charts, values, templating, releases and rollbacks | ⏳ Planned |
| 03 | Cluster internals & CKA track | kubeadm multi-node cluster, control plane, etcd backup/restore, upgrades | ⏳ Planned |
| 04 | Networking deep dive | CNI, Services/kube-proxy, DNS, NetworkPolicies, Ingress/Gateway API | ⏳ Planned |

## Module 01: Kubernetes foundation (labs)

| Lab | Topic |
|---|---|
| [01](01-kubernetes-foundation/labs/lab-01-first-deployment/README.md) | First Deployment: ReplicaSets, self-healing |
| [02](01-kubernetes-foundation/labs/lab-02-services/README.md) | Services: ClusterIP, NodePort, endpoints |
| [03](01-kubernetes-foundation/labs/lab-03-configmaps/README.md) | ConfigMaps |
| [04](01-kubernetes-foundation/labs/lab-04-secrets/README.md) | Secrets |
| [05](01-kubernetes-foundation/labs/lab-05-persistent-volumes/README.md) | Persistent storage: static and dynamic PV/PVC |
| [06](01-kubernetes-foundation/labs/lab-06-statefulsets/README.md) | StatefulSets |
| [07](01-kubernetes-foundation/labs/lab-07-resource-management/README.md) | Resource requests & limits |
| [08](01-kubernetes-foundation/labs/lab-08-health-checks/README.md) | Health checks: liveness, readiness, startup probes |
| [09](01-kubernetes-foundation/labs/lab-09-jobs-cronjobs/README.md) | Jobs & CronJobs |
| [10](01-kubernetes-foundation/labs/lab-10-rbac/README.md) | RBAC: ServiceAccount, Role, RoleBinding, `auth can-i` |
| [11](01-kubernetes-foundation/labs/lab-11-namespaces/README.md) | Namespaces & ResourceQuotas |
| [12](01-kubernetes-foundation/labs/lab-12-ingress/README.md) | Ingress |
| [13](01-kubernetes-foundation/labs/lab-13-production-troubleshooting/README.md) | **Production troubleshooting:** CrashLoopBackOff, ImagePullBackOff, Pending pods, ConfigMap/Secret errors, probe failures, PV issues, NetworkPolicy, resource limits |

## How each lab is documented

`README.md` (objective, steps, validation) · manifests (`*.yaml`) · `screenshots/` (evidence) · `observations.md` (what I saw and learned)

## Related repositories

- [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records): OpenShift labs, Jira-style stories and RCAs on multi-node clusters
- [infra-automation-lab](https://github.com/NookireddiDileep/infra-automation-lab): Bash, Ansible, Python and Go automation
- [project-arohan-ai](https://github.com/NookireddiDileep/project-arohan-ai): AI infrastructure track
