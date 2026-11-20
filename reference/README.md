# Kubernetes reference library

Concept notes in my own words on **upstream Kubernetes**, written to go from plain language to senior / architect (L4) depth. The labs in this repo are the evidence; these notes are the understanding behind them.

**Long-term target:** the depth expected of a senior Kubernetes SME, platform architect, and the full set of Kubernetes certifications (KCNA, KCSA, CKA, CKAD, CKS, i.e. "Kubestronaut").

## How each note is written

1. **Plain-language picture** first: an analogy anyone can follow
2. **What's really happening:** the component, the API objects, the mechanism
3. **Commands and YAML** worth knowing from memory
4. **Troubleshooting:** symptom → evidence → cause → fix
5. **L4 angle:** the follow-up questions a senior panel asks, with model answers
6. **Labs:** links to evidence in this repo, and planned labs

## Depth levels used

| Level | Means |
|---|---|
| L1 Know | Can name it and say what it does |
| L2 Diagnose | Knows symptom → commands → fix |
| L3 Explain | Knows the mechanism and trade-offs |
| L4 Predict and design | Prevents, tunes and designs before it breaks; reasons about scale and failure |

## What lives where (no duplication across my repos)

| Topic | Owned by | Here |
|---|---|---|
| Upstream Kubernetes: networking, API machinery, cluster lifecycle, storage, security, reliability, autoscaling, extensibility, scale | **this repo** | Full notes |
| OpenShift-specific: Routes, SCCs, OVN-Kubernetes, CRI-O, `oc` | [openshift-lab-records/reference](https://github.com/NookireddiDileep/openshift-lab-records#reference) | Linked only |
| Pod failure states (troubleshooting map) | [openshift-lab-records: pod failure states decoder](https://github.com/NookireddiDileep/openshift-lab-records/blob/main/reference/pod-failure-states-decoder.md) | Linked, with an upstream translation in [Lab 13](../01-kubernetes-foundation/labs/lab-13-production-troubleshooting/README.md#upstream-kubernetes-translation-of-the-pod-failure-states-decoder) |
| Linux kernel under containers: processes, memory/OOM, cgroups, namespaces | [project-arohan-ai: linux-internals](https://github.com/NookireddiDileep/project-arohan-ai/tree/main/engineering/linux-internals) | Linked only |
| Scheduling deep dive (kube-scheduler to GPU gangs), inference on Kubernetes, OpenShift vs Kubernetes | [project-arohan-ai: engineering](https://github.com/NookireddiDileep/project-arohan-ai/tree/main/engineering) | Linked only |
| Terraform, Ansible, scripting | [infra-automation-lab](https://github.com/NookireddiDileep/infra-automation-lab) | Linked only |

## Notes

| # | Note | Depth | Certifications it serves | Status |
|---|---|---|---|---|
| R01 | [Kubernetes networking end to end](R01-networking-end-to-end.md): pods, CNI, Services, kube-proxy, DNS, Ingress → Gateway API, NetworkPolicy | L1 → L4 | CKA, CKS, KCNA | ✅ Written |
| R02 | API machinery and controllers: watch, informers, reconciliation, owner references, garbage collection, finalizers, server-side apply | L3 → L4 | CKAD, CKA | ⏳ Planned |
| R03 | Cluster lifecycle with kubeadm: bootstrap, certificates, upgrades, etcd backup and restore | L2 → L4 | CKA | ⏳ Planned |
| R04 | Storage and CSI: PV lifecycle, CSI sidecars, snapshots, expansion, topology | L2 → L4 | CKA | ⏳ Planned |
| R05 | Security hardening: authn/authz, RBAC design, Pod Security Admission, ValidatingAdmissionPolicy, secrets encryption, audit, supply chain | L2 → L4 | CKS, KCSA | ⏳ Planned |
| R06 | Workload reliability: probes, PDBs, priority and preemption, topology spread, graceful shutdown, rollout strategies | L2 → L4 | CKAD, CKA | ⏳ Planned |
| R07 | Autoscaling: HPA, VPA, Cluster Autoscaler, Karpenter, KEDA | L3 → L4 | CKAD | ⏳ Planned |
| R08 | Control plane and node troubleshooting (upstream): API server, etcd, kubelet, certificate expiry | L2 → L4 | CKA | ⏳ Planned |
| R09 | Extending Kubernetes: CRDs, operators, admission webhooks, controller-runtime | L3 → L4 | CKAD | ⏳ Planned |
| R10 | Observability for Kubernetes: metrics pipeline, kube-state-metrics, events, logs, traces | L2 → L3 | CKA | ⏳ Planned |
| R11 | Multi-tenancy and cluster design: namespaces vs virtual clusters vs clusters, policy, multi-cluster | L4 | Architect | ⏳ Planned |
| R12 | Scale and performance: etcd sizing, API Priority and Fairness, large-cluster limits | L4 | Architect | ⏳ Planned |

Order of writing follows the modules: R01 with module 05 (networking), R03 and R08 with module 04 (cluster internals / CKA), the rest as the labs reach them.
