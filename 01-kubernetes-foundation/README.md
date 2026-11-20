# ☸️ Module 01 — Kubernetes Foundation

**13 hands-on labs and 9 break-and-fix troubleshooting scenarios on a K3s cluster I built and ran myself.** Every lab has its manifests, validation screenshots and an `observations.md` with what I saw, what broke and how I fixed it.

| | |
|---|---|
| **Status** | ✅ Complete (v1.0.0, July 2026) · [changelog](CHANGELOG.md) |
| **Cluster** | Single-node K3s `v1.36.2+k3s1`; the one server node also runs workloads. VM retired after this module; [rebuild guide](../00-environment-setup/README.md) |
| **Platform** | Ubuntu 24.04 LTS VM on Google Cloud, containerd runtime |
| **Networking** | Flannel CNI, K3s embedded network policy controller, Traefik ingress |
| **Storage** | `local-path` provisioner (default StorageClass) |
| **Evidence** | 45 manifests · 128 screenshots · one `observations.md` per lab |

> **Scope, honestly:** this is a single-node lab cluster, so it can't show multi-node scheduling, node failure or control-plane HA. Those are the focus of [module 02 incident drills](../02-incident-drills/README.md) (starting) and are practised on multi-node OpenShift in [openshift-lab-records](https://github.com/NookireddiDileep/openshift-lab-records).

---

## Labs

| # | Lab | What I did |
|---|---|---|
| 01 | [First deployment](labs/lab-01-first-deployment/README.md) | Deployment → ReplicaSet → Pods; deleted pods and watched self-healing |
| 02 | [Services](labs/lab-02-services/README.md) | ClusterIP Service, label selectors, EndpointSlices tracking pods as they change |
| 03 | [ConfigMaps](labs/lab-03-configmaps/README.md) | ConfigMap values injected as environment variables |
| 04 | [Secrets](labs/lab-04-secrets/README.md) | Secrets as env vars; base64 is encoding, not encryption |
| 05 | [Persistent storage](labs/lab-05-persistent-volumes/README.md) | Static PV/PVC and dynamic provisioning; data survived pod recreation |
| 06 | [StatefulSets](labs/lab-06-statefulsets/README.md) | Stable names and per-pod storage with a headless Service |
| 07 | [Requests & limits](labs/lab-07-resource-management/README.md) | BestEffort vs Burstable QoS; forced an OOMKill |
| 08 | [Health checks](labs/lab-08-health-checks/README.md) | Liveness, readiness and startup probes |
| 09 | [Jobs & CronJobs](labs/lab-09-jobs-cronjobs/README.md) | Run-to-completion and scheduled workloads |
| 10 | [RBAC](labs/lab-10-rbac/README.md) | ServiceAccount, Role, RoleBinding; verified with `kubectl auth can-i --as` |
| 11 | [Namespaces & quotas](labs/lab-11-namespaces/README.md) | ResourceQuota and LimitRange in a `development` namespace |
| 12 | [Ingress](labs/lab-12-ingress/README.md) | Path routing to a ClusterIP Service through the bundled Traefik controller |
| 13 | [Production troubleshooting](labs/lab-13-production-troubleshooting/README.md) | 9 break-and-fix scenarios, below |

## Lab 13: break-and-fix scenarios

Each scenario was broken on purpose, diagnosed from symptoms only, fixed and written up. Summary of all nine: [observations.md](labs/lab-13-production-troubleshooting/observations.md).

