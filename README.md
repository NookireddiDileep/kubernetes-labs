# ☸️ Kubernetes Labs

**Hands-on Kubernetes engineering: build, break, troubleshoot and document real clusters, workloads, networking, storage and security. From foundations to production-grade operations.**

Every lab is done on a cluster I built and run myself, with manifests, validation screenshots and written observations. Failures are part of the lab: each troubleshooting scenario is reproduced, diagnosed and fixed.

| | |
|---|---|
| **Environment** | Single-node **K3s** on an Ubuntu 24.04 VM in Google Cloud (containerd runtime); VM retired Nov 2026 after module 01, [rebuild guide](00-environment-setup/README.md) kept |
| **Built by** | Nookireddi Dileep, senior Linux/HPC infrastructure engineer moving into Kubernetes/OpenShift platform engineering |
| **Status** | Module 01 ✅ complete · Module 02 (incident drills) 🟡 set up, first drill not yet logged · more planned |

> **Scope, honestly:** modules 00–01 run on a single-node lab cluster. Multi-node behaviour is the focus of the live-outage drills (module 02, starting) and is practised on multi-node OpenShift clusters in my [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records) repo.

---

## Modules

| # | Module | What's inside | Status |
|---|---|---|---|
| 00 | [Environment setup](00-environment-setup/README.md) | GCP VM, Linux preparation, Git/SSH, K3s installation, tear-down; written as a rebuild guide | ✅ VM retired |
| 01 | [Kubernetes foundation](01-kubernetes-foundation/README.md) | 13 hands-on labs + 9 production troubleshooting scenarios | ✅ |
| 02 | [Incident drills](02-incident-drills/README.md) | Timed live-outage drills on multi-node clusters, with my own RCAs | 🟡 Method and template ready, no drills logged yet |
| 03 | Helm package management | Charts, values, templating, releases and rollbacks | ⏳ Planned |
| 04 | Cluster internals & CKA track | kubeadm multi-node cluster, control plane, etcd backup/restore, upgrades | ⏳ Planned |
| 05 | Networking deep dive | CNI, Services/kube-proxy, DNS, NetworkPolicies, Ingress/Gateway API | ⏳ Planned |

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

Part of one hands-on platform-engineering portfolio. Start at my [GitHub profile](https://github.com/NookireddiDileep) for the overview.

| Repo | What's there |
|---|---|
| [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records) | OpenShift 4.x on multi-node clusters: Jira-style stories, RCAs, break-fix runbooks, `oc` command bank, OpenShift vs Kubernetes guide |
| [infra-automation-lab](https://github.com/NookireddiDileep/infra-automation-lab) | One real ops scenario a day, solved in Bash, Ansible, Python and Go against the same golden tests |
| [project-arohan-ai](https://github.com/NookireddiDileep/project-arohan-ai) | AI infrastructure track: Linux internals, OpenShift vs Kubernetes, GPU scheduling, inference reliability, Prometheus SLOs |
