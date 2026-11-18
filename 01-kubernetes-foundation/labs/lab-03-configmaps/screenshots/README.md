# Screenshots – Lab 03: Kubernetes ConfigMaps

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [01-configmap-created-configured](01-configmap-created-configured.png)
- [02-configmap-list](02-configmap-list.png)
- [03-configmap-describe](03-configmap-describe.png)
- [04-deployment-created-unchanged](04-deployment-created-unchanged.png)
- [05-pod-running](05-pod-running.png)
- [06-env-variables](06-env-variables.png)
- [resource-overview](resource-overview.png)

## Commands Used

```bash
kubectl apply -f configmap.yaml

kubectl get configmaps -n demo

kubectl describe configmap app-config -n demo

kubectl apply -f deployment.yaml

kubectl get deployments -n demo

kubectl get pods -n demo -o wide

kubectl exec -n demo <pod-name> -- env | sort

kubectl get all -n demo
```

## Learning Outcome

These screenshots demonstrate the following Kubernetes concepts:

* Creating and managing ConfigMaps.
* Deploying applications using Kubernetes Deployments.
* Injecting ConfigMap values as environment variables.
* Verifying application configuration inside a running Pod.
* Managing resources within a dedicated namespace.
* Validating the overall deployment using Kubernetes CLI commands.
