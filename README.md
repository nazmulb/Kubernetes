# Kubernetes

### What is Kubernetes?

Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. Kubernetes is a Greek word which means `helmsman` or the pilot of a ship. K8s is an abbreviation derived by replacing the 8 letters "ubernete" with "8".

It is an open source project that was started by Google and derived from Borg, which is used inside Google for several years now for container management. Currently, it is hosted by Cloud Native Computing Foundation (CNCF).

Kubernetes is an abstraction that optimizes the utilization of resources like CPU and memory through containers, which allow for efficient distributions of applications across a cluster of nodes. K8s can run anywhere on bare metal or in any cloud provider infrastructure.

### What are the features of Kubernetes?

- Container grouping using pod
- Self-healing
- Auto-scalablility 
- DNS management
- Load balancing
- Rolling update or rollback
- Resource monitoring and logging

### Kubernetes Architecture:

A Kubernetes cluster is made of a master node and a set of worker/slave nodes. A K8s setup consists of several parts, some of them optional, some mandatory for the whole system to function.

This is a high-level diagram of the architecture:

<img alt="Kubernetes Architecture" src="https://raw.githubusercontent.com/nazmulb/Kubernetes/master/images/kubernetes-architecture.webp" width="750px" />

Let’s have a look into each of the component’s responsibilities:

#### Master Node:
The master node is responsible for the management of Kubernetes cluster. This is the entry point of all administrative tasks. The master node is the one taking care of orchestrating the worker nodes, where the actual services are running.

Let's dive into each of the components of the master node:

##### API server:
The API server is the entry points for all the REST commands used to control the cluster. It processes the REST requests, validates them, and executes the bound business logic. It is the only component which stores the desired state in etcd.

##### etcd storage:
All configuration information about cluster states is stored in the etcd in the form of key/value pairs and it allows values to be watched for changes. An example of data stored by Kubernetes in etcd is jobs being scheduled, created and deployed, pod/service details and state, namespaces and replication information, etc.

##### scheduler:
It works with the API server to schedule workloads in the form of pods on the actual worker nodes. These pods include the various containers that make up our application stacks. By default, the basic Kubernetes scheduler spreads pods across the cluster and uses different nodes for matching pod replicas.

##### controller-manager:
Optionally you can run different kinds of controllers inside the master node. An example of such a controller is the Replication controller, which works with the API server to ensure that the correct number of pod replicas are running at any given time. This is exemplary of the desired state concept. If our replication controller is defining three replicas and our actual state is two copies of the pod running, then the scheduler will be invoked to add a third pod somewhere on our cluster. In this way, K8s is always pushing towards that desired state.

##### kubectl:
A command line tool to communicate with the API service and send commands to the master node.

#### Worker node:
The pods are run here, so the worker node contains all the necessary services to manage the networking between the containers, communicate with the master node, and assign resources to the containers scheduled.

##### Pods:
Pods allow you to keep related containers close in terms of the network and hardware infrastructure. Data can live near the application, so processing can be done without incurring a high latency from network traversal. Similarly, common data can be stored on volumes that are shared between a number of containers. 
Pods essentially allow you to logically group containers and pieces of our application stacks together.

While pods may run one or more containers inside, the pod itself may be one of many that is running on a Kubernetes worker node. Pods give us a logical group of containers that we can then replicate, schedule, and balance service endpoints across.

##### Docker:
Docker runs on each of the worker nodes, and runs the configured pods. It takes care of downloading the images and starting the containers.

##### kubelet:
kubelet gets the configuration of a pod from the API server and ensures that the described containers are up and running. This is the worker service that’s responsible for communicating with the master node.
It also communicates with etcd, to get information about services and write the details about newly created ones.

##### kube-proxy:
kube-proxy runs in each node for load distribution among the pods and makes services available to the external host. It uses iptable rules or round robin to forward requests to the correct containers.
