# A High-Level Introduction of Kubernetes Architecture

Kubernetes, or k8s for short, is a container orchestrator that is a controller/management unit that connects multiple hosts/containers and groups systems as a cluster where the container's deployment and management are automated at scale. There are other container orchestrators. For example, Amazon Elastic Container Service(ECS), Azure Container Instances (ACI), Marathon, etc

Kubernetes was open-sourced in 2014 by Google. Google donated it to the [Cloud Native Computing Foundation](https://www.cncf.io/)(CNCF).


Kubernetes cluster, just like any other distributed system,  consists of a central management role called control plane node and one or more executors that do all the dirty work. Those executors are called worker nodes

The diagram of Kubernets architechture
[!k8s-architechture-diagram](img/k8s-architechture-diagram.png?raw=true)


## Control Plane Node
Control plane node is the brain of the whole k8s cluster and provides a running environment for control plane agents and components to manage the state of k8s cluster. Moreover, client users communicate with k8s clusters by sending requests to the control plan node. 

A control plane node runs the following essential control plane components and agents:
- API Server
- Scheduler
- Controller Managers
- Key-Value Data Store.

<!-- - Container Runtime
- Node Agent
- Proxy
- Optional addons for cluster-level monitoring and logging.
 -->

### API server

API server is communication transit among control plan components and worker node components. It is the only component to talk to key-value data store/etcd to read and save Kubernetes cluster state information.

The main implementation of a Kubernetes API server is kube-apiserver. Kube-apiserver is highly configurable and customizable, and it can scale horizontally by deploying more instances.


### ****Scheduler****

Scheduler assigns new workload objects, such as pods encapsulating containers, to nodes - typically worker nodes. The decision is based on current k8s cluster states and new workload requirements. Scheduler is important in multi-node K8s cluster.

Scheduler has two jobs
- Obtains from the key-value store, via the API Server, resource usage data for each worker node in the cluster.
- Receives from the API Server the new workload object's requirements which are part of its configuration data


### ****Controller Managers****

Controller Managers run controllers or operators to regulate the state of the kubernetes cluster. Controller Managers compare the cluster desired state (provided by objects' configuration data ) with its current state (obtained from the key-value store via the API Server). In case of a mismatch, corrective action is taken in the cluster until its current state matches the desired state.

The **kube-controller-manager.** acts when nodes become unavailable.

The **cloud-controller-manager.** interacts with the underlying infrastructure of a cloud provider when nodes become unavailable, manages storage volumes when provided by a cloud service, and manages load balancing and routing.

### ****Key-Value Data Store****
etcd is a consistent, distributed key-value data store used to persist k8s cluster state for it's object such as Pod, Deployment. It is an open-source project by Cloud Native Computing Foundation.

etcd adopts the [Raft Consensus Algorithm](https://web.stanford.edu/~ouster/cgi-bin/papers/raft-atc14). which is a leader with rest as followers, if one leader fails, other replace it as the new leader

In etcd, new data is appended only and no replace. The obsolete data is compacted (or shredded) periodically to minimize the size of the data store.


<!-- etcdctl: snapshot save -->

#### etcd Topology

<!-- In stage and prod, best to replicate data store in HA mode  -->


- Stacked etcd topologies: data store share resources with other components in the same node

- External etcd topologies: data stores are isolated from other components and consist of its etcd cluster



## Worker node

Worker node provides a running environment for client apps, and those apps are encapsulated in pods, controlled by controller agents.

**Pods** is the smallest scheduling work unit in k8s. It is a logitcal collection of one or more containers scheduled together, the collection can be start, stop, and rescheduled as a single unit of work.

Pods are scheduled on worker nodes, where they find required to compute, memory, and storage resources to run and networking to talk to each other and the outside world.

Also, in a multi-worker Kubernetes cluster, the network traffic between client users and the containerized applications deployed in Pods is handled directly by the worker nodes and is not routed through the control plane node.

A Worker Node runs the following components.

### Container Runtime

Container Runtime,  required on every node in k8s cluster,  manages container's lifecycle. Docker is one of the typical container runtime. 

### Kubelet
Kubelet is an agent running on each node in K8s cluster, and it makes sure containers are running in pods.
<!-- Kubelet connects to container runtime to run containers -->


### Kube-proxy

Kube-proxy maintains all networking rules on pods. forward connection requests to containers in the Pods

### Addons

Addons are not available in k8s but utilizing 3rd party tools.
For example, 
- DNS
- Dashboard user interface. UI for cluster management
- Cluster-level monitoring. Collect cluster-level container metrics and save them in a central store
- Logging. Collect cluster-level container logs and save them a central store



# Reference
- https://kubernetes.io/docs/concepts/overview/components/
- [edX: Introduction to Kubernetes](https://learning.edx.org/course/course-v1:LinuxFoundationX+LFS158x+1T2022/block-v1:LinuxFoundationX+LFS158x+1T2022+type@sequential+block@13caf57cc8d448a88e26e36fec502fb0/block-v1:LinuxFoundationX+LFS158x+1T2022+type@vertical+block@09dbdee2eb3146d6a6f11b82005fed7c)



