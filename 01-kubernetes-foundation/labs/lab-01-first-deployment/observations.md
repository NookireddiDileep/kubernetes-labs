# Lab 01 – Observations

## Environment

- Kubernetes Distribution: K3s
- Kubernetes Version: v1.36.2+k3s1
- Runtime: containerd
- Namespace: demo

---

## Observations

### Namespace

Successfully created a dedicated namespace named `demo`.

---

### Deployment

Applied a Deployment manifest using declarative YAML.

Deployment successfully created one ReplicaSet.

---

### ReplicaSet

ReplicaSet maintained one desired replica.

When the running Pod was deleted, the ReplicaSet automatically created a replacement Pod.

---

### Pod

Pod was successfully scheduled on:

```
platform-engineer-lab
```

Pod received an internal cluster IP.

Container image:

```
nginx:stable
```

Status:

```
Running
Ready
```

---

## Self-Healing Test

Deleted the running Pod.

Observed Kubernetes automatically create a new Pod with a different Pod name.

Old Pod

```
nginx-76d5cfb9b6-b9mtp
```

New Pod

```
nginx-76d5cfb9b6-578k4
```

Deployment and ReplicaSet names remained unchanged.

---

## Important Events

Deployment Controller

```
Scaled ReplicaSet from 0 to 1
```

ReplicaSet Controller

```
Created initial Pod

Created replacement Pod after deletion
```

---

## Lessons Learned

- Pods should not be managed directly.
- Deployments provide declarative application management.
- ReplicaSets ensure the desired number of Pods are running.
- Kubernetes continuously reconciles cluster state.
- Pod names change when recreated.
- ReplicaSet names remain constant unless the Pod template changes.
