# Kubernetes Architecture

1. Introduction
2. Kubernetes Cluster Architecture
3. Control Plane Components
4. Worker Node Components
5. End-to-End Kubernetes API Request Flow
6. Pod Lifecycle
7. Deployment Workflow
8. ReplicaSet Workflow
9. Service Networking
10. DNS Resolution
11. ConfigMap & Secret Flow
12. Persistent Volume Workflow
13. Scheduler Workflow
14. Resource Scheduling
15. Health Probe Workflow
16. Namespace Isolation
17. RBAC Authorization Flow
18. NetworkPolicy Overview
19. Production Troubleshooting Workflow
20. Kubernetes Best Practices
21. Interview Quick Revision


# ☸️ Kubernetes Architecture

> **A comprehensive guide to Kubernetes architecture, control plane components, worker nodes, and the end-to-end request flow.**

---

# 📖 Introduction

Kubernetes is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications.

Instead of managing individual containers manually, Kubernetes provides a declarative model where users define the desired state of the system, and Kubernetes continuously works to maintain that state.

Understanding Kubernetes architecture is essential for Platform Engineers because almost every production issue eventually involves one or more core Kubernetes components.

This document explains how Kubernetes works internally, what happens when resources are created, and how workloads are scheduled and managed.

---

# 🏗️ Kubernetes Cluster Architecture

A Kubernetes cluster consists of two primary layers:

- **Control Plane** – Makes global decisions about the cluster and maintains its desired state.
- **Worker Nodes** – Execute and manage application workloads.

```text
                    Kubernetes Cluster
┌──────────────────────────────────────────────────────────┐
│                                                          │
│                 Control Plane                            │
│                                                          │
│  ┌──────────┐  ┌──────────┐  ┌─────────────┐             │
│  │API Server│  │ Scheduler│  │ControllerMgr│             │
│  └────┬─────┘  └────┬─────┘  └──────┬──────┘             │
│       │             │               │                    │
│       └─────────────┼───────────────┘                    │
│                     │                                    │
│                  ┌──▼──┐                                │
│                  │ etcd│                                │
│                  └─────┘                                │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                    Worker Node                          │
│                                                         │
│  ┌────────┐  ┌────────────┐  ┌────────────┐             │
│  │kubelet │  │ kube-proxy │  │containerd  │             │
│  └────┬───┘  └─────┬──────┘  └─────┬──────┘             │
│       │            │               │                    │
│       └────────────┼───────────────┘                    │
│                    ▼                                    │
│                 Running Pods                            │
└─────────────────────────────────────────────────────────┘
```

---

# 🎯 Control Plane Components

The Control Plane manages the overall state of the Kubernetes cluster.

---

## 1. Kubernetes API Server

The **API Server** is the entry point to the Kubernetes cluster.

Every operation performed using `kubectl`, CI/CD pipelines, operators, or controllers ultimately communicates with the API Server.

Responsibilities:

- Receives REST API requests
- Authenticates users
- Authorizes requests using RBAC
- Executes Admission Controllers
- Validates manifests
- Stores cluster state in etcd
- Returns API responses

Example:

```bash
kubectl apply -f deployment.yaml
```

The request is always processed by the API Server first.

---

## 2. etcd

etcd is Kubernetes' distributed key-value database.

It stores the complete desired state of the cluster.

Examples of stored objects:

- Pods
- Deployments
- Services
- ConfigMaps
- Secrets
- Nodes
- Persistent Volumes
- RBAC Policies

Think of etcd as the "source of truth" for the cluster.

---

## 3. kube-scheduler

The Scheduler is responsible for assigning Pods to appropriate Worker Nodes.

It evaluates multiple scheduling constraints before making a decision.

Typical scheduling factors include:

- CPU availability
- Memory availability
- Resource requests
- Node labels
- Node selectors
- Node affinity
- Taints and tolerations
- Topology constraints

