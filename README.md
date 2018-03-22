# Kubernetes

### What is Kubernetes?

Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. Kubernetes is a Greek word which means `helmsman` or the pilot of a ship. K8s is an abbreviation derived by replacing the 8 letters "ubernete" with "8".

It is an open source project that was started by Google and derived from Borg, which is used inside Google for several years now for container management. Currently, it is hosted by Cloud Native Computing Foundation (CNCF).

Kubernetes is an abstraction that optimizes the utilization of resources like CPU and memory through containers, which allow for efficient distributions of applications across a cluster of nodes. K8s can run anywhere on bare metal or in any cloud provider infrastructure.

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