# Lab 10 – Observations

## Objective

To understand Kubernetes Role-Based Access Control (RBAC) by creating a ServiceAccount, Role, and RoleBinding, then validating permissions using `kubectl auth can-i`.

---

# Observations

## Part A – ServiceAccount

Created a ServiceAccount named:

```text id="ifpjhv"
pod-reader
```

Verification:

```bash id="om9m5y"
kubectl get serviceaccounts

kubectl describe serviceaccount pod-reader
```

Findings:

* ServiceAccount created successfully.
* Namespace: default.
* No automatic Secret created (expected in modern Kubernetes versions).
* Ready to be used by workloads or users through RBAC.

---

## Part B – Role

Created a namespace-scoped Role.

Verification:

```bash id="b3v4f0"
kubectl get roles

kubectl describe role pod-reader-role
```

Permissions granted:

* get
* list
* watch

Resource:

* pods

Observation:

The Role provides read-only access to Pods within the default namespace.

---

## Part C – RoleBinding

Created a RoleBinding.

Verification:

```bash id="6zvjlwm"
kubectl get rolebindings

kubectl describe rolebinding pod-reader-binding
```

Observation:

Successfully linked:

* ServiceAccount: pod-reader
* Role: pod-reader-role

This completed the RBAC permission chain.

---

## Part D – Permission Validation

Executed:

```bash id="nmrp6o"
kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader

kubectl auth can-i delete pods --as=system:serviceaccount:default:pod-reader
```

Results:

| Permission  | Result |
| ----------- | ------ |
| Get Pods    | Yes    |
| Delete Pods | No     |

Observation:

The ServiceAccount received only the intended permissions, confirming correct implementation of the Principle of Least Privilege.

---

# Key Learnings

* ServiceAccounts represent identities for workloads.
* Roles define permissions within a namespace.
* RoleBindings assign Roles to ServiceAccounts, users, or groups.
* RBAC authorization occurs after successful authentication.
* `kubectl auth can-i` is an effective command for verifying permissions and troubleshooting authorization issues.
* Least-privilege access reduces security risks in Kubernetes clusters.

---

# Production Relevance

RBAC is essential for:

* Securing CI/CD pipelines
* Restricting developer access
* Granting application-specific permissions
* Protecting Secrets and configuration
* Multi-team Kubernetes environments
* Regulatory and security compliance