The Scheduler **does not create Pods**. It selects the best node for Pods that are waiting to be scheduled.

---

## 4. Controller Manager

The Controller Manager runs reconciliation loops that continuously compare the current cluster state with the desired state stored in etcd.

Examples include:

- Deployment Controller
- ReplicaSet Controller
- StatefulSet Controller
- Job Controller
- Node Controller
- Namespace Controller

Example:

Desired replicas:

```text
3
```

Current replicas:

```text
2
```

The Deployment Controller creates one additional Pod to restore the desired state.

---

# ⚙️ Worker Node Components

Worker Nodes run the actual application workloads.

Each node contains several core components.

---

## 1. kubelet

The kubelet is the node agent responsible for ensuring Pods assigned to the node are running as expected.

Responsibilities:

- Watches the API Server
- Starts containers
- Monitors container health
- Reports node and Pod status
- Executes health probes
- Mounts ConfigMaps, Secrets, and Volumes

Without kubelet, a node cannot participate in the cluster.

---

## 2. kube-proxy

kube-proxy manages network rules that enable communication between Services and Pods.

Responsibilities:

- Implements Service networking
- Maintains networking rules
- Supports ClusterIP, NodePort, and LoadBalancer Services
- Performs load balancing across backend Pods

---

## 3. Container Runtime

The container runtime executes application containers.

In this lab environment, the runtime is:

```text
containerd
```

Responsibilities:

- Pull container images
- Create containers
- Start containers
- Stop containers
- Remove containers

Examples of supported runtimes include:

- containerd
- CRI-O

---

# 🔄 End-to-End Kubernetes API Request Flow

One of the most common interview questions is:

> **What happens after running `kubectl apply -f deployment.yaml`?**

The following workflow illustrates the complete process.

```text
kubectl apply deployment.yaml
            │
            ▼
     Kubernetes API Server
            │
            ▼
      Authentication
            │
            ▼
    Authorization (RBAC)
            │
            ▼
    Admission Controllers
            │
            ▼
      Manifest Validation
            │
            ▼
            etcd
            │
            ▼
   Deployment Controller
            │
            ▼
       ReplicaSet Created
            │
            ▼
        Pod Object Created
            │
            ▼
      Scheduler Selects Node
            │
            ▼
  kubelet Receives Assignment
            │
            ▼
 containerd Pulls Image
            │
            ▼
   Container Starts Running
            │
            ▼
      Pod Status = Running
```

---

# 📌 Key Takeaways

- The API Server is the gateway to the Kubernetes cluster.
- etcd stores the desired state of all Kubernetes resources.
- The Scheduler assigns Pods to nodes but does not create them.
- Controllers continuously reconcile the current state with the desired state.
- kubelet manages Pods on each worker node.
- kube-proxy enables Service networking.
- containerd is responsible for running containers.
- Kubernetes continuously works to maintain the desired state declared by users.

---

# 🎤 Interview Quick Revision

### Q1. Which component receives all Kubernetes API requests?

**API Server**

---

### Q2. Where is the cluster state stored?

**etcd**

---

### Q3. Which component schedules Pods?

**kube-scheduler**

---

### Q4. Which component maintains the desired state?

**Controller Manager**

---

### Q5. Which component runs on every worker node?

**kubelet**

---

### Q6. Which component manages Service networking?

**kube-proxy**

---

### Q7. Which component runs containers?

**containerd (or another CRI-compliant runtime)**

---

### Q8. What is Kubernetes' design philosophy?

Users declare the desired state, and Kubernetes continuously reconciles the current state to match it.


---

# 🚀 Pod Lifecycle

A Pod is the smallest deployable unit in Kubernetes. It encapsulates one or more tightly coupled containers that share networking and storage resources.

Unlike containers, Pods are **ephemeral**. If a Pod fails or is deleted, Kubernetes creates a new Pod rather than repairing the existing one.

## Pod Lifecycle Phases

