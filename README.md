# Kubernetes

Kubernetes or K8s is an open-source container orchestration system for automating software deployment, scaling, and management, which was devloped by Google and now mantined by Cloud Native Computing Foundation. It is a platform for managing application containers across multiple hosts. It provides lots of management features for conatiner-oriented applications, such as auto scaling, rolling deployment, compute resources and volume management. 

K8s considers mnost of the orperational needs for appliation containers. The highlights are:

 - Container Deployment.
 - Persistent Storage.
 - Container health monitoring.
 - Compute resource management.
 - Auto-Scaling.
 - High Availability by cluster federation. 

# Kubernetes Architecture

![image](https://github.com/CraftyCancer/Kubernetes/assets/113592437/bc3602c9-e986-4551-848f-be120e666f93)

As per the above image we are able to see the K8s includes two major players, Master & Nodes.
Before getting deep into the Architecture understanding, lets understand some terminalogy which we will be using in the learning factor.

## Kubelet

Kubelet is an agent that runs on each node in a cluster. It listens the infstructions from the KUBE API Server ( to deploy or destroy the containers). The kubelete in the K8s worker nodes, register the node within the K8s Cluster.

When it recieves an instruction to lad a container or pod on the node, it request CRE(Container Runtime Engine) to pull the required image and the run the instance. 

The Kubelet monitors the pods and reports it back to the respective compoments for keeping alive the container or pod. 

## Master Node

Master Node is reponsible for managaing the Kubernetes cluster (Manage, Plan, Schedule, Monitore Nodes).
Under Master Node we are having different components: 

1. API Server.
2. ETCD.
3. Kube-Scheduler.
4. Controller Manager.

Lets Get Deep into all this components: 

### 1. API Server

The Kube API Server is the primary component in Kubernetes.

When we run a kubelet command, the Kubelet utilty is in fact reaching to the Kube-apiserver.

1. The Kube-Api Server initial authenticate the request and validate it.
2. API Server provides an HTTP/HTTPS server, which provides a RESTful API for all the compoments in the K8s master. (Example: GET reosuce status such as pod, POST to create a new resoruce and also watch a resource. API Server reads and updates ETCD, which is K8s backend data store.
