# Lab 03 – Kubernetes ConfigMaps

## Objective

The objective of this lab is to understand how Kubernetes ConfigMaps are used to externalize application configuration from container images. You will create a ConfigMap, inject configuration into a Deployment using environment variables, and verify that the application can access the configuration at runtime.

---

## Learning Objectives

By completing this lab, you will learn how to:

* Create Kubernetes ConfigMaps
* Understand the purpose of externalized configuration
* Use the `data` field to store key-value pairs
* Inject ConfigMap values into Pods as environment variables
* Verify ConfigMap usage inside a running container
* Understand production configuration management best practices

---

## Lab Architecture

```text
                 Kubernetes ConfigMap
                      app-config
                           │
          ┌────────────────┴────────────────┐
          │                                 │
      APP_NAME                       APP_ENV
          │                                 │
          └────────────────┬────────────────┘
                           │
                     Deployment
                           │
                     2 NGINX Pods
                           │
               Environment Variables
```

---

## Files

```text
lab-03-configmaps/
├── README.md
├── configmap.yaml
├── deployment.yaml
├── observations.md
└── screenshots/
    └── README.md
```

---

## Prerequisites

* Running Kubernetes cluster (K3s, Minikube, Kind, or OpenShift)
* kubectl configured
* Basic understanding of Pods and Deployments

---

## Lab Steps

### Step 1 – Create the ConfigMap

Apply the ConfigMap manifest.

```bash
kubectl apply -f configmap.yaml
```

Verify the ConfigMap.

```bash
kubectl get configmaps
```

Describe the ConfigMap.

```bash
kubectl describe configmap app-config
```

---

### Step 2 – Deploy the Application

Create the Deployment that consumes the ConfigMap.

```bash
kubectl apply -f deployment.yaml
```

Verify the Deployment.

```bash
kubectl get deployments
```

Verify the Pods.

```bash
kubectl get pods
```

---

### Step 3 – Verify Environment Variables

Identify a running Pod.

```bash
kubectl get pods
```

Open a shell inside the Pod.

```bash
kubectl exec -it <pod-name> -- /bin/sh
```

Display the injected environment variables.

```bash
echo $APP_NAME

echo $APP_ENV
```

Exit the Pod.

```bash
exit
```

---

### Step 4 – View the ConfigMap

Display the ConfigMap in YAML format.

```bash
kubectl get configmap app-config -o yaml
```

---

### Step 5 – Cleanup

Delete all resources.

```bash
kubectl delete -f deployment.yaml

kubectl delete -f configmap.yaml
```

---

## Expected Output

After completing this lab, you should observe:

* ConfigMap successfully created
* ConfigMap visible using `kubectl get configmaps`
* Deployment successfully created
* Pods running successfully
* Environment variables populated from the ConfigMap
* Configuration externalized from the application image

---

## Screenshots

```text
lab-03-configmaps/
├── configmap.yaml
├── deployment.yaml
├── observations.md
├── README.md
└── screenshots/
    ├── 01-configmap-created-configured.png
    ├── 02-configmap-list.png
    ├── 03-configmap-describe.png
    ├── 04-deployment-created-unchanged.png
    ├── 05-pod-running.png
    ├── 06-env-variables.png
    ├── README.md
    └── resource-overview.png
```

---

## Production Best Practices

* Store non-sensitive configuration in ConfigMaps.
* Never store passwords, API keys, or tokens in ConfigMaps; use Secrets instead.
* Keep application configuration separate from container images.
* Use meaningful names for ConfigMaps.
* Version configuration changes through Git for traceability.
* Restart or roll out Deployments when configuration changes need to take effect.

---

## Key Concepts Learned

* Kubernetes ConfigMap
* Externalized Configuration
* Environment Variables
* Declarative YAML
* Configuration Management
* Application Configuration
* Pod Environment Variables
* kubectl Resource Management

---

## Interview Questions

**1. What is a Kubernetes ConfigMap?**

An API object that holds non-confidential configuration as key-value pairs or files, so it can be changed without rebuilding the image.

**2. How is a ConfigMap different from a Secret?**

Both hold configuration, but Secrets are meant for sensitive data: they are base64-encoded, can be encrypted at rest, are treated separately by RBAC and are kept in memory (tmpfs) when mounted. A ConfigMap is plain text.

**3. What types of data can be stored in a ConfigMap?**

Strings in `data` (single values or whole files such as `nginx.conf`) and binary content in `binaryData`. The total size is limited to 1 MiB.

**4. How can a Pod consume a ConfigMap?**

As environment variables (`valueFrom.configMapKeyRef` or `envFrom`), as files through a volume, or as command-line arguments built from those environment variables.

**5. Can a ConfigMap be mounted as a volume?**

Yes. Each key becomes a file in the mounted directory, or you can pick specific keys with `items`.

**6. How do ConfigMap updates affect running Pods?**

Environment variables are read only at container start, so Pods must be restarted (for example `kubectl rollout restart`). Mounted volumes are updated by the kubelet after a short delay, except for `subPath` mounts, which never update. Immutable ConfigMaps never change.

**7. What are common production use cases for ConfigMaps?**

Application settings per environment, feature flags, config files for NGINX or Prometheus, and connection endpoints that aren't secret.

**8. When should you use ConfigMaps instead of Secrets?**

Whenever the value is not sensitive. Anything that grants access (passwords, tokens, keys, certificates) belongs in a Secret or an external secret manager.

**9. How do you inspect a ConfigMap?**

`kubectl get configmap <name> -o yaml` or `kubectl describe configmap <name>`, and inside the Pod with `env` or by reading the mounted files.

**10. What are the best practices for managing application configuration?**

Keep it in Git and apply it declaratively, separate config from images, keep secrets out of ConfigMaps, version or mark ConfigMaps immutable for safe rollouts, and roll Pods when config changes.

---

## References

* Official Kubernetes Documentation
* Kubernetes ConfigMaps Documentation
* CNCF Kubernetes Best Practices

---

## Conclusion

This lab demonstrated how Kubernetes ConfigMaps enable applications to externalize configuration from container images. You learned how to create, inspect, and consume ConfigMaps using environment variables, making applications easier to configure, maintain, and deploy across different environments while following production-ready Kubernetes practices.

