# ☸️ Kubernetes Foundation

> **A comprehensive, production-oriented Kubernetes learning path covering core concepts, workload management, networking, storage, security, resource optimization, and real-world troubleshooting for Platform Engineers.**

---

# 📖 Overview

The **Kubernetes Foundation** module is the first phase of the **Platform Engineer Lab 2026** roadmap. It provides a structured learning journey from Kubernetes fundamentals to production troubleshooting through hands-on labs designed to mirror real-world engineering scenarios.

Rather than focusing only on deployments, this module emphasizes understanding **how Kubernetes works internally, how applications are operated in production, and how failures are investigated and resolved**.

Each lab includes:

- 📘 Detailed README documentation
- 🛠️ Hands-on implementation
- 📸 Validation screenshots
- 🔍 Observations and key learnings
- 💡 Production best practices
- 🎤 Interview preparation
- 🚨 Troubleshooting guidance

---

# 📊 Module Progress

| Metric | Value |
|---------|------:|
| Total Labs | **13** |
| Production Troubleshooting Scenarios | **9** |
| Kubernetes Concepts Covered | **20+** |
| Interview Questions | **50+** |
| Documentation Files | **20+** |
| Status | **✅ Completed** |

---

# 🎯 Learning Objectives

After completing this module, you will be able to:

- Understand Kubernetes architecture and control plane components
- Deploy and manage containerized applications
- Work with Pods, ReplicaSets, Deployments, and Services
- Configure applications using ConfigMaps and Secrets
- Manage persistent storage using PVs and PVCs
- Deploy stateful workloads with StatefulSets
- Organize workloads using Namespaces
- Understand RBAC fundamentals
- Configure Resource Requests and Limits
- Diagnose and troubleshoot production issues
- Apply a structured Root Cause Analysis (RCA) methodology
- Prepare for Kubernetes, OpenShift, and Platform Engineering interviews

---

# 🏗️ Lab Environment

| Component | Details |
|------------|---------|
| Kubernetes Distribution | K3s |
| Kubernetes Version | v1.36.x |
| Operating System | Ubuntu 24.04 LTS |
| Container Runtime | containerd |
| Cloud Platform | Google Cloud Platform (GCP) |
| Cluster Type | Single Node Control Plane |

---

# 📚 Labs Covered

| Lab | Topic | Status |
|-----|-------|:------:|
| Lab 01 | Kubernetes First Deployment | ✅ |
| Lab 02 | Services | ✅ |
| Lab 03 | ConfigMaps | ✅ |
| Lab 04 | Secrets | ✅ |
| Lab 05 | Persistent Volumes & Persistent Volume Claims | ✅ |
| Lab 06 | StatefulSets | ✅ |
| Lab 07 | Resource Requests & Limits | ✅ |
| Lab 08 | Jobs & CronJobs | ✅ |
| Lab 09 | Multi-Container Pods | ✅ |
| Lab 10 | Init Containers | ✅ |
| Lab 11 | Namespaces | ✅ |
| Lab 12 | RBAC | ✅ |
| Lab 13 | Production Troubleshooting | ✅ |

---

# 🚨 Production Troubleshooting (Lab 13)

The final lab focuses on diagnosing and resolving common Kubernetes production failures using a structured troubleshooting methodology similar to real-world Platform Engineering and SRE environments.

### Scenarios Covered

| Scenario | Description |
|----------|-------------|
| 01 | CrashLoopBackOff |
| 02 | ImagePullBackOff |
| 03 | Pending Pod |
| 04 | ConfigMap Issues |
| 05 | Secret Issues |
| 06 | Liveness & Readiness Probe Failures |
| 07 | Persistent Volume Issues |
| 08 | NetworkPolicy Analysis |
| 09 | Resource Limits & OOMKilled |

---

# 🔍 Production Troubleshooting Workflow

Every troubleshooting scenario follows the same engineering methodology.

```text
Issue Detected
      │
      ▼
Observe Symptoms
      │
      ▼
Collect Evidence
      │
      ▼
Analyze Events & Logs
      │
      ▼
Identify Root Cause
      │
      ▼
Apply Resolution
      │
      ▼
Validate Recovery
      │
      ▼
Document Findings
```

---

# 🛠️ Kubernetes Concepts Covered

## Core Architecture

