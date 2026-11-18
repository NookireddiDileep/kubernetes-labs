# Lab 13 - Scenario 04: ConfigMap Misconfiguration

## Overview

This troubleshooting scenario demonstrates how to identify and resolve application startup failures caused by a missing Kubernetes ConfigMap.

In production environments, ConfigMaps are commonly used to externalize application configuration. If a Deployment references a ConfigMap that does not exist, the Pod cannot start successfully and enters the `CreateContainerConfigError` state.

---

## Learning Objectives

After completing this scenario, you will be able to:

- Understand the purpose of Kubernetes ConfigMaps
- Troubleshoot `CreateContainerConfigError`
- Identify missing ConfigMap dependencies
- Create and apply ConfigMaps
- Restart workloads after configuration changes
- Verify environment variables inside running containers
- Apply production troubleshooting practices

---

## Prerequisites

- Kubernetes cluster (K3s)
- kubectl configured
- Basic understanding of Deployments and ConfigMaps

---

## Repository Structure

```text
scenario-04-configmap/
├── README.md
├── configmap-error.yaml
├── configmap.yaml
└── screenshots/
```

---

## Production Scenario

A newly deployed application fails to start because it references a ConfigMap that has not been created.

Typical production causes include:

- Incorrect GitOps deployment
- Missing Kubernetes manifests
- Wrong namespace
- Manual deletion of ConfigMaps
- CI/CD deployment sequencing issues

---

## Architecture

```
Deployment
      │
      ▼
References ConfigMap
      │
      ▼
ConfigMap Missing
      │
      ▼
CreateContainerConfigError
      │
      ▼
Investigate
      │
      ▼
Create ConfigMap
      │
      ▼
Restart Deployment
      │
      ▼
Running Pod
```

---

# Step 1 — Deploy Application

Apply the deployment manifest.

```bash
kubectl apply -f configmap-error.yaml
```

---

# Step 2 — Verify Pod Status

```bash
kubectl get pods
```

Expected:

```text
STATUS
CreateContainerConfigError
```

---

# Step 3 — Investigate

Describe the Pod.

```bash
kubectl describe pod <pod-name>
```

Expected Event:

```text
Error: configmap "app-config" not found
```

Verify ConfigMaps:

```bash
kubectl get configmap
```

Initially:

```text
kube-root-ca.crt
```

---

# Root Cause Analysis

The Deployment references:

```text
app-config
```

but the ConfigMap does not exist.

As a result, Kubernetes cannot inject the required environment variables into the container.

---

# Resolution

Create the missing ConfigMap.

```bash
kubectl apply -f configmap.yaml
```

No restart is strictly needed: the kubelet keeps retrying a Pod in `CreateContainerConfigError`, so it starts on its own once the missing object exists. A restart just makes it immediate:

```bash
kubectl rollout restart deployment configmap-demo
```

---

# Validation

Verify the Pod is running.

```bash
kubectl get pods
```

Verify environment variables.

```bash
kubectl exec -it <pod-name> -- env | grep APP
```

Example output:

```text
APP_NAME=platform-engineer-lab
```

---

# Production Troubleshooting Workflow

```
Application Startup Failure
          │
          ▼
kubectl get pods
          │
          ▼
CreateContainerConfigError
          │
          ▼
kubectl describe pod
          │
          ▼
ConfigMap not found
          │
          ▼
kubectl get configmap
          │
          ▼
Create ConfigMap
          │
          ▼
Restart Deployment
          │
          ▼
Verify Application
```

---

# Production Best Practices

- Store application configuration in ConfigMaps.
- Keep sensitive information in Secrets instead of ConfigMaps.
- Manage ConfigMaps using GitOps or Infrastructure as Code.
- Validate required ConfigMaps before deployment.
- Use consistent naming conventions across environments.
- Avoid manual configuration changes in production clusters.

---
# Why Learn Both Imperative and Declarative Approaches?

As a Platform Engineer, it is important to understand **both imperative and declarative ways** of managing Kubernetes resources. Each approach serves a different purpose in real-world production environments.

---

## Imperative Approach

The imperative approach uses `kubectl` commands to perform an action immediately.

Example:

