# Lab 02 – Kubernetes Services

## Objective

The objective of this lab is to understand how Kubernetes Services provide stable network access to Pods. You will deploy an NGINX application, expose it using a ClusterIP Service, and verify service discovery and load balancing within the Kubernetes cluster.

---

## Learning Objectives

By completing this lab, you will learn how to:

* Create Kubernetes Services
* Understand the purpose of ClusterIP Services
* Expose Deployments using Services
* Understand Labels and Selectors
* Verify Endpoints and EndpointSlices
* Test Service connectivity
* Understand Kubernetes Service Discovery
* Learn production networking concepts

---

## Lab Architecture

```text
                   Client Pod
                        │
                        │
                 ClusterIP Service
                 nginx-service
                        │
          ┌─────────────┴─────────────┐
          │                           │
      NGINX Pod 1                NGINX Pod 2
                        │
                   Deployment
```

---

## Files

```text
lab-02-services/
├── observations.md
├── README.md
├── service.yaml
└── screenshots/
    ├── 01-deployment-status.png
    ├── 02-service-created.png
    ├── 03-describe-service.png
    ├── 04-endpoints.png
    ├── 05-endpointslice.png
    ├── 06-pod-details.png
    ├── 07-scale-to-four-replicas.png
    ├── 08-endpointslice-after-scaling.png
    ├── 09-service-verification.png
    ├── 10-curl-Thank you for using nginx.PNG
    └── 10.1.nginx-service.PNG
```

---

## Prerequisites

* Running Kubernetes cluster (K3s, Minikube, Kind, or OpenShift)
* kubectl configured
* Basic understanding of Pods and Deployments

---

## Lab Steps

### Step 1 – Deploy the Application

Create the Deployment. This lab reuses the NGINX Deployment from Lab 01 (`app: nginx` in namespace `demo`), which is what `service.yaml` selects.

```bash
kubectl apply -f ../lab-01-first-deployment/nginx.yaml
```

Verify the Deployment.

```bash
kubectl get deployments
```

Verify the Pods.

```bash
kubectl get pods -o wide
```

---

### Step 2 – Create the Service

Expose the Deployment using a ClusterIP Service.

```bash
kubectl apply -f service.yaml
```

Verify the Service.

```bash
kubectl get services
```

Describe the Service.

```bash
kubectl describe service nginx-service
```

---

### Step 3 – Verify Endpoints

Check the Service endpoints.

```bash
kubectl get endpoints
```

Verify EndpointSlices.

```bash
kubectl get endpointslices
```

---

### Step 4 – Test Service Connectivity

Create a temporary test Pod.

```bash
kubectl run test-client --rm -it --image=busybox -- sh
```

Inside the Pod, test connectivity.

```bash
wget -qO- http://nginx-service
```

Exit the test Pod.

```bash
exit
```

---

### Step 5 – Cleanup

Delete the resources.

```bash
kubectl delete -f service.yaml

kubectl delete -f ../lab-01-first-deployment/nginx.yaml
```

---

## Expected Output

After completing this lab, you should observe:

* Deployment successfully created
* Pods in Running state
* ClusterIP Service created
* Service assigned a stable cluster IP
* Endpoints mapped to running Pods
* Successful communication using the Service DNS name

---

## Screenshots

```text
lab-02-services/
├── observations.md
├── README.md
├── service.yaml
└── screenshots/
    ├── 01-deployment-status.png
    ├── 02-service-created.png
    ├── 03-describe-service.png
    ├── 04-endpoints.png
    ├── 05-endpointslice.png
    ├── 06-pod-details.png
    ├── 07-scale-to-four-replicas.png
    ├── 08-endpointslice-after-scaling.png
    ├── 09-service-verification.png
    ├── 10-curl-Thank you for using nginx.PNG
    └── 10.1.nginx-service.PNG
```

---

## Production Best Practices

* Use Services instead of accessing Pods directly.
* Select meaningful labels for Service selectors.
* Prefer ClusterIP for internal application communication.
* Expose applications externally using Ingress or LoadBalancer where appropriate.
* Regularly verify Endpoints during troubleshooting.
* Use DNS-based Service discovery rather than Pod IP addresses.

---

## Key Concepts Learned

* Kubernetes Services
* ClusterIP
* Labels & Selectors
* Endpoints
* EndpointSlices
* Service Discovery
* Internal DNS
* Network Abstraction
* Load Balancing

---

## Interview Questions

**1. What is a Kubernetes Service?**

A stable virtual IP and DNS name in front of a changing set of Pods selected by labels, with load balancing across them.

**2. Why do Pods require a Service?**

Pod IPs change whenever Pods are recreated, rescheduled or scaled. A Service gives clients one address that doesn't change and only sends traffic to ready Pods.

**3. What are the different Service types in Kubernetes?**

`ClusterIP` (internal only), `NodePort` (a port on every node), `LoadBalancer` (an external load balancer from the cloud or, on K3s, ServiceLB) and `ExternalName` (a DNS alias). A headless Service (`clusterIP: None`) returns Pod IPs directly.

**4. What is the default Service type?**

`ClusterIP`.

**5. How do Labels and Selectors work with Services?**

The Service's `selector` lists labels; every Pod whose labels match (and that is ready) becomes a backend. A typo in either the selector or the Pod labels leaves the Service with no endpoints.

**6. What are Endpoints?**

The list of IP:port pairs of the Pods currently backing a Service. kube-proxy programs its rules from them.

**7. What are EndpointSlices?**

The scalable replacement for Endpoints: the backend list is split into slices of up to 100 entries, so large Services don't rewrite one huge object on every change. kube-proxy reads EndpointSlices.

**8. How does Kubernetes perform Service discovery?**

Through cluster DNS (CoreDNS): `<service>.<namespace>.svc.cluster.local` resolves to the ClusterIP. Pods also get legacy environment variables for Services that existed when they started.

**9. What happens if a Pod is recreated?**

It gets a new IP, but the Service selector still matches it, so the EndpointSlice is updated automatically and clients using the Service name are unaffected.

**10. When would you use ClusterIP, NodePort, or LoadBalancer?**

ClusterIP for internal traffic between services; NodePort for quick tests or when an external load balancer targets node ports; LoadBalancer to expose one service directly in a cloud. For many HTTP apps, use one Ingress or Gateway in front of ClusterIP Services instead.

---

## References

* Official Kubernetes Documentation
* Kubernetes Services Documentation
* CNCF Kubernetes Best Practices

---

## Conclusion

This lab demonstrated how Kubernetes Services provide stable networking for applications running inside a cluster. You learned how Services decouple clients from individual Pods, enable service discovery, distribute traffic across healthy Pods, and provide a reliable networking abstraction that is fundamental to production Kubernetes environments.