```text
                Pod Created
                     │
                     ▼
                 Pending
                     │
                     ▼
      Image Pulled & Container Created
                     │
                     ▼
                  Running
                     │
         ┌───────────┴────────────┐
         ▼                        ▼
    Succeeded                 Failed
                     │
                     ▼
                 Pod Deleted
```

### Pending

The Pod has been accepted by the Kubernetes API but has not yet started.

Possible reasons:

- Scheduler has not assigned a node
- Image download in progress
- Waiting for PVC
- Waiting for Secrets/ConfigMaps

---

### Running

The Pod has been scheduled successfully.

Container(s):

- Created
- Started
- Healthy

Application is now serving requests.

---

### Succeeded

All containers exited successfully.

Common for:

- Jobs
- CronJobs

---

### Failed

One or more containers terminated unexpectedly.

Examples:

- Application crash
- OOMKilled
- Exit Code 1
- Exit Code 137

---

### Unknown

Kubernetes cannot determine the Pod state.

Usually caused by:

- Node communication failure
- kubelet unavailable

---

# 📦 Deployment Workflow

Deployments provide declarative management of stateless applications.

A Deployment does **not** create Pods directly.

Instead, Kubernetes follows this chain:

```text
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
      │
      ▼
Containers
```

---

## Deployment Responsibilities

A Deployment manages:

- Desired replica count
- Rolling updates
- Rollbacks
- Self-healing
- Scaling

Example:

```yaml
replicas: 3
```

If one Pod fails:

```text
Desired Pods = 3

Current Pods = 2

↓

Deployment notices mismatch

↓

ReplicaSet creates new Pod

↓

Cluster returns to desired state
```

This reconciliation process is one of Kubernetes' core strengths.

---

# 🔄 ReplicaSet Workflow

A ReplicaSet ensures that a specified number of Pod replicas are running.

Example:

```yaml
replicas: 3
```

Current state:

```text
Running Pods = 2
```

ReplicaSet immediately creates:

```text
1 additional Pod
```

Result:

```text
Running Pods = 3
```

ReplicaSets are typically managed by Deployments rather than created directly.

---

# 🎯 Scheduler Decision Process

One of the Scheduler's primary responsibilities is selecting the most suitable node for each Pending Pod.

The Scheduler evaluates multiple criteria before making a placement decision.

```text
          Pending Pod
                │
                ▼
         kube-scheduler
                │
      ┌─────────┼──────────┐
      ▼         ▼          ▼
CPU Available?
Memory Available?
Node Ready?
Labels Match?
Affinity Rules?
Taints/Tolerations?
Storage Available?
Topology Constraints?
                │
                ▼
        Best Node Selected
                │
                ▼
        Pod Assigned to Node
```

### The Scheduler Considers

- CPU Requests
- Memory Requests
- Available Node Capacity
- Node Labels
- NodeSelector
- Node Affinity
- Pod Affinity
- Pod Anti-Affinity
- Taints
- Tolerations
- Storage requirements
- Topology spread constraints

---

# ⚖️ Resource Requests vs Limits

Resource management is essential for efficient scheduling and cluster stability.

## Requests

Requests define the minimum resources a Pod requires.

Example:

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
```

Requests are used by the Scheduler when selecting a node.

---

## Limits

Limits define the maximum resources a container can consume.

Example:

```yaml
resources:
  limits:
    cpu: "500m"
    memory: "512Mi"
```

If the application exceeds its memory limit:

```text
OOMKilled
Exit Code 137
```

---

## Requests vs Limits Summary

| Requests | Limits |
|-----------|--------|
| Used for scheduling | Maximum resource usage |
| Guarantees minimum resources | Prevents excessive consumption |
| Checked by Scheduler | Enforced by kubelet/runtime |

---

# 🏭 End-to-End Pod Creation Workflow

The following diagram summarizes everything that happens when a Deployment is created.

```text
kubectl apply deployment.yaml
            │
            ▼
      API Server
            │
            ▼
         Authentication
            │
            ▼
      Authorization (RBAC)
            │
            ▼
     Admission Controllers
            │
            ▼
            etcd
            │
            ▼
