# Kubernetes Cluster Architecture

## Objective

Understand how Kubernetes components interact to manage workloads.

---

## Current Cluster

Node Name

platform-engineer-lab

Node Role

Control Plane

Container Runtime

containerd

---

## High-Level Architecture

The API server is the hub. Every other component talks only to it and **watches** it for changes; they never call each other directly.

```text
                 kubectl
                    │
                    ▼
 Scheduler ───► API Server ◄─── Controller Manager
                 │      ▲
          (state)│      │ watch / status
                 ▼      │
     datastore (etcd; SQLite on single-node K3s)
                        │
                     kubelet ──► containerd ──► Pods
```

---

## Components

### API Server

Acts as the entry point for all Kubernetes API requests.

### Scheduler

Assigns newly created Pods to Nodes.

### Controller Manager

Maintains the desired state of the cluster.

### kubelet

Runs on every Node, watches the API server for Pods bound to its node, and asks containerd to run them.

### Datastore

Stores all cluster state. Upstream Kubernetes uses etcd. A single-server K3s cluster like this one uses SQLite by default (through kine); K3s switches to embedded etcd for multi-server HA.

### containerd

Creates and manages containers.

---

## Verification Commands

```bash
kubectl get nodes
kubectl cluster-info
kubectl get pods -A
```
