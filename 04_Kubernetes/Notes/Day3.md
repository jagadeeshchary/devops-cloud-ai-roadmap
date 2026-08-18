1. Why do we need Services in Kubernetes?

Pods in Kubernetes are temporary and their IP addresses can change whenever they are recreated.

Example:

Old Pod:
10.244.0.9


Pod crashes


New Pod:
10.244.0.15

Because the Pod IP changes, users or applications cannot directly depend on Pod IP addresses.

A Service provides a stable network endpoint to access Pods, even when Pods are recreated.

2. What is a Kubernetes Service?

A Service is a Kubernetes resource that provides:

Stable IP address
Stable DNS name
Load balancing between Pods

A Service does not create or manage Pods.

It only provides networking and communication between clients and Pods.

Architecture:

User
 |
 ▼
Service
 |
 ▼
Pods


3. How does a Service find Pods?

A Service uses labels and selectors.

Pods have labels:

labels:
  app: nginx-deployment

Service has a selector:

selector:
  app: nginx-deployment

When the labels match, the Service connects to those Pods.

Flow:

Service Selector
        |
        ▼
Pod Labels
        |
        ▼
Endpoints Created



4. What are Endpoints?

Endpoints are the actual IP addresses of Pods behind a Service.

Example:

Service:

ClusterIP:
10.101.165.178

Endpoints:

10.244.0.19:80
10.244.0.20:80
10.244.0.21:80

The Service forwards traffic to these Pod IP addresses.

Traffic flow:

Client
  |
  ▼
Service IP
10.101.165.178
  |
  ▼
Endpoint
10.244.0.19:80
  |
  ▼
Container



5. Types of Kubernetes Services
A. ClusterIP

ClusterIP is the default Service type.

It provides communication only inside the Kubernetes cluster.

Example:

Pod A
 |
 ▼
ClusterIP Service
 |
 ▼
Pod B

External users cannot access a ClusterIP Service directly.

Use cases:

Internal microservices communication
Backend services
Database connections
B. NodePort

NodePort exposes a Service outside the Kubernetes cluster by opening a port on every Node.

Example:

User Browser


http://Node-IP:30610


        |
        ▼


NodePort Service


        |
        ▼


Pods

Example:

PORT(S)


80:30610/TCP

Meaning:

80       → Service Port


30610    → NodePort

Traffic flow:

Browser
   |
   ▼
Node IP:30610
   |
   ▼
Service Port 80
   |
   ▼
Pod Port 80

NodePort is commonly used for:

Testing
Development
Some on-premises environments
C. LoadBalancer

LoadBalancer is mainly used in cloud environments like:

AWS
Azure
Google Cloud

It creates an external load balancer provided by the cloud provider.

Traffic flow:

Internet Users
       |
       ▼
Cloud LoadBalancer
       |
       ▼
Kubernetes Service
       |
       ▼
Pods

LoadBalancer distributes incoming traffic across available Nodes and Pods.

It is commonly used for production applications with many users.



6. Difference Between ClusterIP, NodePort and LoadBalancer
ClusterIP
Internal access only
Default Service type
Used inside Kubernetes cluster
NodePort
Opens a port on Kubernetes Nodes
Allows external access
Mostly used for testing/development
LoadBalancer
Provides external access using cloud infrastructure
Used for production applications
Handles large user traffic



7. Service does not know about Deployment or ReplicaSet

A common misunderstanding is that Services connect directly to Deployments.

This is incorrect.

The actual relationship is:

Deployment
      |
      ▼
ReplicaSet
      |
      ▼
Pods
      |
      ▼
Labels
      |
      ▼
Service Selector
      |
      ▼
Service

The Service only uses labels to find Pods.



8. What happens when Pods scale?

Example:

Current:

3 Pods

Service Endpoints:

Pod 1 IP
Pod 2 IP
Pod 3 IP

If Deployment scales to 5 replicas:

2 new Pods are created

Because the new Pods have the same labels, the Service automatically updates Endpoints.

No changes are required in the Service.




9. Hands-on Practice Completed

Created a Service YAML:

File:

04_Kubernetes/YAML/nginx-service.yaml

Content:

apiVersion: v1
kind: Service


metadata:
  name: nginx-service


spec:
  selector:
    app: nginx-deployment


  ports:
  - port: 80
    targetPort: 80


  type: NodePort

Applied using:

kubectl apply -f nginx-service.yaml

Verified using:

kubectl get svc

and:

kubectl describe svc nginx-service

Confirmed:

Selector matched Pods
Endpoints were created
Traffic routing was working



Day 3 Summary

Kubernetes Services provide stable networking for Pods.

Pods are temporary, but Services provide a permanent way to communicate with applications.

The complete traffic flow:

User
 |
 ▼
LoadBalancer
 |
 ▼
NodePort
 |
 ▼
ClusterIP Service
 |
 ▼
Endpoints
 |
 ▼
Pods
 |
 ▼
Containers

This completes Week 1 - Day 3: Services & Networking.
