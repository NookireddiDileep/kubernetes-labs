# 🎤 Kubernetes Production Troubleshooting Interview Cheatsheet

> A quick-reference guide covering common Kubernetes production troubleshooting questions for Platform Engineers, Site Reliability Engineers (SREs), DevOps Engineers, and OpenShift Administrators.

---

# 📖 Contents

- Pod Lifecycle
- CrashLoopBackOff
- ImagePullBackOff
- Pending Pods
- ConfigMaps
- Secrets
- Liveness & Readiness Probes
- Persistent Volumes
- NetworkPolicies
- Resource Requests & Limits
- OOMKilled
- Scheduler
- Production Troubleshooting
- Kubernetes Components
- Best Practices

---

# 🚀 Pod Lifecycle

### Q1. What are the phases of a Pod?

- Pending
- Running
- Succeeded
- Failed
- Unknown

---

### Q2. Which Kubernetes component creates Pods?

The appropriate controller (Deployment, ReplicaSet, StatefulSet, Job, etc.) creates Pods by reconciling the desired state.

---

### Q3. Which component schedules Pods?

**kube-scheduler**

---

### Q4. Which component starts containers?

**kubelet**, working with the configured container runtime (for example, containerd).

---

# 🚨 CrashLoopBackOff

### Q5. What is CrashLoopBackOff?

A Pod repeatedly crashes after starting, and Kubernetes applies an exponential delay before each restart.

---

### Q6. Common causes?

- Application crash
- Invalid startup command
- Missing dependencies
- Configuration errors
- Failed health checks

---

### Q7. Troubleshooting commands?

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get events
```

---

### Q8. Is CrashLoopBackOff the root cause?

No.

It is a symptom.

The root cause is usually found in logs or Events.

---

# 🚨 ImagePullBackOff

### Q9. What causes ImagePullBackOff?

Kubernetes cannot pull the container image.

---

### Q10. Common reasons?

- Wrong image name
- Wrong tag
- Private registry authentication failure
- Registry unavailable
- Network connectivity issues

---

### Q11. Difference between ErrImagePull and ImagePullBackOff?

- ErrImagePull → Initial image pull failure
- ImagePullBackOff → Repeated retries with exponential backoff

---

# 🚨 Pending Pods

### Q12. Why does a Pod remain Pending?

Possible reasons include:

- No matching node
- Insufficient CPU
- Insufficient memory
- NodeSelector mismatch
- Node Affinity mismatch
- Taints without tolerations
- Unbound PersistentVolumeClaim

---

### Q13. Which component is responsible?

**kube-scheduler**

---

### Q14. Investigation commands

```bash
kubectl describe pod
kubectl get nodes
kubectl get events
```

---

# 📦 ConfigMaps

### Q15. What is a ConfigMap?

A Kubernetes object used to store non-sensitive configuration data.

---

### Q16. How can ConfigMaps be consumed?

- Environment variables
- Volume mounts
- Command arguments

---

### Q17. Troubleshooting steps

```bash
kubectl get configmaps
kubectl describe configmap
kubectl describe pod
```

---

# 🔐 Secrets

### Q18. What is a Secret?

A Kubernetes object used to store sensitive information.

Examples:

- Passwords
- Tokens
- Certificates

---

### Q19. How do you inspect Secrets?

```bash
kubectl get secrets
kubectl describe secret
```

---

### Q20. How are Secret values stored?

They are Base64-encoded by default. Encoding is not the same as encryption.

---

# ❤️ Liveness & Readiness

### Q21. Difference?

Liveness Probe

- Determines if a container should be restarted.

Readiness Probe

- Determines if the Pod should receive traffic.

---

### Q22. Startup Probe?

Allows slow-starting applications additional time before liveness checks begin.

---

### Q23. Common probe types

- HTTP
- TCP
- Exec
- gRPC

---

# 💾 Persistent Volumes

### Q24. Difference between PV and PVC?

PV

- Storage resource

PVC

- Storage request

---

### Q25. Why does a PVC remain Pending?

- StorageClass missing
- Provisioner unavailable
- No matching PV
- Capacity mismatch
- `WaitForFirstConsumer`: normal until a Pod uses the claim

---

# 🌐 NetworkPolicy

### Q26. What is a NetworkPolicy?

Defines how Pods communicate with each other and external endpoints.

---

### Q27. Does Kubernetes enforce NetworkPolicies?

No. The API server only stores them; a policy engine enforces them, usually the CNI plugin, or a separate controller (K3s ships one based on kube-router, so default K3s does enforce them).

---

### Q28. CNIs supporting NetworkPolicy?

- Calico
- Cilium
- Antrea
- OVN-Kubernetes

---

# 🧠 Resource Requests & Limits

### Q29. Requests vs Limits?

Requests

- Used for scheduling

Limits

- Maximum allowed resource usage

---

### Q30. What is OOMKilled?

A container exceeded its memory limit and was terminated by the Linux OOM Killer.

---

### Q31. Exit Code 137?

128 + 9 = SIGKILL. Often the OOM killer, but confirm with the `OOMKilled` reason; other SIGKILLs give 137 too.

---

# ⚙️ Scheduler

### Q32. What influences scheduling?

- Requests
- NodeSelector
- Node Affinity
- Taints
- Tolerations
- Available resources

---

### Q33. NodeSelector vs Node Affinity?

NodeSelector

- Exact label matching

Node Affinity

- More expressive scheduling rules

---

# 🔍 Production Troubleshooting

### Q34. First troubleshooting command?

```bash
kubectl get pods
```

---

### Q35. Most important command?

```bash
kubectl describe pod
```

---

### Q36. Where do you check application logs?

```bash
kubectl logs <pod-name>
```

---

### Q37. How do you inspect cluster events?

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

### Q38. How do you view node resource usage?

```bash
kubectl top nodes
```

---

### Q39. How do you view Pod resource usage?

```bash
kubectl top pods
```

---

# 🏗️ Kubernetes Components

### Q40. API Server

Cluster entry point.

---

### Q41. etcd

Stores cluster state.

---

### Q42. Scheduler

Assigns Pods to nodes.

---

### Q43. Controller Manager

Maintains desired state.

---

### Q44. kubelet

Runs Pods on nodes.

---

### Q45. Container Runtime

Runs containers.

Example:

- containerd

---

# ⭐ Production Best Practices

### Q46. What should you check first?

Events

---

### Q47. Before restarting a Pod?

Review logs and Events.

---

### Q48. Should you delete Pods immediately?

No.

Investigate first.

---

### Q49. Should requests equal limits?

Not always.

Configure them according to workload characteristics and capacity planning.

---

### Q50. What is the most important troubleshooting skill?

**Root Cause Analysis (RCA)**

Always identify the underlying cause instead of treating only the symptom.

---

# 🏁 Final Revision Checklist

Before a Kubernetes or Platform Engineer interview, make sure you can confidently explain:

- ✅ Pod Lifecycle
- ✅ Scheduler
- ✅ CrashLoopBackOff
- ✅ ImagePullBackOff
- ✅ Pending Pods
- ✅ ConfigMaps
- ✅ Secrets
- ✅ Health Probes
- ✅ Persistent Volumes
- ✅ NetworkPolicies
- ✅ Resource Requests & Limits
- ✅ OOMKilled
- ✅ Events
- ✅ Logs
- ✅ Root Cause Analysis (RCA)
- ✅ Kubernetes Control Plane Components
- ✅ Production Troubleshooting Workflow