- Kubernetes Architecture
- API Server
- etcd
- Scheduler
- Controller Manager
- kubelet
- containerd Runtime

## Workloads

- Pods
- ReplicaSets
- Deployments
- StatefulSets
- Jobs
- CronJobs
- Init Containers
- Multi-Container Pods

## Networking

- Services
- DNS
- NetworkPolicies

## Configuration

- ConfigMaps
- Secrets

## Storage

- Persistent Volumes (PV)
- Persistent Volume Claims (PVC)
- StorageClasses

## Security

- Namespaces
- RBAC

## Operations

- Resource Requests & Limits
- Kubernetes Events
- Container Logs
- Production Troubleshooting
- Root Cause Analysis (RCA)

---

# 📂 Repository Structure

```text
01-kubernetes-foundation/
│
├── README.md
└── labs/
    ├── lab-01-first-deployment/
    ├── lab-02-services/
    ├── lab-03-configmaps/
    ├── lab-04-secrets/
    ├── lab-05-persistent-volumes/
    ├── lab-06-statefulsets/
    ├── lab-07-resource-management/
    ├── lab-08-jobs-cronjobs/
    ├── lab-09-multi-container-pods/
    ├── lab-10-init-containers/
    ├── lab-11-namespaces/
    ├── lab-12-rbac/
    └── lab-13-production-troubleshooting/
```

---

# 💻 Frequently Used Commands

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>

kubectl get deployments
kubectl get services

kubectl get configmaps
kubectl get secrets

kubectl get pv
kubectl get pvc

kubectl get namespaces

kubectl get events --sort-by=.metadata.creationTimestamp

kubectl top pods
kubectl top nodes
```

---

# 🎯 Skills Developed

After completing this module, you will have practical experience with:

- Kubernetes architecture
- Container orchestration
- Application deployments
- Service discovery
- Configuration management
- Secret management
- Persistent storage
- Stateful workloads
- Namespace isolation
- RBAC fundamentals
- Resource optimization
- Production troubleshooting
- Kubernetes operational best practices
- Root Cause Analysis (RCA)

---

# 🎤 Interview Topics Covered

This module prepares you for interview questions on:

- Kubernetes Architecture
- Pods
- Deployments
- ReplicaSets
- Services
- ConfigMaps
- Secrets
- StatefulSets
- Persistent Volumes
- Persistent Volume Claims
- StorageClasses
- Jobs & CronJobs
- Namespaces
- RBAC
- Resource Requests & Limits
- CrashLoopBackOff
- ImagePullBackOff
- Pending Pods
- NetworkPolicies
- Kubernetes Events
- Production Troubleshooting

---

# 🚀 Production Best Practices

- Use declarative YAML manifests.
- Separate configuration using ConfigMaps.
- Store sensitive data in Secrets.
- Configure meaningful Resource Requests and Limits.
- Use Liveness and Readiness Probes appropriately.
- Review Kubernetes Events before making changes.
- Analyze application logs before restarting Pods.
- Apply the Principle of Least Privilege using RBAC.
- Validate storage before deploying stateful workloads.
- Perform Root Cause Analysis (RCA) after every production incident.
- Document findings to improve operational knowledge.

---

# 📈 Learning Outcomes

After completing this module, you will be able to:

- Deploy and manage Kubernetes applications confidently.
- Understand interactions between Kubernetes control plane components.
- Troubleshoot production issues using a structured methodology.
- Diagnose scheduling, networking, storage, and resource problems.
- Analyze Kubernetes Events, logs, and workload health.
- Build a strong operational foundation for Helm, OpenShift, GitOps, Infrastructure as Code (Terraform), Ansible, and Observability.

---

# 🔗 Next Module

➡️ **02 – Helm Package Management**

The next module focuses on packaging, templating, versioning, dependency management, and production deployment of Kubernetes applications using Helm charts.

---

# 🏁 Conclusion

The **Kubernetes Foundation** module establishes the core operational knowledge required for modern Platform Engineering. Through **13 hands-on labs**, **9 production troubleshooting scenarios**, and comprehensive documentation, this module emphasizes practical implementation, operational excellence, and systematic troubleshooting.

It provides a solid foundation for advanced topics such as **Helm, OpenShift, GitOps, Terraform, Ansible, Observability, and AI Infrastructure**, while also serving as a strong portfolio to demonstrate real-world Kubernetes skills during senior Platform Engineer interviews.
