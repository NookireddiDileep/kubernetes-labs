# Lab 12 – Observations

## Lab Objective

The objective of this lab was to understand how Kubernetes **Ingress** provides Layer 7 (HTTP/HTTPS) routing for applications and how an **Ingress Controller** routes external traffic to Services and Pods.

---

# Environment

| Component               | Details   |
| ----------------------- | --------- |
| Kubernetes Distribution | K3s       |
| Ingress Controller      | Traefik   |
| Application             | NGINX     |
| Service Type            | ClusterIP |
| Namespace               | default   |

---

# Step 1 – Ingress Controller Verification

Verification Commands

```text id="zcmswv"
kubectl get pods -A

kubectl get ingressclass

kubectl get svc -A
```

Observation

The K3s cluster already included the Traefik Ingress Controller.

Verified:

* Traefik Pod was in the **Running** state.
* Default IngressClass was **traefik**.
* Traefik Service was exposed through a LoadBalancer.

Conclusion

No additional installation was required because K3s automatically deploys Traefik by default.

---

# Step 2 – Deployment

Deployment Created

```text id="d5w5i7"
nginx-web
```

Replicas

```text id="gbz6ic"
Desired : 2

Available : 2
```

Observation

The Deployment successfully created two NGINX Pods.

Both Pods reached the **Running** state.

The Deployment controller ensured the desired number of replicas remained available.

---

# Step 3 – ClusterIP Service

Service Created

```text id="s1s9of"
nginx-service
```

Type

```text id="n9x6wl"
ClusterIP
```

Observation

The ClusterIP Service successfully exposed the NGINX Pods internally within the cluster.

The Service selected the Pods using the configured label selector.

---

# Step 4 – Endpoint Verification

Observed Endpoints

```text id="jlwm6m"
10.42.0.193:80

10.42.0.194:80
```

Observation

The Service automatically created backend endpoints corresponding to the two NGINX Pods.

This confirmed:

* Pod labels matched the Service selector.
* Traffic could be forwarded correctly to both Pods.

The endpoint discovery process is a critical verification step before troubleshooting Ingress issues.

---

# Step 5 – Ingress Resource

Ingress Created

```text id="0i75bw"
nginx-ingress
```

Ingress Class

```text id="rdzngm"
traefik
```

Backend

```text id="44u4w4"
nginx-service:80
```

Observation

The Ingress resource successfully associated with the Traefik Ingress Controller.

The configured rule routed requests from the root path (`/`) to the `nginx-service` ClusterIP Service.

---

# Step 6 – External Connectivity

Test Command

```text id="qtx5lo"
curl http://10.160.0.2/
```

Result

```text id="91yz8q"
Welcome to nginx!
```

Observation

The successful response confirmed that external HTTP traffic passed through:

* Traefik LoadBalancer
* Traefik Ingress Controller
* Ingress Resource
* ClusterIP Service
* NGINX Pods

This validated the complete end-to-end request flow.

---

# Request Flow

```text id="9zjlwm"
Client
   │
Traefik LoadBalancer
   │
Traefik Ingress Controller
   │
Ingress Resource
   │
ClusterIP Service
   │
NGINX Pods
```

Observation

Each Kubernetes component functioned correctly, demonstrating how Ingress provides Layer 7 routing while the Service continues to handle Pod discovery and load balancing.

---

# Key Learnings

* An Ingress resource contains routing rules only.
* An Ingress Controller implements and enforces those rules.
* ClusterIP Services remain the preferred backend for Ingress resources.
* Services automatically discover Pods using label selectors.
* Endpoint verification is an important troubleshooting step before investigating Ingress issues.
* A single Ingress Controller can expose multiple applications using host-based or path-based routing.

---

# Production Relevance

Ingress is commonly used to:

* Publish web applications.
* Centralize HTTP/HTTPS traffic.
* Reduce the number of external LoadBalancers.
* Perform TLS termination.
* Implement host-based routing.
* Implement path-based routing.
* Simplify application exposure in Kubernetes and OpenShift.

---

# Common Troubleshooting

| Issue                   | Possible Cause                  | Resolution                            |
| ----------------------- | ------------------------------- | ------------------------------------- |
| 404 Not Found           | Ingress rule mismatch           | Verify host and path rules            |
| 503 Service Unavailable | No Service endpoints            | Verify Pods and selectors             |
| Ingress has no Address  | Ingress Controller unavailable  | Verify Traefik/NGINX controller       |
| Backend unreachable     | Incorrect Service configuration | Check Service selector and targetPort |
| Requests timeout        | Application not responding      | Inspect Pod logs and readiness status |

---

# Conclusion

This lab demonstrated how Kubernetes Ingress exposes HTTP applications through a single entry point. We verified the complete request flow from the Traefik Ingress Controller to the ClusterIP Service and finally to the backend Pods. These concepts form the foundation of production-grade application exposure in Kubernetes and OpenShift environments.

