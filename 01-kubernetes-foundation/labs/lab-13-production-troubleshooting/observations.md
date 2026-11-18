# 📝 Lab 13 - Production Troubleshooting Observations

## 📖 Overview

This document summarizes the observations, root causes, troubleshooting steps, and resolutions for each production troubleshooting scenario completed in **Lab 13**.

The objective is to build a structured troubleshooting mindset by following a consistent **Observe → Investigate → Analyze → Resolve → Verify** workflow.

---

# 🔍 Standard Troubleshooting Workflow

```
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
Verify Recovery
      │
      ▼
Document Findings
```

---

# 🚨 Scenario 01 – CrashLoopBackOff

## Observation

- Pod repeatedly restarted after container startup.
- Pod status changed to **CrashLoopBackOff**.
- Restart count continuously increased.

## Root Cause

The container exited immediately because the startup command executed:

```bash
exit 1
```

Kubernetes restarted the container repeatedly until exponential backoff was applied.

## Investigation Commands

```bash
kubectl get pods
kubectl describe pod crash-demo
kubectl logs crash-demo
kubectl get events
```

## Resolution

Updated the startup command to:

```bash
sleep 3600
```

Recreated the Pod and verified it reached the **Running** state.

## Key Learning

CrashLoopBackOff is a symptom of repeated application failures, not the root cause itself.

---

# 🚨 Scenario 02 – ImagePullBackOff

## Observation

- Pod failed before container startup.
- Status changed from **ErrImagePull** to **ImagePullBackOff**.

## Root Cause

The image tag specified in the manifest did not exist:

```text
nginx:wrongtag
```

The container runtime could not download the image.

## Investigation Commands

```bash
kubectl get pods
kubectl describe pod imagepull-demo
kubectl get events
```

## Resolution

Updated the image reference to:

```text
nginx:latest
```

Redeployed the Pod successfully.

## Key Learning

ImagePullBackOff indicates repeated image download failures. Always verify image names, tags, and registry access.

---

# 🚨 Scenario 03 – Pending Pod

## Observation

- Pod remained in the **Pending** state.
- No node was assigned by the Scheduler.

## Root Cause

The Pod specified a `nodeSelector` that did not match any available node labels.

## Investigation Commands

```bash
kubectl get pods
kubectl describe pod pending-demo
kubectl get nodes --show-labels
kubectl get events
```

## Resolution

Removed the invalid `nodeSelector` and redeployed the Pod.

## Key Learning

The Kubernetes Scheduler only places Pods on nodes that satisfy all scheduling constraints.

---

# 🚨 Scenario 04 – ConfigMap Troubleshooting

## Observation

- Pod could not start correctly because required configuration was unavailable.

## Root Cause

The referenced ConfigMap did not exist.

## Investigation Commands

```bash
kubectl describe pod configmap-demo
kubectl get configmaps
kubectl get events
```

## Resolution

Created the missing ConfigMap and recreated the Pod.

## Key Learning

Always verify ConfigMaps before deploying workloads that depend on them.

---

# 🚨 Scenario 05 – Secret Troubleshooting

## Observation

- Pod failed during startup because sensitive configuration was unavailable.

## Root Cause

The referenced Secret was missing.

## Investigation Commands

```bash
kubectl describe pod secret-demo
kubectl get secrets
kubectl get events
```

## Resolution

Created the required Secret and redeployed the Pod.

## Key Learning

Secrets should be created and validated before workload deployment.

---

# 🚨 Scenario 06 – Liveness & Readiness Probe

## Observation

- Container restarted repeatedly because health checks failed.

## Root Cause

The Liveness Probe checked an invalid endpoint.

## Investigation Commands

```bash
kubectl describe pod probe-demo
kubectl get events
```

## Resolution

Updated the probe configuration to use a valid endpoint.

## Key Learning

Health probes determine application availability and container lifecycle management.

---

# 🚨 Scenario 07 – Persistent Volume Troubleshooting

## Observation

- PersistentVolumeClaim remained in the **Pending** state.

## Root Cause

The requested StorageClass was unavailable or incorrectly configured.

## Investigation Commands

```bash
kubectl get pvc
kubectl describe pvc
kubectl get storageclass
```

## Resolution

Updated the StorageClass to the available default (`local-path`) and verified the PVC reached the **Bound** state.

## Key Learning

Persistent storage depends on correctly configured StorageClasses and available provisioners.

---

# 🚨 Scenario 08 – NetworkPolicy Troubleshooting

## Observation

- Verified Pod-to-Pod communication before and after applying a `deny-all-ingress` NetworkPolicy on `app: nginx`.
- Before: BusyBox reached NGINX. After: `wget` failed with `Connection refused` (exit code 1).

## Root Cause

A NetworkPolicy defines traffic rules; a policy engine on each node enforces them.

Default K3s pairs Flannel (which has no policy support) with an **embedded kube-router network policy controller**, so the policy **was enforced** and ingress to NGINX was blocked.

## Investigation Commands

```bash
kubectl get networkpolicy
kubectl describe networkpolicy
kubectl get pods --show-labels
```

## Resolution

Confirmed the policy selected the NGINX pod by label and that traffic was blocked; removed the policy during cleanup to restore access.

## Key Learning

NetworkPolicies need a policy engine: a policy-capable CNI (Calico, Cilium, Antrea, OVN-Kubernetes) or a separate controller such as the one K3s embeds. Without one, policies are accepted but ignored.

---

# 🚨 Scenario 09 – Resource Limits & OOMKilled

## Observation

- Container terminated with **OOMKilled**.
- Exit Code **137** recorded in Pod details.

## Root Cause

The application exceeded the configured memory limit, causing the Linux Out-Of-Memory (OOM) Killer to terminate the process.

## Investigation Commands

```bash
kubectl get pods
kubectl describe pod oom-demo
kubectl get events
```

## Resolution

Increased the memory requests and limits, then redeployed the Pod successfully.

## Key Learning

Resource requests influence scheduling, while limits define the maximum resources a container can consume.

---

# 📚 Overall Learning Outcomes

After completing this lab, the following production skills were developed:

- Kubernetes Pod lifecycle troubleshooting
- Event-driven diagnostics
- Container log analysis
- Scheduler troubleshooting
- Configuration management
- Secret management
- Health probe validation
- Persistent storage troubleshooting
- NetworkPolicy analysis
- Kubernetes resource management
- Root Cause Analysis (RCA)
- Structured production troubleshooting

---

# 🎯 Final Conclusion

This lab provided hands-on experience with nine common Kubernetes production issues using a structured troubleshooting methodology.

The scenarios reinforced the importance of collecting evidence, identifying root causes, applying targeted fixes, and validating recovery before closing an incident.

These troubleshooting practices are directly applicable to Platform Engineering, Site Reliability Engineering (SRE), Kubernetes Administration, and OpenShift operations in production environments.