Deployment Controller
            │
            ▼
      ReplicaSet
            │
            ▼
      Pod Object
            │
            ▼
     kube-scheduler
            │
            ▼
      Selected Node
            │
            ▼
         kubelet
            │
            ▼
      containerd
            │
            ▼
 Image Pulled from Registry
            │
            ▼
 Container Created
            │
            ▼
 Liveness / Readiness Checks
            │
            ▼
 Pod Status = Running
```

---

# 💡 Production Best Practices

- Use Deployments instead of standalone Pods for stateless applications.
- Configure appropriate resource requests and limits.
- Avoid creating ReplicaSets directly unless required.
- Monitor Pending Pods for scheduling issues.
- Investigate Events before modifying workloads.
- Configure health probes for production services.
- Use rolling updates for application deployments.
- Monitor Pod restart counts to identify recurring failures.

---

# 🎤 Interview Quick Revision

### Q1. Does a Deployment create Pods directly?

No.

Deployment → ReplicaSet → Pods.

---

### Q2. What is the responsibility of a ReplicaSet?

Maintain the desired number of Pod replicas.

---

### Q3. What does the Scheduler do?

Selects the most appropriate node for a Pending Pod.

---

### Q4. What are Resource Requests used for?

Scheduling decisions.

---

### Q5. What are Resource Limits used for?

Restricting the maximum CPU and memory a container can consume.

---

### Q6. What happens if a container exceeds its memory limit?

The Linux Out-Of-Memory (OOM) Killer terminates the container, and Kubernetes reports the Pod as **OOMKilled** (typically with Exit Code 137).

---

### Q7. What happens if one Pod in a Deployment fails?

The ReplicaSet creates a replacement Pod to restore the desired replica count.

---

### Q8. Why are Pods considered ephemeral?

Pods are not repaired in place. When they fail or are deleted, Kubernetes creates new Pods to maintain the desired state.


---

# 🌐 Service Networking

Pods in Kubernetes are ephemeral—they can be created, deleted, or recreated with different IP addresses. Applications therefore communicate through **Services**, which provide a stable network endpoint.

## Why Services?

Without a Service:

```text
Client
   │
   ▼
Pod (10.42.0.5)

Pod deleted

↓

New Pod (10.42.0.12)

↓

Application unavailable
```

With a Service:

```text
        Client
           │
           ▼
      Service (ClusterIP)
           │
           ▼
      kube-proxy
           │
     ┌─────┴─────┐
     ▼           ▼
 Pod A       Pod B
```

The Service remains constant while Pods change underneath it.

---

# 🔀 kube-proxy Workflow

kube-proxy runs on every worker node.

Responsibilities:

- Watches the Kubernetes API
- Detects Service changes
- Programs networking rules
- Distributes traffic across backend Pods

Example

```text
Service

↓

Backend Pods

Pod-1

Pod-2

Pod-3

↓

kube-proxy load balances requests

↓

Client receives response
```

---

# 🌍 Service Types

## ClusterIP

Default Service type.

Accessible only within the cluster.

```text
Pod

↓

ClusterIP Service

↓

Other Pods
```

---

## NodePort

Exposes the Service through a port on every node.

```text
Client

↓

NodeIP:30080

↓

Service

↓

Pods
```

---

## LoadBalancer

Used in cloud environments.

```text
Internet

↓

Cloud Load Balancer

↓

Service

↓

Pods
```

---

# 🌐 DNS Resolution

Kubernetes includes an internal DNS service (CoreDNS).

Instead of connecting to Pod IPs:

```text
10.42.0.15
```

Applications use DNS names.

Example:

```text
nginx-service.default.svc.cluster.local
```

DNS Resolution Flow

```text
Application

