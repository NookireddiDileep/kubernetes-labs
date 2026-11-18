# 🚨 Scenario 09 - Resource Limits & OOMKilled Troubleshooting

## 🎯 Objective

Learn how to identify, investigate, troubleshoot, and resolve a Kubernetes **OOMKilled** issue caused by a container exceeding its configured memory limit.

This scenario demonstrates how Kubernetes enforces container resource limits and how Platform Engineers troubleshoot memory-related failures in production environments.

---

# 📚 Learning Objectives

After completing this lab, you will be able to:

- Understand Kubernetes resource requests and limits
- Differentiate between CPU and memory resource management
- Identify Pods terminated with `OOMKilled`
- Analyze Pod Events and container exit codes
- Troubleshoot memory-related application failures
- Apply corrective actions and verify recovery
- Explain OOMKilled scenarios during technical interviews

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

# 📁 Lab Files

```
scenario-09-resource-limits/
│
├── README.md
├── oom-demo.yaml
└── screenshots/
    ├── 01-pod-created.png
    ├── 02-oomkilled-status.png
    ├── 03-describe-pod.png
    ├── 04-events.png
    ├── 05-updated-resource-limits.png
    └── 06-running-after-fix.png
```

---

# 📖 Problem Statement

Kubernetes allows administrators to define resource **requests** and **limits** for containers.

In this lab, the container intentionally allocates more memory than its configured memory limit. Once the limit is exceeded, the Linux Out-Of-Memory (OOM) Killer terminates the process, causing the Pod to enter the **OOMKilled** state.

---

# 📝 Initial Manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: oom-demo
spec:
  containers:
  - name: stress
    image: polinux/stress
    command:
      - stress
    args:
      - "--vm"
      - "1"
      - "--vm-bytes"
      - "128M"
      - "--vm-hang"
      - "1"
    resources:
      requests:
        memory: "32Mi"
      limits:
        memory: "64Mi"
```

---

# 🚀 Deploy the Pod

```bash
kubectl apply -f oom-demo.yaml
```

Verify Pod status:

```bash
kubectl get pods
```

Expected output:

```
oom-demo   0/1   OOMKilled
```

---

# 🔍 Investigation Steps

## 1. Check Pod Status

```bash
kubectl get pods
```

Purpose:

- Verify the Pod status
- Observe restart count
- Identify OOMKilled

---

## 2. Describe the Pod

```bash
kubectl describe pod oom-demo
```

Purpose:

- Review Events
- Identify termination reason
- Verify Exit Code
- Review resource configuration

Expected output:

```
Reason: OOMKilled
Exit Code: 137
```

---

## 3. Review Kubernetes Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp | grep oom-demo
```

Purpose:

- Observe container termination events
- Confirm memory limit enforcement

---

# 🔬 Root Cause Analysis

The container attempts to allocate approximately **128Mi** of memory.

However, the Pod is configured with a memory limit of only **64Mi**.

Once memory consumption exceeds the configured limit, the Linux kernel terminates the process using the **Out-Of-Memory (OOM) Killer**.

Kubernetes records this termination as **OOMKilled** and restarts the container according to the Pod restart policy.

---

# 🔄 OOMKilled Lifecycle

```
Pod Created
      │
      ▼
Container Starts
      │
      ▼
Application Allocates Memory
      │
      ▼
Memory Usage Exceeds Limit
      │
      ▼
Linux OOM Killer Terminates Process
      │
      ▼
Exit Code 137
      │
      ▼
Pod Status = OOMKilled
```

---

# 🛠️ Resolution

> `oom-demo.yaml` in this folder is the **fixed** manifest (128Mi request, 256Mi limit). The broken version above used a 64Mi limit.

Increase the configured memory resources.

Updated manifest:

```yaml
resources:
  requests:
    memory: "128Mi"
  limits:
    memory: "256Mi"
```

Delete the existing Pod:

```bash
kubectl delete pod oom-demo
```

Deploy the updated manifest:

```bash
kubectl apply -f oom-demo.yaml
```

---

# ✅ Verification

Verify that the Pod is running successfully.

```bash
kubectl get pods
```

Expected output:

```
oom-demo   1/1   Running
```

---

# 📸 Screenshots

| Screenshot | Description |
|------------|-------------|
| 01-pod-created.png | Pod deployment |
| 02-oomkilled-status.png | OOMKilled status |
| 03-describe-pod.png | Pod description |
| 04-events.png | Kubernetes events |
| 05-updated-resource-limits.png | Updated resource limits |
| 06-running-after-fix.png | Pod running after fix |

---

# 💡 Production Troubleshooting Workflow

```
OOMKilled
      │
      ▼
kubectl get pods
      │
      ▼
kubectl describe pod
      │
      ▼
Review Exit Code
      │
      ▼
Check Resource Limits
      │
      ▼
Increase Memory Limit
      │
      ▼
Redeploy Pod
      │
      ▼
Verify Running State
```

---

# 🎯 Key Learning Outcomes

- Kubernetes enforces configured memory limits.
- Exceeding the memory limit results in an `OOMKilled` termination.
- Exit code **137** means SIGKILL; the `OOMKilled` reason is what proves it was memory.
- Resource requests influence scheduling, while limits control maximum resource usage.
- Proper resource sizing improves workload stability and cluster efficiency.

---

# 🚀 Production Best Practices

- Define realistic resource requests and limits based on application profiling.
- Monitor memory usage using Prometheus and Grafana.
- Investigate recurring OOMKilled events to detect memory leaks.
- Avoid setting unnecessarily low memory limits for production workloads.
- Use Vertical Pod Autoscaler (VPA) where appropriate to optimize resource allocation.
- Continuously review workload resource consumption as applications evolve.

---

# 🎤 Interview Questions

### Q1. What does OOMKilled mean?

A container was terminated because it exceeded its configured memory limit.

---

### Q2. What is Exit Code 137?

Exit code **137** = 128 + 9, i.e. the process was killed with **SIGKILL**. The OOM killer is one source of SIGKILL, so check the `Reason` field: `OOMKilled` confirms memory; other SIGKILLs (e.g. after a failed liveness probe's grace period) show different reasons.

---

### Q3. What is the difference between resource requests and limits?

- **Requests** specify the minimum resources reserved for scheduling.
- **Limits** define the maximum resources a container can consume.

---

### Q4. Which component enforces memory limits?

The Linux kernel enforces memory limits using **cgroups**, while Kubernetes configures those limits for the container runtime.

---

### Q5. How do you troubleshoot an OOMKilled Pod?

1. Check Pod status.
2. Describe the Pod.
3. Review Events.
4. Verify Exit Code.
5. Review resource requests and limits.
6. Increase memory limits or optimize application memory usage.
7. Redeploy and verify the Pod.

---

### Q6. Can increasing the memory limit always solve OOMKilled?

Not always. If an application has a memory leak or inefficient memory usage, increasing the limit may only delay the problem. Application profiling and optimization may also be required.

---

# 🏁 Conclusion

This lab demonstrated how Kubernetes manages container memory through resource requests and limits, how to investigate **OOMKilled** events, and how to resolve memory-related failures.

Understanding Kubernetes resource management and memory troubleshooting is an essential skill for Platform Engineers, Kubernetes Administrators, OpenShift Engineers, and Site Reliability Engineers responsible for operating production Kubernetes environments.
