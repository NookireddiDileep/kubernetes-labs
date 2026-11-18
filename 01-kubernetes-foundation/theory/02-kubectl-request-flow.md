# kubectl Request Flow

## Request Lifecycle

```text
kubectl ──HTTPS──► API Server: authentication → authorization (RBAC) → mutating admission
                   → schema validation → validating admission → saved to the datastore
Deployment controller (watching) → creates ReplicaSet
ReplicaSet controller (watching)  → creates Pod (no node yet, Pending)
Scheduler (watching)              → picks a node, writes the binding
kubelet on that node (watching)   → containerd pulls the image and starts the container
kubelet                           → reports status back to the API server (Running)
```

---

## Example Command

```bash
kubectl apply -f nginx.yaml
```

### What Happens?

1. kubectl sends the Deployment to the API server.
2. The API server authenticates, authorizes, runs admission and validates it, then stores it.
3. The Deployment controller creates a ReplicaSet; the ReplicaSet controller creates the Pod.
4. The scheduler picks a node and binds the Pod to it.
5. The kubelet on that node sees the Pod and asks containerd to pull the image and start it.
6. The kubelet reports the Pod's status back to the API server.

No component calls another directly: each one watches the API server and reacts.