↓

DNS Query

↓

CoreDNS

↓

Service IP

↓

Backend Pod
```

Benefits

- Stable service discovery
- No hardcoded IPs
- Automatic updates

---

# ⚙️ ConfigMap Workflow

ConfigMaps store non-sensitive configuration.

Examples

- Environment names
- URLs
- Feature flags
- Configuration files

Workflow

```text
ConfigMap Created

↓

Stored in API Server

↓

etcd

↓

Referenced by Pod

↓

Mounted as:

• Environment Variables

OR

• Files

↓

Application Reads Configuration
```

Example

```yaml
env:
- name: APP_ENV
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: environment
```

Benefits

- Configuration separated from application code
- Easier updates
- Reusable across workloads

---

# 🔐 Secret Workflow

Secrets store sensitive information.

Examples

- Passwords
- Tokens
- API Keys
- Certificates

Workflow

```text
Secret Created

↓

API Server

↓

etcd

↓

Pod References Secret

↓

Mounted as:

Environment Variable

OR

Volume

↓

Application Uses Secret
```

Example

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```

Important Note

Secrets are Base64-encoded by default.

Encoding is **not** encryption.

For production environments, consider enabling encryption at rest for etcd and implementing appropriate access controls.

---

# 💾 Persistent Storage Workflow

Containers have ephemeral filesystems.

When a Pod is deleted:

```text
Container Files

↓

Deleted
```

Persistent Volumes solve this problem.

Workflow

```text
Application

↓

PersistentVolumeClaim

↓

StorageClass

↓

Provisioner

↓

PersistentVolume

↓

Underlying Storage

↓

Disk
```

---

# 📦 Persistent Volume (PV)

A Persistent Volume represents available storage.

Example

```yaml
capacity:
  storage: 5Gi
```

Characteristics

- Independent of Pods
- Can outlive Pods
- Can be reused

---

# 📥 Persistent Volume Claim (PVC)

A PVC is a request for storage.

Example

```yaml
resources:
  requests:
    storage: 2Gi
```

The PVC requests storage.

The PV provides storage.

---

# ⚡ Dynamic Provisioning

Modern Kubernetes clusters typically create storage automatically.

Workflow

```text
PVC Created

↓

StorageClass

↓

CSI Provisioner

↓

PersistentVolume Created

↓

PVC Bound

↓

Pod Starts
```

In this lab environment

```text
StorageClass

↓

local-path

↓

Dynamic PV
```

---

# 🗂️ StorageClasses

A StorageClass defines how storage should be provisioned.

Example

```yaml
storageClassName: local-path
```

Typical parameters include

- Provisioner
- Reclaim Policy
- Volume Binding Mode
- Allow Volume Expansion

---

# 🔄 Volume Mount Workflow

Once a PVC is bound:

```text
Pod

↓

Volume Mount

↓

Persistent Volume

↓

Disk

↓

Data Persists
```

Even if the Pod is recreated:

```text
Old Pod

↓

Deleted

↓

New Pod

↓

Same PVC

↓

Same Data
```

---

# 💡 Production Best Practices

- Use Services instead of Pod IP addresses.
- Prefer DNS names for service-to-service communication.
- Store non-sensitive configuration in ConfigMaps.
- Store credentials in Secrets.
- Enable encryption at rest for Secrets where appropriate.
- Use PersistentVolumeClaims instead of hardcoding storage.
- Prefer dynamic provisioning using StorageClasses.
- Select reclaim policies that match operational requirements.
- Regularly back up persistent data.

---

# 🎤 Interview Quick Revision

### Q1. Why do we need Services?

Because Pod IP addresses are not permanent.

---

### Q2. Which component performs Service load balancing?

kube-proxy.

---

### Q3. Which DNS service does Kubernetes use?

CoreDNS.

---

### Q4. What is the difference between a ConfigMap and a Secret?

ConfigMaps store non-sensitive configuration.

