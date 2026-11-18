# Changelog

All notable changes to the **Kubernetes Foundation** module are documented in this file.

This changelog follows the principles of semantic versioning and documents major learning milestones, documentation improvements, and production-focused enhancements.

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

The next modules in the roadmap are **02 – Incident Drills** (ongoing) and then:

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
