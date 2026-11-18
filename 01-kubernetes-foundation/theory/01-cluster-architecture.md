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

kubectl

↓

API Server

↓

Scheduler

↓

Controller Manager

↓

kubelet

↓

containerd

↓

Pods

---

## Components

### API Server

Acts as the entry point for all Kubernetes API requests.

### Scheduler

Assigns newly created Pods to Nodes.

### Controller Manager

Maintains the desired state of the cluster.

### kubelet

Runs on every Node and communicates with the API Server.

### containerd

Creates and manages containers.

---

## Verification Commands

```bash
kubectl get nodes
kubectl cluster-info
kubectl get pods -A
```