Secrets store sensitive information.

---

### Q5. Are Secrets encrypted by default?

No.

They are Base64-encoded by default. Encryption at rest is a separate configuration.

---

### Q6. What is the difference between a PV and a PVC?

PV → Storage resource.

PVC → Storage request.

---

### Q7. What is a StorageClass?

A StorageClass defines how Persistent Volumes are dynamically provisioned.

---

### Q8. Why use dynamic provisioning?

It automatically creates storage when a PVC is requested, reducing manual administration.

---

### Q9. Why should applications use DNS names instead of Pod IPs?

DNS names remain stable even when Pods are recreated with new IP addresses.

---

### Q10. What happens when a Pod using a PVC is recreated?

If the PVC remains bound, the new Pod can mount the same Persistent Volume and continue using the existing data.


---

# ❤️ Health Probe Workflow

Kubernetes uses health probes to determine the health and availability of containers.

There are three probe types:

- Startup Probe
- Liveness Probe
- Readiness Probe

---

## Startup Probe

A Startup Probe allows slow-starting applications to initialize before Kubernetes begins health monitoring.

Workflow

```text
Container Starts
       │
       ▼
Startup Probe
       │
       ├───────────────┐
       ▼               ▼
Success             Failure
       │               │
       ▼               ▼
Liveness Starts   Restart Container
```

Use Startup Probes for:

- Java applications
- Spring Boot
- Large ML applications
- Slow database initialization

---

## Liveness Probe

Determines whether a container is still healthy.

Workflow

```text
Running Container
        │
        ▼
Liveness Probe
        │
        ├──────────────┐
        ▼              ▼
Healthy           Unhealthy
        │              │
        ▼              ▼
Continue      Restart Container
```

Examples

- HTTP endpoint
- TCP socket
- Command execution

---

## Readiness Probe

Determines whether a Pod is ready to receive traffic.

Workflow

```text
Pod Running
      │
      ▼
Readiness Probe
      │
      ├──────────────┐
      ▼              ▼
Ready         Not Ready
      │              │
      ▼              ▼
Receive      Removed From
Traffic      Service Endpoints
```

A failed Readiness Probe **does not restart** the container.

Instead, Kubernetes temporarily removes the Pod from the Service until it becomes ready again.

---

# 🏢 Namespace Isolation

Namespaces logically divide a Kubernetes cluster into independent environments.

Example

```text
Cluster

├── default

├── dev

├── qa

├── production
```

Benefits

- Resource isolation
- Team separation
- RBAC boundaries
- Quota management

---

# 🔐 RBAC Authorization Flow

Role-Based Access Control (RBAC) determines **who can perform which actions** on Kubernetes resources.

Workflow

```text
User

↓

API Server

↓

Authentication

↓

Authorization (RBAC)

↓

Role / ClusterRole

↓

RoleBinding / ClusterRoleBinding

↓

Allow

OR

Deny
```

Core Objects

- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding
- ServiceAccount

Production Principle

> Grant the minimum permissions required to perform a task (Principle of Least Privilege).

---

# 🌐 NetworkPolicy Overview

NetworkPolicies define which network traffic is allowed between Pods.

Without a NetworkPolicy:

```text
Pod A

↓

Can communicate

↓

Pod B
```

With a restrictive NetworkPolicy:

```text
Pod A

↓

Allowed?

↓

YES → Traffic Allowed

NO → Traffic Blocked
```

Important

A NetworkPolicy requires a compatible CNI plugin (for example, Calico, Cilium, Antrea, or OVN-Kubernetes) to enforce the rules.

---

# 🚨 Production Troubleshooting Workflow

One of the most valuable skills for a Platform Engineer is troubleshooting production issues using a repeatable methodology.

Workflow

