---
aliases: ["Kubernetes"]
---

# Kubernetes

 provides numerous abstractions and APIs that make it easier to build decoupled microservices architectures.
 
- Pods, or groups of containers, can group together container images developed by different teams into a single deployable unit.
- Kubernetes services provide load balancing, naming, and discovery to isolate one microservice from another.
- Namespaces provide isolation and access control, so that each microservice can control the degree to which other services interact with it.
- Ingress objects provide an easy-to-use frontend that can combine multiple microservices into a single externalized API surface area.

A Kubernetes cluster consists of two types of resources:

- The `Control Plane` coordinates the cluster
- `Nodes` are the workers that run applications

**The Control Plane is responsible for managing the cluster.** The Control Plane coordinates all activities in your cluster, such as scheduling applications, maintaining applications' desired state, scaling applications, and rolling out new updates.

Control Planes manage the cluster and the nodes that are used to host the running applications.

**A node is a VM or a physical computer that serves as a worker machine in a Kubernetes cluster.** Each node has a `Kublet`, which is an agent for managing the node and communicating with the Kubernetes control plane. The node should also have tools for handling container operations, such as `containerd` or `CRI-O`. 

Note: A Kubernetes cluster that handles production traffic should have a minimum of three nodes because if one node goes down, both an `etcd` member  and a control plane instance are lost, and redundancy is compromised. You can mitigate this risk by adding more control plane nodes.

When you deploy applications on Kubernetes, you tell the control plane to start the application containers. The control plane schedules the containers to run on the cluster's nodes. **Node-level components, such as the kublet, communicate with the control plane using the Kubernetes API**, which the control plane exposes. End users can also use the Kubernetes API directly to interact with the cluster.

A Kubernetes cluster can be deployed on either physical or virtual machines. To get started with Kubernetes development, you can use `Minikube`. Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. Minikube is available for Linux, macOS, and Windows systems. The Minikube CLI provides basic bootstrapping operations for working with your cluster, including start, stop, status, and delete.


## Install and set up kubectl on macOS

```shell
brew install kubectl
```

Verify the installation:

```shell
kubectl version
```

Check that kubectl is properly configured by getting the cluster state:

```shell
kubectl cluster-info
```

In order for kubectl to find and access a Kubernetes cluster, it needs a kubeconfig file, which is created automatically when you create a cluster using kube-up.sh or successfully deploy a Minikube cluster. By default, kubectl configuration is located at `~/.kube/config`




## References

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Install and Set Up kubectl on macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/#install-kubectl-binary-with-curl-on-macos)
