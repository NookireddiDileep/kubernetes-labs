# Basic kubectl Commands

## Cluster

```bash
kubectl cluster-info
kubectl get nodes -o wide
kubectl version
```

## Pods

```bash
kubectl get pods -A
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- sh
```

## Namespaces

```bash
kubectl get ns
kubectl create ns demo
kubectl delete ns demo
```

## Deployments

```bash
kubectl get deploy
kubectl describe deploy <deployment-name>
```
