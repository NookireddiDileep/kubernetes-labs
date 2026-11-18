# Lab 04 – Kubernetes Secrets

## Objective

The objective of this lab is to understand how Kubernetes Secrets securely store and manage sensitive information such as usernames, passwords, API keys, and tokens. This lab demonstrates creating Secrets, injecting them into Pods as environment variables, and verifying their usage inside a running container.

---

## Learning Objectives

By completing this lab, you will learn how to:

* Create Kubernetes Secrets
* Understand the difference between `data` and `stringData`
* Store sensitive application credentials securely
* Inject Secrets into containers as environment variables
* Inspect and verify Secret resources
* Decode Base64-encoded Secret values
* Apply security best practices for Secrets

---

## Lab Architecture

```
                Kubernetes Secret
                       │
                       │
             app-secret (Opaque)
                       │
          ┌────────────┴────────────┐
          │                         │
     USERNAME                  PASSWORD
          │                         │
          └────────────┬────────────┘
                       │
                 Deployment
                       │
                 2 NGINX Pods
                       │
          Environment Variables
```

---

## Files

```
lab-04-secrets/
├── README.md
├── secret.yaml
├── deployment.yaml
├── observations.md
└── screenshots/
    └── README.md
```

---

## Prerequisites

* Kubernetes cluster (K3s, Minikube, Kind, or OpenShift)
* kubectl configured
* Basic understanding of Pods and Deployments

---

## Lab Steps

### Step 1 – Create a Secret

Create an Opaque Secret using the `stringData` field.

```bash
kubectl apply -f secret.yaml
```

Verify the Secret:

```bash
kubectl get secrets
```

Describe the Secret:

```bash
kubectl describe secret app-secret
```

---

### Step 2 – View the Secret

Display the Secret in YAML format.

```bash
kubectl get secret app-secret -o yaml
```

Observe that Kubernetes stores values under the `data` section as Base64-encoded strings.

---

### Step 3 – Decode Secret Values

Decode the username:

```bash
echo YWRtaW4= | base64 -d
```

Decode the password:

```bash
echo bXlTZWN1cmVQYXNzd29yZDEyMw== | base64 -d
```

---

### Step 4 – Deploy an Application

Deploy an NGINX application that consumes Secret values as environment variables.

```bash
kubectl apply -f deployment.yaml
```

Verify the Deployment:

```bash
kubectl get deployment
```

Verify the Pods:

```bash
kubectl get pods
```

---

### Step 5 – Verify Secret Inside the Pod

Open a shell inside one of the running Pods.

```bash
kubectl exec -it <pod-name> -- /bin/sh
```

Check the injected environment variables.

```bash
echo $USERNAME

echo $PASSWORD
```

Exit the container.

```bash
exit
```

---

### Step 6 – Cleanup

Delete all created resources.

```bash
kubectl delete -f deployment.yaml

kubectl delete -f secret.yaml
```

---

## Expected Output

After completing this lab, you should observe:

* Secret successfully created
* Secret listed using `kubectl get secrets`
* Secret values visible as Base64-encoded strings in YAML
* Deployment successfully created
* Pods running successfully
* Secret values available inside the container as environment variables

---

## Screenshots

```
screenshots/
├── 01-secret-created.png
├── 02-secret-list.png
├── 03-secret-describe.png
├── 04-secret-yaml.png
├── 05-secret-decode.png
├── 06-deployment-created.png
├── 07-secret-env-inside-pod.png
```

---

## Production Best Practices

* Never store plaintext passwords in Git repositories.
* Use external Secret management solutions such as HashiCorp Vault or cloud-native secret managers for production workloads.
* Enable encryption at rest for Kubernetes Secrets in etcd.
* Apply Role-Based Access Control (RBAC) to restrict Secret access.
* Rotate credentials regularly.
* Follow the principle of least privilege.

---

## Key Concepts Learned

* Kubernetes Secret
* Opaque Secret
* `stringData`
* `data`
* Base64 encoding
* Environment variable injection
* Secret lifecycle
* Security best practices

---

## Interview Questions

1. What is a Kubernetes Secret?
2. How is a Secret different from a ConfigMap?
3. Is Base64 encoding considered encryption?
4. What are the different Secret types in Kubernetes?
5. How can Secrets be consumed by Pods?
6. What is the difference between `data` and `stringData`?
7. Can Secrets be mounted as files?
8. How are Secrets stored in etcd?
9. How do you secure Secrets in a production Kubernetes cluster?
10. What are common methods for Secret rotation?

---

## References

* Kubernetes Secrets
* Kubernetes Documentation
* Kubernetes Security Best Practices

---

## Conclusion

This lab demonstrated how Kubernetes Secrets provide a secure mechanism for storing and managing sensitive configuration data. You learned how to create, inspect, decode, and consume Secrets within Pods while following recommended security practices used in production Kubernetes environments.

