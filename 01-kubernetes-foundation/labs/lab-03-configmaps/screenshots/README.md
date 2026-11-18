# Screenshots – Lab 03: Kubernetes ConfigMaps

This directory contains screenshots captured during the execution of **Lab 03 – Kubernetes ConfigMaps**. These images provide evidence of the successful creation, deployment, and verification of Kubernetes resources.

| Screenshot                    | Description                                                                                                                                    |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **01-configmap-created.png**  | Shows the successful creation of the ConfigMap using `kubectl apply -f configmap.yaml`.                                                        |
| **02-configmap-list.png**     | Displays the list of ConfigMaps in the `demo` namespace using `kubectl get configmaps -n demo`.                                                |
| **03-configmap-describe.png** | Shows the detailed information and key-value pairs stored in the `app-config` ConfigMap using `kubectl describe configmap app-config -n demo`. |
| **04-deployment-created.png** | Shows the successful creation of the Deployment using `kubectl apply -f deployment.yaml`.                                                      |
| **05-pod-running.png**        | Verifies that the Deployment created a healthy Pod in the `Running` state using `kubectl get pods -n demo -o wide`.                            |
| **06-env-variables.png**      | Demonstrates that the application successfully received configuration values from the ConfigMap as environment variables using `kubectl exec`. |
| **07-resource-overview.png**  | Displays all Kubernetes resources created for this lab using `kubectl get all -n demo`, providing a complete overview of the deployment.       |

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

