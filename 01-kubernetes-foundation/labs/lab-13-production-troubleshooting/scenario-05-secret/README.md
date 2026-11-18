# Lab 13 - Scenario 05: Secret Misconfiguration

## Overview

This troubleshooting scenario demonstrates how to identify and resolve application startup failures caused by a missing Kubernetes Secret.

In production environments, Secrets are used to securely store sensitive information such as passwords, API keys, tokens, and certificates. If a Deployment references a Secret that does not exist, the Pod cannot start successfully and enters the `CreateContainerConfigError` state.

---

## Learning Objectives

After completing this scenario, you will be able to:

- Understand the purpose of Kubernetes Secrets
- Troubleshoot `CreateContainerConfigError`
- Identify missing Secret dependencies
- Create and apply Secrets
- Restart workloads after configuration changes
- Verify Secret values injected as environment variables
- Apply production troubleshooting practices

---

## Prerequisites

- Kubernetes cluster (K3s)
- kubectl configured
- Basic understanding of Deployments and Secrets

---

## Repository Structure

```text
scenario-05-secret/
├── README.md
├── secret-error.yaml
├── secret.yaml
└── screenshots/
```

---

## Production Scenario

A newly deployed application fails to start because it references a Secret that has not been created.

Typical production causes include:

- Missing Secret manifest
- Incorrect Secret name
- Wrong namespace
- GitOps synchronization issues
- CI/CD deployment sequencing problems
- Accidental Secret deletion

---

## Architecture

```
Deployment
      │
      ▼
References Secret
      │
      ▼
Secret Missing
      │
      ▼
CreateContainerConfigError
      │
      ▼
Investigate
      │
      ▼
Create Secret
      │
      ▼
Restart Deployment
      │
      ▼
Running Pod
```

---

# Step 1 – Deploy Application

Apply the deployment manifest.

```bash
kubectl apply -f secret-error.yaml
```

---

# Step 2 – Verify Pod Status

```bash
kubectl get pods
```

Expected:

```text
STATUS
CreateContainerConfigError
```

---

# Step 3 – Investigate

Describe the Pod.

```bash
kubectl describe pod <pod-name>
```

Expected Event:

```text
Error: secret "app-secret" not found
```

Verify existing Secrets:

```bash
kubectl get secrets
```

Initially, the required Secret should not exist.

---

# Root Cause Analysis

The Deployment references:

```text
app-secret
```

However, the Secret is missing from the namespace.

Since Kubernetes cannot retrieve the required Secret values, it cannot create the container configuration, resulting in a `CreateContainerConfigError`.

---

# Resolution

Create the missing Secret.

```bash
kubectl apply -f secret.yaml
```

No restart is strictly needed: the kubelet keeps retrying a Pod in `CreateContainerConfigError`, so it starts on its own once the missing object exists. A restart just makes it immediate:

```bash
kubectl rollout restart deployment secret-demo
```

---

# Validation

Verify the Pod is running.

```bash
kubectl get pods
```

Verify the environment variables.

```bash
kubectl exec -it <pod-name> -- env | grep DB
```

Example output:

```text
DB_USERNAME=admin
DB_PASSWORD=MySecurePassword123
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
Secret not found
          │
          ▼
kubectl get secrets
          │
          ▼
Create Secret
          │
          ▼
Restart Deployment
          │
          ▼
Verify Application
```

---

# Production Best Practices

- Never store sensitive information in ConfigMaps.
- Use Kubernetes Secrets for credentials, certificates, and tokens.
- Manage Secrets through GitOps using encrypted solutions such as Sealed Secrets or External Secrets Operator.
- Avoid committing real production Secrets into Git repositories.
- Rotate Secrets regularly and follow the principle of least privilege.
- Store Secrets in dedicated secret management systems (e.g., HashiCorp Vault or cloud-native secret managers) for production environments.

---

# Why Learn Both Imperative and Declarative Approaches?

As a Platform Engineer, it is important to understand **both imperative and declarative ways** of managing Kubernetes resources. Each approach serves a different purpose in real-world production environments.

---

## Imperative Approach

The imperative approach uses `kubectl` commands to perform an action immediately.

Example:

```bash
kubectl create secret generic app-secret \
  --from-literal=username=admin \
  --from-literal=password=MySecurePassword123
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
kind: Secret
metadata:
  name: app-secret
type: Opaque
stringData:
  username: admin
  password: MySecurePassword123
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

### What is a Kubernetes Secret?

A Secret is a Kubernetes object used to store sensitive information such as passwords, API keys, tokens, and certificates.

---

### What causes `CreateContainerConfigError`?

Common causes include:

- Missing Secret
- Missing ConfigMap
- Incorrect Secret key
- Invalid environment variable references
- Incorrect volume configuration

---

### What is the difference between ConfigMap and Secret?

| ConfigMap | Secret |
|-----------|--------|
| Stores non-sensitive configuration | Stores sensitive information |
| Values stored as plain text | Values stored as Base64-encoded data in Kubernetes (encoding, not encryption) |
| Used for application configuration | Used for credentials, certificates, and tokens |

---

### How do you troubleshoot Secret issues?

1. Check Pod status.
2. Describe the Pod.
3. Review Events.
4. Verify Secret existence.
5. Validate Secret keys.
6. Create or update the Secret.
7. Restart the workload.
8. Verify application functionality.

---

## Key Commands

```bash
kubectl get pods

kubectl describe pod <pod-name>

kubectl get secrets

kubectl apply -f secret.yaml

kubectl rollout restart deployment secret-demo

kubectl exec -it <pod-name> -- env | grep DB
```

---

# Screenshots

Evidence captured during this scenario:

- [createcontainerconfigerror](screenshots/01-createcontainerconfigerror.png)
- [describe pod](screenshots/02-describe-pod.png)
- [secret list](screenshots/03-secret-list.png)
- [secret created](screenshots/04-secret-created.png)
- [pod running](screenshots/05-pod-running.png)
- [env verification](screenshots/06-env-verification.png)

---

# Key Takeaways

- Secrets securely store sensitive application data.
- Missing Secrets prevent Pods from starting successfully.
- `kubectl describe pod` is the fastest way to identify Secret-related startup failures.
- Kubernetes Events provide the root cause of Secret configuration issues.
- Production Secrets should be managed securely and never committed to Git in plain form.

---

## Lab Status

**Status:** ✅ Completed

**Scenario:** Secret Misconfiguration

**Difficulty:** Intermediate

**Production Relevance:** ⭐⭐⭐⭐⭐
