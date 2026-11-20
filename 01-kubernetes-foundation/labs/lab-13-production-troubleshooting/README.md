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

# Upstream Kubernetes translation of the pod failure states decoder

The full troubleshooting map for every pod failure state (plain language → mechanism → fix → L4 interview angle) lives in one place: the [pod failure states decoder](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md) in my openshift-lab-records repo. It's written with an OpenShift lens; this table maps it to upstream Kubernetes and to the scenarios in this lab.

**Scenario → decoder section**

| Scenario here | Decoder section |
|---|---|
| [01 CrashLoopBackOff](scenario-01-crashloop/README.md) | [§10 CrashLoopBackOff](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#10-crashloopbackoff) |
| [02 ImagePullBackOff](scenario-02-imagepullbackoff/README.md) | [§7 ErrImagePull / ImagePullBackOff](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#7-errimagepull--imagepullbackoff) |
| [03 Pending pod](scenario-03-pending-pod/README.md) | [§5 Pending](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#5-pending) |
| [04 ConfigMap](scenario-04-configmap/README.md), [05 Secret](scenario-05-secret/README.md) | [§8 CreateContainerConfigError](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#8-createcontainerconfigerror--createcontainererror) |
| [06 Probe failures](scenario-06-liveness-readiness/README.md) | [§10 CrashLoopBackOff](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#10-crashloopbackoff) (liveness) and [§12 Running but not Ready](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#12-running-but-not-ready-01) |
| [07 PVC Pending](scenario-07-persistent-volume/README.md) | [§5 Pending](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#5-pending) (unbound PVC) |
| [08 NetworkPolicy](scenario-08-network-policy/README.md) | Not a pod state: see [R01 networking](../../../reference/R01-networking-end-to-end.md#7-networkpolicy) |
| [09 OOMKilled](scenario-09-resource-limits/README.md) | [§11 OOMKilled](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md#11-oomkilled) |

**OpenShift wording → upstream K3s**

| The decoder says | On upstream Kubernetes / K3s |
|---|---|
| `oc …` | `kubectl …` (same verbs) |
| SCC rejection, random UID, `restricted-v2`, `anyuid` | No SCCs. Pod Security Admission namespace labels (`privileged` / `baseline` / `restricted`) and `securityContext`; images often run as root unless you restrict them |
| `oc debug node/<n>` → `chroot /host` → `crictl` | SSH to the node, `sudo k3s crictl …` (containerd) |
| CRI-O logs: `journalctl -u crio` | `journalctl -u k3s` (K3s runs kubelet and containerd inside its own service) |
| Router / Route returns 503 | Ingress controller (Traefik in K3s) or Gateway returns 404/503 |
| `image.config.openshift.io` (trusted CAs, mirrors) | containerd `registries.yaml` (`/etc/rancher/k3s/registries.yaml`) |
| ODF / vSphere CSI | `local-path` provisioner (`WaitForFirstConsumer`) |
| OVN-Kubernetes | Flannel (VXLAN) + kube-router network policy controller |

---

# 📚 Related Documentation

- **observations.md** – Summary of findings from each troubleshooting scenario.
- **interview-cheatsheet.md** – Interview questions and quick revision notes.
- **Scenario README files** – Detailed walkthroughs for each production issue.
- **[Pod failure states decoder](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md)** – the full troubleshooting map these scenarios plug into (see the translation table above).

---

# 🏁 Conclusion

Production troubleshooting is a critical responsibility of every Platform Engineer. This lab provides practical experience with common Kubernetes failures, emphasizing investigation, root cause analysis, remediation, and validation.

Completing these scenarios strengthens operational knowledge and prepares engineers for real-world production environments and senior-level Kubernetes, OpenShift, and Platform Engineering interviews.