| # | Scenario | Root cause I found |
|---|---|---|
| 01 | [CrashLoopBackOff](labs/lab-13-production-troubleshooting/scenario-01-crashloop/README.md) | Container command exited with code 1 at start-up |
| 02 | [ImagePullBackOff](labs/lab-13-production-troubleshooting/scenario-02-imagepullbackoff/README.md) | Image tag that doesn't exist in the registry |
| 03 | [Pending pod](labs/lab-13-production-troubleshooting/scenario-03-pending-pod/README.md) | `nodeSelector` matched no node labels |
| 04 | [ConfigMap error](labs/lab-13-production-troubleshooting/scenario-04-configmap/README.md) | Pod referenced a ConfigMap that didn't exist |
| 05 | [Secret error](labs/lab-13-production-troubleshooting/scenario-05-secret/README.md) | Pod referenced a Secret that didn't exist |
| 06 | [Probe failures](labs/lab-13-production-troubleshooting/scenario-06-liveness-readiness/README.md) | Liveness probe checked `/health`, but NGINX only serves `/`, so the kubelet kept restarting a healthy app |
| 07 | [PVC stuck Pending](labs/lab-13-production-troubleshooting/scenario-07-persistent-volume/README.md) | PVC asked for a StorageClass that doesn't exist in the cluster |
| 08 | [NetworkPolicy](labs/lab-13-production-troubleshooting/scenario-08-network-policy/README.md) | `deny-all-ingress` on `app: nginx` blocked BusyBox → NGINX, enforced by K3s's embedded policy controller |
| 09 | [OOMKilled](labs/lab-13-production-troubleshooting/scenario-09-resource-limits/README.md) | App exceeded its memory limit; the kernel's cgroup OOM killer ended it (exit 137) |

Every scenario follows the same loop: **observe → collect evidence (events, describe, logs) → find root cause → fix → verify → document**.

---

## What I found (highlights)

- **OOMKilled is the kernel, not Kubernetes.** A pod with a 128Mi limit tried to allocate ~300MB and was killed with reason `OOMKilled`, exit code `137` (128 + SIGKILL), then went into CrashLoopBackOff. [Lab 07](labs/lab-07-resource-management/observations.md) · [screenshot](labs/lab-07-resource-management/screenshots/04.oom-killed.png)
- **A PVC can be Pending for two very different reasons.** The static PVC stayed Pending because of a StorageClass mismatch (fixed with `storageClassName: ""`). The dynamic one was Pending *by design*: `local-path` uses `WaitForFirstConsumer`, so the volume is only created once a pod is scheduled. Data written to the volume survived pod deletion. [Lab 05](labs/lab-05-persistent-volumes/observations.md) · [screenshot](labs/lab-05-persistent-volumes/screenshots/08-data-persisted-after-pod-recreation.png)
- **Default K3s does enforce NetworkPolicy.** Flannel has no policy support, but K3s adds a kube-router-based controller. After the deny policy, `wget` to NGINX failed with `Connection refused`. [Scenario 08](labs/lab-13-production-troubleshooting/scenario-08-network-policy/README.md) · [screenshot](labs/lab-13-production-troubleshooting/scenario-08-network-policy/screenshots/03-connectivity-test.png)
- **QoS class comes from requests and limits.** No resources gave BestEffort (first to be evicted); requests below limits gave Burstable. [Lab 07](labs/lab-07-resource-management/observations.md)

## Study material

| Doc | What |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Control plane and node components, API request flow, pod lifecycle, scheduling, networking, storage and RBAC flows |
| [theory/](theory/) | Cluster architecture, `kubectl` request flow, components, basic commands, interview questions |
| [Lab 13 interview cheat sheet](labs/lab-13-production-troubleshooting/interview-cheatsheet.md) | Troubleshooting questions with model answers |
| [Pod failure states decoder](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md) | Every pod failure state, plain language to L4; [upstream translation and scenario map](labs/lab-13-production-troubleshooting/README.md#upstream-kubernetes-translation-of-the-pod-failure-states-decoder) |
| [Reference library](../reference/README.md) | Upstream Kubernetes concept notes, starting with [R01 networking end to end](../reference/R01-networking-end-to-end.md) |

## Commands I used most when debugging

```bash
kubectl get pods -o wide                                   # status, restarts, node
kubectl describe pod <pod>                                 # events at the bottom
kubectl get events --sort-by=.lastTimestamp                # cluster timeline
kubectl get events --field-selector involvedObject.name=<pod>
kubectl logs <pod> --previous                              # logs of the crashed container
kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[0].lastState}'
kubectl auth can-i delete pods --as=system:serviceaccount:default:pod-reader
kubectl get pvc,pv,storageclass                            # storage chain
kubectl top pods && kubectl top nodes                      # live usage (metrics-server)
```

---

## Next

- **[Module 02 — Incident drills](../02-incident-drills/README.md)** 🟡 (set up, no drills logged yet) timed live-outage drills on multi-node clusters, with my own RCAs
- **Module 03 — Helm** ⏳ charts, values, templating, releases and rollbacks
