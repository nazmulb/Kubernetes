# Kubernetes

### What is Kubernetes?

Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. Kubernetes is a Greek word which means `helmsman` or the pilot of a ship. K8s is an abbreviation derived by replacing the 8 letters "ubernete" with "8".

It is an open source project that was started by Google and derived from Borg, which is used inside Google for several years now for container management. Currently, it is hosted by Cloud Native Computing Foundation (CNCF).

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

### K8s installation and our first cluster:

Kubernetes is supported on a variety of platforms. Here we will use Amazon Web Services (AWS) and <a href="https://github.com/kubernetes/kops">kops</a> (Kubernetes Operations) which provides the easiest way to get a production grade Kubernetes cluster up and running.

#### Step 1: Create AWS account:

You need an AWS account. Visit <a href="http://aws.amazon.com">http://aws.amazon.com</a> to get started.

#### Step 2: Install AWS CLI (Command Line Interface):

If you install `python 3`, `pip` will be installed automatically in macOS.

```js
brew install python3
pip install awscli --upgrade --user
```

After you install the AWS CLI, you may need to add the path to the executable file to your PATH variable. 

```js
vim ~/.zshrc
```

Add the line `export PATH=~/.local/bin:$PATH` in `.zshrc` and source the file.

```js
source ~/.zshrc
aws --version
```

#### Step 3: Install `kops` (Kubernetes Operations):

```js
brew update && brew install kops
```

#### Step 4: Install `kubectl` (CLI tool to manage and operate Kubernetes clusters):

```js
brew install kubernetes-cli
```

#### Step 4: Setup AWS IAM (Identity and Access Management) user:

AWS IAM enables you to manage access to AWS services and resources securely. In order to build clusters within AWS we'll create a dedicated IAM user for `kops`. This user requires API credentials in order to use `kops`. You can create the kops IAM user from the command line using the following:

```
aws iam create-group --group-name kops

aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMFullAccess --group-name kops
aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess --group-name kops

aws iam create-user --user-name kops

aws iam add-user-to-group --user-name kops --group-name kops

aws iam create-access-key --user-name kops
```

You should record the SecretAccessKey and AccessKeyID in the returned JSON output, and then use them below:

```
# configure the aws client to use your new IAM user
aws configure           # Use your new access and secret key here
```

```js
vim ~/.zshrc
```

Add the following two lines in `.zshrc` and source the file because "aws configure" does not export these vars for kops to use, we export them now:

```js
export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
```

```js
source ~/.zshrc
aws --version
```

You can see a list of all your IAM users here:

```js
aws iam list-users
```

You can also check the config as well:

```js
cat ~/.aws/credentials
cat ~/.aws/config
```

Please use <a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html">this doc</a> if you have any problem to configure.
