# kubectl Request Flow

## Request Lifecycle

User

↓

kubectl

↓

API Server

↓

Scheduler

↓

kubelet

↓

containerd

↓

Pod Running

---

## Example Command

```bash
kubectl apply -f nginx.yaml
```

### What Happens?

1. kubectl sends the request.
2. API Server validates it.
3. Scheduler selects a Node.
4. kubelet receives the Pod assignment.
5. containerd pulls the image.
6. Pod starts.
7. Status is updated in the API Server.
