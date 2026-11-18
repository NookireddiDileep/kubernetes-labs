# 🚨 Lab 13 - Kubernetes Production Troubleshooting

> **Production-focused Kubernetes troubleshooting scenarios for Platform Engineers, Site Reliability Engineers (SREs), and OpenShift Administrators.**

---

## 📖 Overview

Troubleshooting is one of the most valuable skills for a Platform Engineer. While deploying applications is important, identifying and resolving failures in production environments is what distinguishes experienced engineers.

This lab provides hands-on experience with common Kubernetes production issues by recreating real-world failure scenarios, investigating their root causes, applying corrective actions, and validating successful recovery.

Each scenario follows a structured troubleshooting workflow used in production environments.

---

# 🎯 Learning Objectives

After completing this lab, you will be able to:

- Troubleshoot common Kubernetes production issues
- Investigate Pod lifecycle failures
- Analyze Kubernetes Events
- Interpret application logs
- Diagnose scheduling failures
- Troubleshoot ConfigMaps and Secrets
- Understand Liveness and Readiness Probe failures
- Investigate Persistent Volume issues
- Understand Kubernetes NetworkPolicies
- Diagnose Resource Limit and OOMKilled issues
- Apply systematic troubleshooting techniques
- Prepare for Kubernetes and OpenShift interviews

---

# 🏗️ Lab Environment

| Component | Details |
|------------|---------|
| Kubernetes Distribution | K3s |
| Kubernetes Version | v1.36.2+k3s1 |
| Operating System | Ubuntu 24.04 LTS |
| Container Runtime | containerd |
| Cloud Platform | Google Cloud Platform (GCP) |
| Architecture | Single node (control plane and workloads) |

---

# 🧩 Troubleshooting Workflow

Every scenario follows the same production troubleshooting methodology.

```
Application Failure
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

# 📂 Repository Structure

```
lab-13-production-troubleshooting/
│
├── README.md
├── observations.md
├── interview-cheatsheet.md
│
├── scenario-01-crashloopbackoff/
├── scenario-02-imagepullbackoff/
├── scenario-03-pending-pod/
├── scenario-04-configmap/
├── scenario-05-secret/
├── scenario-06-liveness-readiness/
├── scenario-07-persistent-volume/
├── scenario-08-network-policy/
└── scenario-09-resource-limits/
```

---

# 📚 Scenarios Covered

| Scenario | Topic | Skills Covered |
|----------|-------|----------------|
| 01 | CrashLoopBackOff | Pod failures, Events, Logs, Restart behavior |
| 02 | ImagePullBackOff | Container registry, Image troubleshooting |
| 03 | Pending Pod | Scheduler, NodeSelector, Scheduling |
| 04 | ConfigMap | Configuration management |
| 05 | Secret | Secure configuration and secret injection |
| 06 | Liveness & Readiness | Health checks and application availability |
| 07 | Persistent Volume | Storage provisioning and PVC troubleshooting |
| 08 | NetworkPolicy | Pod-to-Pod communication and network security |
| 09 | Resource Limits & OOMKilled | Memory management and resource optimization |

---

# 🛠️ Common Troubleshooting Commands

## Pod Status

```bash
kubectl get pods
```

## Pod Details

```bash
kubectl describe pod <pod-name>
```

## Container Logs

```bash
kubectl logs <pod-name>
```

## Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Resource Usage

```bash
kubectl top pods
kubectl top nodes
```

## Node Information

```bash
kubectl get nodes
kubectl describe node <node-name>
```

---

# 🎯 Production Skills Developed

Throughout this lab, you will gain experience with:

- Kubernetes Pod Lifecycle
- Scheduling and Node Selection
- Application Health Checks
- Configuration Management
- Secret Management
- Persistent Storage
- Networking
- Resource Management
- Event Analysis
- Log Analysis
- Root Cause Analysis (RCA)
- Production Incident Troubleshooting

---

# 🚀 Production Best Practices

- Always investigate Events before making changes.
- Analyze application logs before restarting workloads.
- Validate ConfigMaps and Secrets before deployment.
- Configure meaningful resource requests and limits.
- Use Health Probes appropriately.
- Apply the principle of least privilege with NetworkPolicies.
- Validate storage configuration before deploying stateful workloads.
- Document root causes and corrective actions after every incident.

---

# 🎤 Interview Topics Covered

This lab prepares you for interview questions on:

- CrashLoopBackOff
- ImagePullBackOff
- Pending Pods
- Scheduler
- NodeSelector
- ConfigMaps
- Secrets
- Health Probes
- Persistent Volumes
- StorageClasses
- NetworkPolicies
- Resource Requests & Limits
- OOMKilled
- Kubernetes Events
- Kubernetes Logs
- Production Troubleshooting

---

# 📈 Learning Outcomes

By completing this lab, you will be able to:

- Troubleshoot Kubernetes workloads with confidence.
- Perform structured Root Cause Analysis (RCA).
- Resolve common production failures.
- Understand interactions between Kubernetes components.
- Apply troubleshooting techniques used by Platform Engineers and SREs.
- Demonstrate practical Kubernetes operational skills during technical interviews.

---

# 📚 Related Documentation

- **observations.md** – Summary of findings from each troubleshooting scenario.
- **interview-cheatsheet.md** – Interview questions and quick revision notes.
- **Scenario README files** – Detailed walkthroughs for each production issue.

---

# 🏁 Conclusion

Production troubleshooting is a critical responsibility of every Platform Engineer. This lab provides practical experience with common Kubernetes failures, emphasizing investigation, root cause analysis, remediation, and validation.

Completing these scenarios strengthens operational knowledge and prepares engineers for real-world production environments and senior-level Kubernetes, OpenShift, and Platform Engineering interviews.
