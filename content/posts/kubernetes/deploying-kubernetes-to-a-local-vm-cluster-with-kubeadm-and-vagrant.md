+++
date = 2021-01-11T20:00:00Z
draft = true
title = "Deploying Kubernetes to a local VM cluster with Kubeadm and Vagrant"

+++
I've wanted to learn how Kubernetes works so I've decided to spin up a cluster and get a better understanding of the components.

# Deployment options

There are multiple ways of deploying a Kubernetes cluster. For example, GKE from Google, AKS from AWS. There's even lightweight distributions of Kubernetes such as k3s and microkube for IOT/edge computing. Some organizations may also host Kubernetes inside their own data centers due to costs, performance or other engineering/business decisions.

For a beginner like myself, I didn't want to pay extra for physical or virtual machines and I don't want to be tied to a cloud provider. There's minikube which is great for learning Kubernetes for application developers but I wanted to learn how Kubernetes works from scratch.

# The components in a Kubernetes cluster

Regardless of the deployment option, the Kubernetes components should be more or less the same.

Each physical or virtual machine can be a master or worker node. The master node(s) are responsible for maintaining the cluster (ie scheduling) while the worker nodes runs pods which is a group of containers. Pods can be 1 or more containers (ie. application container and a sidecar monitoring container) and are considered the smallest deployable units in Kubernetes.

![](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

## Kubernetes Control Plane (aka master nodes)

Each of these components inside the control plane is a process inside a container.

### Kubernetes API Server

This server exposes the Kubernetes API which defines how to define, create and maintain the lifecycle of a pod/container.

### Persistent Store (Etcd)

This is where Kubernetes stores its cluster information.

### Controller Manager

These are processes that watch for specific states in the Kubernetes Cluster and help transition states. For example, the node controller is responsible for managing ensuring nodes are up.

All the controllers are compiled to a single binary.

### Scheduler

The scheduler is responsible for assigning nodes with pods.

## Kubernetes (Worker) Nodes

### Kublet

Manages containers in their respective pods that the its assigned to.

### Kube Proxy

Maintains the network layer for the node. It also acts as the network proxy for the Kubernetes node.

### Container Runtime

Kubernetes can run most container runtimes such as docker.

## Kubernetes Networking

Cluster Networking allows pods to communicate with each other. It's an important component inside the Kubernetes cluster that is probably overlooked by beginners because (1) it's not in the architectural diagram and (2) it's buried inside the docs.

Kubernetes has a basic network plugin which doesn't do pod-to-pod communication from different nodes(ie. pod A from node A to pod B from node B). Admins/Engineers will have to supply their own based on their needs.

For simplicity sake, I've narrowed down to flannel by CoreOS which seems to be the common route to go for self-hosted Kubernetes. Cluster networking is a pretty deep topic itself.

# Deploying to a local VM Cluster

## What you'll need

You'll need the following on your host machine

* VirtualBox
* Vagrant

Make sure you have at least 6 GB of free memory. Each k8s node requires at least 2 GB of memory.

## 1. Create the VMs

Clone the project which contains the vagrant file and bootstrap file

    $ git clone project
    $ cd project
    $ vagrant up

## 2. Initialize the cluster

## 3. Setup container networking

## 4. Add k8s worker nodes

## 5. Deploy sample application

# Conclusion

Setting up a simple Kubernetes cluster isn't too difficult to get started. Most of the overhead is figuring out what the components are doing. With vagrant and kubeadm inside the VMs, we can create a simple cluster. However, ensuring it's production-grade is another story.