```bash
kubectl create configmap app-config \
  --from-literal=APP_ENV=production \
  --from-literal=LOG_LEVEL=info
```

This method is commonly used during:

- Production incidents
- Emergency troubleshooting
- Rapid validation
- Temporary operational fixes

### Advantages

- Fast and simple
- Ideal during outages
- Useful for troubleshooting
- Excellent for learning and testing

### Limitations

Imperative changes are **not automatically version-controlled**.

Without updating the corresponding YAML manifests, there is:

- No Git history
- No peer review
- No audit trail
- No reproducible deployment

For this reason, imperative commands should generally be considered **temporary operational actions**.

---

## Declarative Approach

The declarative approach defines the desired state of the cluster using YAML manifests.

Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
  LOG_LEVEL: info
```

Apply the resource:

```bash
kubectl apply -f secret.yaml
```

This approach is preferred for:

- Infrastructure as Code (IaC)
- GitOps workflows
- CI/CD automation
- Production deployments
- Disaster recovery
- Multi-cluster management

### Advantages

- Version controlled
- Peer reviewed
- Auditable
- Repeatable
- Easy to recreate environments
- Supports automated deployments

---

## Production Workflow

A mature Platform Engineering team typically uses **both approaches** together.

```
Production Incident
        │
        ▼
Rapid Operational Fix
(kubectl command)
        │
        ▼
Service Restored
        │
        ▼
Update YAML Manifest
        │
        ▼
Commit to Git
        │
        ▼
Pull Request & Code Review
        │
        ▼
GitOps / CI-CD Deployment
        │
        ▼
Cluster State Matches Git
```

This workflow balances **fast incident response** with **long-term operational consistency**.

---

## Interview Perspective

A Senior Platform Engineer is expected to understand when each approach is appropriate.

During a production incident, an engineer may use imperative commands to restore service quickly. Once the incident is resolved, the change should be captured in a declarative manifest, committed to Git, and managed through a GitOps or CI/CD pipeline.

This ensures that the cluster remains:

- Consistent
- Auditable
- Reproducible
- Recoverable

---

## Production Note

This lab uses sample credentials for demonstration purposes.

In production environments, sensitive information should **not** be stored directly in Git repositories. Organizations typically use secure secret management solutions such as:

- Bitnami Sealed Secrets
- External Secrets Operator
- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager

These tools securely manage sensitive data while allowing Kubernetes workloads to consume Secrets safely.

---

# Common Interview Questions

### What is a ConfigMap?

A ConfigMap stores non-sensitive configuration data that can be consumed by Pods as environment variables, command-line arguments, or mounted files.

---

### What causes CreateContainerConfigError?

Common causes include:

- Missing ConfigMap
- Missing Secret
- Invalid environment variable references
- Incorrect volume configuration

---

### How do you troubleshoot ConfigMap issues?

1. Check Pod status.
2. Describe the Pod.
3. Review Events.
4. Verify ConfigMap existence.
5. Create or update the ConfigMap.
6. Restart the workload.
7. Validate application functionality.

---

## Key Commands

```bash
kubectl get pods

kubectl describe pod <pod-name>

kubectl get configmap

kubectl apply -f configmap.yaml

kubectl rollout restart deployment configmap-demo

kubectl exec -it <pod-name> -- env | grep APP
```

---

# Screenshots

Evidence captured during this scenario:

- [createcontainerconfigerror](screenshots/01-createcontainerconfigerror.png)
- [describe pod](screenshots/02-describe-pod.png)
- [configmap list empty](screenshots/03-configmap-list-empty.png)
- [configmap created](screenshots/04-configmap-created.png)
- [pod running](screenshots/05-pod-running.png)
- [env verification](screenshots/06-env-verification.png)

---

# Key Takeaways

- ConfigMaps externalize application configuration.
- Missing ConfigMaps prevent Pods from starting.
- `kubectl describe pod` is the primary troubleshooting command.
- Kubernetes Events provide the root cause quickly.
- Configuration should be version-controlled and deployed through GitOps.

---

## Lab Status

**Status:** ✅ Completed

**Scenario:** ConfigMap Misconfiguration

**Difficulty:** Intermediate

**Production Relevance:** ⭐⭐⭐⭐⭐
