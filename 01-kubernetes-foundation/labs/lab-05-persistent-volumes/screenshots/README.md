# Lab 05 – Screenshots

Capture the following screenshots while performing the lab.

| Screenshot                                 | Description                                                              |
| ------------------------------------------ | ------------------------------------------------------------------------ |
| 01-pv-created.png                          | PersistentVolume successfully created                                    |
| 02-pvc-pending-storageclass-mismatch.png   | PVC in Pending state before StorageClass fix                             |
| 03-pvc-bound.png                           | PVC successfully bound to the static PV                                  |
| 04-static-deployment-created.png           | NGINX Deployment using the static PVC                                    |
| 05-volume-mounted.png                      | Volume mounted inside the running container                              |
| 06-data-written.png                        | HTML file created within the mounted volume                              |
| 07-data-persisted-after-pod-recreation.png | File still available after Pod recreation                                |
| 08-dynamic-pvc-created.png                 | Dynamic PVC created using the `local-path` StorageClass                  |
| 09-dynamic-pv-auto-created.png             | Automatically provisioned PersistentVolume visible with `kubectl get pv` |
| 10-storageclass-details.png                | Output of `kubectl describe storageclass local-path`                     |
| 11-dynamic-pod-running.png                 | Dynamic provisioning Pod in the Running state                            |
| 12-pvc-events.png                          | PVC events showing `WaitForFirstConsumer` and `ProvisioningSucceeded`    |

## Recommended Commands

```bash
kubectl get pv
kubectl get pvc
kubectl get pods
kubectl describe pvc nginx-dynamic-pvc
kubectl describe storageclass local-path
```

## Tips

* Use a consistent terminal width for all screenshots.
* Ensure command output is fully visible.
* Capture the command along with its output for clarity.
* Organize screenshots in chronological order using the filenames above.

