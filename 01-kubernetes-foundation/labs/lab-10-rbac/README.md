# Lab 10 – Kubernetes Role-Based Access Control (RBAC)

## Objective

The objective of this lab is to understand Kubernetes Role-Based Access Control (RBAC) by creating a ServiceAccount, Role, and RoleBinding, and validating permissions using `kubectl auth can-i`.

RBAC is the primary authorization mechanism in Kubernetes and is essential for implementing secure, least-privilege access to cluster resources.

---

# Learning Objectives

After completing this lab, you will be able to:

* Understand Authentication vs Authorization
* Create ServiceAccounts
* Create Roles
* Create RoleBindings
* Verify permissions using `kubectl auth can-i`
* Apply the Principle of Least Privilege
* Understand namespace-scoped RBAC
* Troubleshoot Kubernetes authorization issues

---

# Lab Environment

| Component               | Value                       |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Container Runtime       | containerd                  |
| Cloud Platform          | Google Cloud Platform (GCP) |
| Operating System        | Ubuntu 24.04 LTS            |

---

# Repository Structure

```text
lab-10-rbac/
│
├── README.md
├── observations.md
├── serviceaccount.yaml
├── role.yaml
├── rolebinding.yaml
└── screenshots/
    └── README.md
```

---

# RBAC Components

Kubernetes RBAC consists of four primary components:

* ServiceAccount
* Role
* RoleBinding
* Authorization Check

Relationship:

```text
ServiceAccount
      │
      ▼
RoleBinding
      │
      ▼
Role
      │
      ▼
Permissions
```

---

# Part A – ServiceAccount

Created a ServiceAccount named:

```text
pod-reader
```

Manifest:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: pod-reader
```

Applied using:

```bash
kubectl apply -f serviceaccount.yaml
```

Verified using:

```bash
kubectl get serviceaccounts
kubectl describe serviceaccount pod-reader
```

Observation:

* ServiceAccount created successfully.
* No automatic Secret was created, which is expected in modern Kubernetes versions using the TokenRequest API.

---

# Part B – Role

Created a namespace-scoped Role that grants read-only access to Pods.

Manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader-role
rules:
- apiGroups: [""]
  resources:
  - pods
  verbs:
  - get
  - list
  - watch
```

Applied using:

```bash
kubectl apply -f role.yaml
```

Verified using:

```bash
kubectl get roles
kubectl describe role pod-reader-role
```

Permissions granted:

* get
* list
* watch

---

# Part C – RoleBinding

Bound the ServiceAccount to the Role.

Manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
subjects:
- kind: ServiceAccount
  name: pod-reader
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-reader-role
```

Applied using:

```bash
kubectl apply -f rolebinding.yaml
```

Verified using:

```bash
kubectl get rolebindings
kubectl describe rolebinding pod-reader-binding
```

Observation:

The ServiceAccount was successfully bound to the Role.

---

# Part D – Permission Verification

Verified authorization using:

```bash
kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader

kubectl auth can-i delete pods --as=system:serviceaccount:default:pod-reader
```

Results:

| Action      | Result |
| ----------- | ------ |
| Get Pods    | ✅ Yes  |
| Delete Pods | ❌ No   |

This confirms the RBAC policy correctly enforces least-privilege access.

---

# Authentication vs Authorization

| Authentication                       | Authorization                             |
| ------------------------------------ | ----------------------------------------- |
| Identifies who is making the request | Determines what actions are permitted     |
| User, Group, or ServiceAccount       | RBAC evaluates Roles and Bindings         |
| Performed first                      | Performed after successful authentication |

---

# Role vs ClusterRole

| Role                              | ClusterRole                                 |
| --------------------------------- | ------------------------------------------- |
| Namespace-scoped                  | Cluster-scoped                              |
| Applies within a single namespace | Can apply across the cluster                |
| Used with RoleBinding             | Used with ClusterRoleBinding or RoleBinding |

---

# Production Use Cases

RBAC is commonly used to:

* Restrict developer access
* Secure CI/CD pipelines
* Control application ServiceAccount permissions
* Enforce least-privilege security
* Separate operational responsibilities
* Protect sensitive resources such as Secrets

---

# Best Practices

* Grant only the minimum permissions required.
* Prefer namespace-scoped Roles whenever possible.
* Assign permissions to ServiceAccounts rather than default identities.
* Regularly review RoleBindings and ClusterRoleBindings.
* Use `kubectl auth can-i` to verify effective permissions before troubleshooting.

---

# Interview Questions

**1. What is RBAC?**

Role-Based Access Control: the authorizer that decides what an authenticated user or ServiceAccount may do, using Roles (permissions) and bindings (who gets them).

**2. What is a ServiceAccount?**

An identity for processes running in Pods. Pods use it to authenticate to the API server with a short-lived token injected by the kubelet.

**3. What is the difference between Role and ClusterRole?**

A Role grants permissions inside one namespace. A ClusterRole is cluster-wide: it can cover cluster-scoped resources (nodes, PVs) or be reused across namespaces.

**4. What is the difference between RoleBinding and ClusterRoleBinding?**

A RoleBinding grants a Role or ClusterRole within one namespace. A ClusterRoleBinding grants a ClusterRole across the whole cluster.

**5. What does `kubectl auth can-i` do?**

Asks the API server whether an identity is allowed to do an action, for example `kubectl auth can-i delete pods --as=system:serviceaccount:default:pod-reader`, which answered `no` in this lab.

**6. What is the Principle of Least Privilege?**

Give each identity only the permissions it needs, nothing more. Here, the `pod-reader` ServiceAccount got only `get`, `list` and `watch` on Pods.

**7. Why are ServiceAccounts preferred for workloads?**

They are namespaced, managed through the API and get short-lived tokens, so each workload can have its own tightly scoped permissions instead of sharing a powerful human credential.

**8. How do you troubleshoot an RBAC "Forbidden" error?**

Read the message (it names the user, verb, resource and namespace), confirm with `kubectl auth can-i ... --as`, find the bindings for that identity with `kubectl get rolebindings,clusterrolebindings -A -o wide`, then add the missing rule to a Role and bind it.

---

# Conclusion

This lab demonstrated how Kubernetes RBAC controls access to resources using ServiceAccounts, Roles, and RoleBindings. By validating permissions with `kubectl auth can-i`, we confirmed that the configured ServiceAccount received only the intended read-only access to Pods, illustrating a secure least-privilege authorization model suitable for production Kubernetes environments.

