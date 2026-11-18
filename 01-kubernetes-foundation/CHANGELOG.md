# Changelog

All notable changes to the **Kubernetes Foundation** module are documented in this file.

This changelog follows the principles of semantic versioning and documents major learning milestones, documentation improvements, and production-focused enhancements.

---

## [v1.0.3] - 2026-11-18

### 🎤 Interview answers and file listings

- Every interview question in labs 01–12 and Lab 13 scenarios 01–02 now has a short answer (139 answers), tied to what the lab showed where possible.
- Folder diagrams in labs 02, 03, 05 and scenarios 01–03 regenerated from the real files.
- Lab 02 now reuses Lab 01's `nginx.yaml` (the Deployment its Service selects) instead of a `deployment.yaml` that was never in the repo.
- Screenshot index pages in labs 03 and 05–11 were planning templates naming files that don't exist; they now link the actual screenshots.

---

## [v1.0.2] - 2026-11-18

### 🔍 End-to-end technical review

- **Lab 01:** removed a duplicated older copy of `observations.md`.
- **Lab 07:** QoS table now gives the real rules for Burstable and Guaranteed.
- **Lab 08:** a failing startup probe restarts the container; it doesn't just "delay liveness".
- **Lab 11:** quota troubleshooting corrected: when a quota blocks a Deployment, no Pods are created (`FailedCreate` on the ReplicaSet), rather than Pods staying Pending.
- **Lab 12:** "external access" was a `curl` to the node's private IP from the VM; wording and the ServiceLB role now say so.
- **Lab 13:** scenario 02 no longer claims a Pod's image can't be changed in place; scenarios 04/05 explain the restart is optional; scenario 04's imperative/declarative examples now use a ConfigMap (they were Secrets); scenario 09 explains exit code 137 as SIGKILL and notes that `oom-demo.yaml` is the fixed manifest; scenario 07 notes `local-path` data doesn't survive node loss; screenshot sections in scenarios 01–07 now link the real files; Kubernetes version pinned to `v1.36.2+k3s1`.
- **Interview cheat sheet:** gRPC probes, `WaitForFirstConsumer`, K3s NetworkPolicy enforcement, exit code 137.
- **theory/:** request flow now shows authn → authz → admission → datastore → controllers → scheduler → kubelet; components list fixed for K3s (SQLite, not etcd, on a single server); removed the scaffolding file `Module_Structure.md`.
- **ARCHITECTURE.md:** new "How K3s differs" table; admission order corrected; NetworkPolicy note corrected.
- **Module 02:** status changed from "ongoing" to "set up, no drills logged yet", here and in the repo README.

---

## [v1.0.1] - 2026-11-18

### 📝 Documentation fixes

- Module README rewritten: lab table and folder list now match the real labs (08 Health Checks, 12 Ingress; Multi-Container Pods and Init Containers were never part of this module and are removed), every lab and scenario linked, exact environment and evidence counts, findings with screenshot links.
- Corrected the v1.0.0 lab list below to match the labs actually delivered.
- Scenario 08 (NetworkPolicy): corrected the claim that default K3s does not enforce NetworkPolicies. K3s ships an embedded kube-router network policy controller, and the lab's own screenshot shows traffic blocked (`Connection refused`). Lab 13 observations updated to match.
- Next module corrected: 02 is Incident Drills, Helm is 03.

---

## [v1.0.0] - 2026-07-26

### 🎉 Initial Release

The Kubernetes Foundation module has been completed.

---

### ✨ Labs Added

- ✅ Lab 01 – Kubernetes First Deployment
- ✅ Lab 02 – Services
- ✅ Lab 03 – ConfigMaps
- ✅ Lab 04 – Secrets
- ✅ Lab 05 – Persistent Volumes & Persistent Volume Claims
- ✅ Lab 06 – StatefulSets
- ✅ Lab 07 – Resource Requests & Limits
- ✅ Lab 08 – Health Checks (liveness, readiness, startup probes)
- ✅ Lab 09 – Jobs & CronJobs
- ✅ Lab 10 – RBAC
- ✅ Lab 11 – Namespaces & Resource Quotas
- ✅ Lab 12 – Ingress
- ✅ Lab 13 – Production Troubleshooting

---

### 🚨 Production Troubleshooting

Added nine production-oriented troubleshooting scenarios:

- CrashLoopBackOff
- ImagePullBackOff
- Pending Pods
- ConfigMap Issues
- Secret Issues
- Liveness & Readiness Probe Failures
- Persistent Volume Issues
- NetworkPolicy Analysis
- Resource Limits & OOMKilled

---

### 📚 Documentation

Added comprehensive documentation including:

- Module README
- Production-focused lab READMEs
- Kubernetes Architecture Guide
- Troubleshooting Observations
- Interview Cheatsheet
- Best Practices
- Architecture Diagrams
- Production Workflows

---

### 🛠️ Technologies Covered

- Kubernetes
- K3s
- containerd
- Pods
- ReplicaSets
- Deployments
- Services
- ConfigMaps
- Secrets
- StatefulSets
- Persistent Volumes
- Persistent Volume Claims
- StorageClasses
- Jobs
- CronJobs
- Health Probes
- Ingress (Traefik)
- ResourceQuotas & LimitRanges
- Namespaces
- RBAC
- Resource Requests & Limits
- Kubernetes Events
- Container Logs
- NetworkPolicies
- Production Troubleshooting

---

### 🎯 Skills Acquired

- Kubernetes Fundamentals
- Kubernetes Architecture
- Workload Management
- Networking
- Configuration Management
- Secret Management
- Persistent Storage
- Resource Optimization
- Security Fundamentals
- Production Troubleshooting
- Root Cause Analysis (RCA)
- Platform Engineering Practices

---

### 🎤 Interview Preparation

Prepared documentation covering:

- Kubernetes Architecture
- Workload Management
- Networking
- Storage
- Security
- Resource Management
- Troubleshooting
- Operational Best Practices
- Production Incident Analysis

---

### 📈 Repository Statistics

| Metric | Value |
|---------|------:|
| Labs | **13** |
| Troubleshooting Scenarios | **9** |
| Architecture Documents | **1** |
| Interview Guides | **1** |
| README Files | **15+** |
| Screenshots | **128** |
| Kubernetes Concepts Covered | **20+** |

---

### 🚀 Next Milestone

The next modules in the roadmap are **02 – Incident Drills** (set up, no drills logged yet) and then:

**03 – Helm Package Management**

Upcoming topics include:

- Helm Architecture
- Chart Structure
- Chart.yaml
- Values Files
- Go Templates
- Built-in Functions
- Helper Templates
- Subcharts
- Chart Dependencies
- OCI Registry
- Helm Production Best Practices
- Helm Troubleshooting

---

## Status

✅ **Kubernetes Foundation Module Completed**

**Version:** v1.0.0

**State:** Complete

**Next Target:** Helm Package Management