```text
Issue Reported
       │
       ▼
Observe Symptoms
       │
       ▼
Check Pod Status
       │
       ▼
Inspect Events
       │
       ▼
Review Logs
       │
       ▼
Describe Resources
       │
       ▼
Identify Root Cause
       │
       ▼
Apply Fix
       │
       ▼
Validate Recovery
       │
       ▼
Document Findings
```

Useful Commands

```bash
kubectl get pods

kubectl describe pod <pod>

kubectl logs <pod>

kubectl get events --sort-by=.metadata.creationTimestamp

kubectl top pods

kubectl top nodes

kubectl get pvc

kubectl get pv
```

---

# ⭐ Kubernetes Best Practices

## Cluster Design

- Prefer declarative manifests.
- Store manifests in Git.
- Use namespaces to organize workloads.

---

## Security

- Apply the Principle of Least Privilege (RBAC).
- Store sensitive data in Secrets.
- Enable encryption at rest where supported.
- Avoid running privileged containers unless required.

---

## Reliability

- Configure Liveness and Readiness Probes.
- Define Resource Requests and Limits.
- Use Deployments for stateless applications.
- Use StatefulSets for stateful workloads.

---

## Storage

- Prefer PersistentVolumeClaims.
- Use StorageClasses for dynamic provisioning.
- Choose appropriate reclaim policies.
- Regularly back up persistent data.

---

## Operations

- Monitor Events during troubleshooting.
- Review logs before restarting Pods.
- Use rolling updates.
- Document Root Cause Analysis (RCA).
- Continuously monitor cluster health.

---

# 🎤 Senior Platform Engineer Interview Revision

## Kubernetes Architecture

✔ API Server

✔ etcd

✔ Scheduler

✔ Controller Manager

✔ kubelet

✔ kube-proxy

✔ containerd

---

## Workloads

✔ Pods

✔ ReplicaSets

✔ Deployments

✔ StatefulSets

✔ Jobs

✔ CronJobs

✔ Init Containers

✔ Multi-Container Pods

---

## Networking

✔ Services

✔ DNS

✔ NetworkPolicies

---

## Configuration

✔ ConfigMaps

✔ Secrets

---

## Storage

✔ Persistent Volumes

✔ Persistent Volume Claims

✔ StorageClasses

---

## Operations

✔ Resource Requests

✔ Resource Limits

✔ OOMKilled

✔ CrashLoopBackOff

✔ ImagePullBackOff

✔ Pending Pods

✔ Kubernetes Events

✔ Container Logs

✔ Health Probes

✔ Root Cause Analysis (RCA)

---

## Security

✔ Namespaces

✔ RBAC

✔ ServiceAccounts

---

# 📚 Recommended Learning Path

```
Kubernetes Architecture
        │
        ▼
Pods & Deployments
        │
        ▼
Services & Networking
        │
        ▼
ConfigMaps & Secrets
        │
        ▼
Persistent Storage
        │
        ▼
Security (Namespaces & RBAC)
        │
        ▼
Resource Management
        │
        ▼
Production Troubleshooting
        │
        ▼
Helm
        │
        ▼
OpenShift
        │
        ▼
GitOps
        │
        ▼
Observability
        │
        ▼
AI Infrastructure
```

---

# 🏁 Conclusion

Kubernetes is far more than a container orchestrator—it is a distributed system that continuously reconciles the desired state with the current state of applications and infrastructure.

Understanding how the API Server, etcd, Scheduler, Controller Manager, kubelet, kube-proxy, and container runtime interact provides the foundation for operating production Kubernetes clusters.

Combined with hands-on experience from the accompanying labs, this architecture guide establishes a strong knowledge base for Platform Engineering, Site Reliability Engineering (SRE), OpenShift administration, and cloud-native infrastructure.

---

# 📖 Related Documentation

- `README.md` – Kubernetes Foundation module overview
- `CHANGELOG.md` – Module release history
- `labs/` – Hands-on Kubernetes exercises
- `lab-13-production-troubleshooting/` – Production incident scenarios, observations, and interview guide
