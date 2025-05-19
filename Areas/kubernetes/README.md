---
aliases: ["Kubernetes"]
---

 provides numerous abstractions and APIs that make it easier to build decoupled microservices architectures.
- Pods, or groups of containers, can group together container images developed by different teams into a single deployable unit.
- Kubernetes services provide load balancing, naming, and discovery to isolate one microservice from another.
- Namespaces provide isolation and access control, so that each microservice can control the degree to which other services interact with it.
- Ingress objects provide an easy-to-use frontend that can combine multiple microservices into a single externalized API surface area.

A Kubernetes cluster consists of two types of resources:

- The `Control Plane` coordinates the cluster
- `Nodes` are the workers that run applications

**The Control Plane is responsible for managing the cluster.** The Control Plane coordinates all activities in your cluster, such as scheduling applications, maintaining applications' desired state, scaling applications, and rolling out new updates.

[!Note]
Control Planes manage the cluster and the nodes that are used to host the running applications.