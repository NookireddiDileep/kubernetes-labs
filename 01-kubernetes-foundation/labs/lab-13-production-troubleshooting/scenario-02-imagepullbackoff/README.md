# Lab 13 – Kubernetes Production Troubleshooting

## Scenario 02 – ImagePullBackOff

---

# Objective

The objective of this lab is to understand how Kubernetes behaves when a container image cannot be pulled from the container registry and how to troubleshoot an `ImagePullBackOff` error using standard Kubernetes debugging techniques.

This scenario simulates one of the most common production deployment failures caused by an incorrect container image tag.

---

# Learning Objectives

After completing this lab, you will be able to:

* Understand the Image Pull lifecycle.
* Differentiate between `ErrImagePull` and `ImagePullBackOff`.
* Troubleshoot image download failures.
* Interpret Kubernetes Events related to image pulls.
* Recover workloads by correcting container image references.
* Know which Pod fields can be changed in place (image) and which require recreating the Pod.

---

# Prerequisites

* Kubernetes Cluster (K3s)
* kubectl configured
* Internet connectivity to pull container images
* Basic understanding of Kubernetes Pods

---

# Directory Structure

```text
scenario-02-imagepullbackoff/
├── README.md
├── imagepull.yaml
└── screenshots/
    ├── 01-pod-created.png
    ├── 02-errimagepull.png
    ├── 03-imagepullbackoff.png
    ├── 04-describe-pod.png
    ├── 05-logs-error.png
    └── 06-pod-running.png
```

---

# Scenario

A developer accidentally deploys an application using a non-existent image tag.

```yaml
image: nginx:wrongtag
```

Since the image does not exist in the registry, Kubernetes cannot download it.

The Pod is successfully scheduled onto a node, but container creation never begins.

---

# Step 1 – Create the Pod

Apply the manifest.

```bash
kubectl apply -f imagepull.yaml
```

Verify the Pod.

```bash
kubectl get pods
```

Typical lifecycle:

```text
ContainerCreating

↓

ErrImagePull

↓

ImagePullBackOff
```

---

# Step 2 – Investigate the Failure

Describe the Pod.

```bash
kubectl describe pod imagepull-demo
```

Typical Events section:

```text
Failed to pull image "nginx:wrongtag"

manifest unknown

ErrImagePull

ImagePullBackOff
```

These Events provide the exact reason why Kubernetes cannot create the container.

---

# Step 3 – Check Container Logs

Retrieve logs.

```bash
kubectl logs imagepull-demo
```

Output:

```text
Error from server (BadRequest):
container "nginx" in pod "imagepull-demo" is waiting to start:
trying and failing to pull image
```

Unlike CrashLoopBackOff, the application never starts.

Therefore no application logs are available.

---

# Root Cause Analysis

The specified image tag does not exist.

```yaml
image: nginx:wrongtag
```

Kubernetes repeatedly attempts to download the image.

After multiple failed attempts, Kubernetes applies an exponential back-off delay before retrying.

The Pod enters the `ImagePullBackOff` state.

---

# Step 4 – Fix the Image

Update the image.

```yaml
image: nginx:latest
```

`image` is one of the few Pod fields that **can** be changed in place (`kubectl set image pod/<name> <container>=<image>` or `kubectl apply` with the new tag). For a clean run, the Pod was deleted and recreated instead.

Delete the existing Pod.

```bash
kubectl delete pod imagepull-demo
```

Recreate it.

```bash
kubectl apply -f imagepull.yaml
```

Verify.

```bash
kubectl get pods
```

Expected output:

```text
NAME             READY   STATUS    RESTARTS
imagepull-demo   1/1     Running   0
```

---

# Troubleshooting Workflow

```text
Pod Scheduled

      │

      ▼

Image Pull

      │

      ▼

Registry Error

      │

      ▼

ErrImagePull

      │

      ▼

Repeated Retry

      │

      ▼

ImagePullBackOff
```

---

# Production Best Practices

* Verify image names and tags before deployment.
* Use immutable version tags instead of `latest` in production.
* Validate image availability in the container registry.
* Configure `imagePullSecrets` for private registries.
* Monitor Events using `kubectl describe`.
* Use Deployment rolling updates instead of standalone Pods.
* Store image versions in Git for traceability.

---

# Common Causes of ImagePullBackOff

* Incorrect image name
* Invalid image tag
* Private registry authentication failure
* Missing imagePullSecrets
* Registry unavailable
* Network connectivity issues
* Registry rate limiting
* Image deleted from registry

---

# Interview Questions

## Basic

1. What is ImagePullBackOff?
2. What causes ImagePullBackOff?
3. What is ErrImagePull?
4. Which command provides the root cause?
5. Why are logs unavailable?

## Intermediate

6. Difference between ErrImagePull and ImagePullBackOff?
7. How does Kubernetes retry image downloads?
8. What information is available in Pod Events?
9. How would you troubleshoot a private registry failure?
10. What is the purpose of imagePullSecrets?

## Advanced

11. How would you prevent ImagePullBackOff in production?
12. Why should production workloads avoid the `latest` tag?
13. How do Kubernetes nodes cache images?
14. What happens if the registry is temporarily unavailable?
15. How would you debug intermittent image pull failures across multiple nodes?

---

# Screenshots

Evidence captured during this scenario:

- [describe pod imagepull demo](screenshots/describe%20pod%20imagepull-demo.PNG)
- [imagepulloff pod created and pod status](screenshots/imagepulloff%20pod%20created%20and%20pod%20status.PNG)
- [pod is running post fix](screenshots/pod%20is%20running%20post%20fix.PNG)
- [verify pod logs](screenshots/verify%20pod%20logs.PNG)

---

# Key Takeaways

* `ErrImagePull` indicates the initial image pull failure.
* `ImagePullBackOff` indicates Kubernetes is retrying image downloads with an exponential back-off strategy.
* `kubectl describe pod` is the primary troubleshooting command for image pull failures.
* Application logs are unavailable because the container never starts.
* Correcting the image reference (in place, or by recreating the Pod) resolves the issue.
* Using immutable image tags and validating registry access helps prevent production deployment failures.

