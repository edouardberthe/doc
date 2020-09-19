# Basics

Notions:

- Pod: smallest unit in Kubernetes. A pod can be a set of several containers. For instance an app Node.js + a database PostgreSQL with it.
- Node: a Node is a **worker machine**, it can be a physical or virtual machine, on which there will be several Pods. A Node has at least:
    - a Docker runtime (to pull images, run the containers inside the pods, etc.)
    - a Kubelet, which is the process allowing to communicate with the Master Node
- Deployment:
- Service: abstraction which defines a logical set of Pods and a policy by which to access them. Pods can die, and can be replaced, but we need something to store the identification of each one (for instance IPs)

# Main commands

- `kubectl get`: list resources
- `kubectl describe`: show detailed information about a resource
- `kubectl logs`: print the logs from a container in a pod
- `kubectl exec`: execute a command on a container in a pod

Pods are **mortal**. They have a lifecycle.


# Minikube

Minikube is a tool used to test Kubernetes. It creates a Kubernetes cluster with only one Nod, inside a Virtual Machine.
Some useful commands are available with it:

`minikube service <service-name>`

`minikube dashboard` --> extremely useful. Open a Dashboard to have an overlook of the cluster (only one node because of Minikube but still cool).

[Minikube with hyperkit](https://blog.arkey.fr/2018/06/18/minikube-with-hyperkit/)

# How to interact with Kubernetes

They are three ways to interact with Kubernetes:

- Imperative commmands
- Imperative object configuration
- Declarative object configuration