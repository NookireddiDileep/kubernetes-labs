# Lab 06 – Screenshots

Capture the following screenshots during the lab.

| Screenshot                           | Description                             |
| ------------------------------------ | --------------------------------------- |
| 01-headless-service-created.png      | Headless Service with `ClusterIP: None` |
| 02-statefulset-created.png           | StatefulSet created successfully        |
| 03-stateful-pods-running.png         | Two StatefulSet Pods running            |
| 04-stateful-pvcs-created.png         | Dedicated PVCs created for each replica |
| 05-pod0-data-created.png             | Custom data written to Pod 0            |
| 06-pod1-data-created.png             | Custom data written to Pod 1            |
| 07-pod0-recreated.png                | Pod 0 recreated after deletion          |
| 08-stateful-data-persisted.png       | Data persisted after Pod recreation     |
| 09-scale-to-three.png                | StatefulSet scaled to three replicas    |
| 10-third-pvc-created.png             | Third PVC automatically created         |
| 11-scale-back-to-two.png             | StatefulSet scaled back to two replicas |
| 12-pvc-retained-after-scale-down.png | PVC retained after scale-down           |
| 13-hostname-verification.png         | Stable Pod hostnames                    |
| 14-fqdn-verification.png             | Stable Fully Qualified Domain Names     |

## Recommended Commands

```bash
kubectl get statefulset
kubectl get pods
kubectl get pvc
kubectl get svc
kubectl exec nginx-stateful-0 -- hostname
kubectl exec nginx-stateful-0 -- hostname -f
```

## Tips

* Include both the command and its output in screenshots.
* Keep terminal width consistent for readability.
* Capture screenshots in the order of execution.
* Use descriptive filenames matching the table above.

