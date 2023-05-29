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

## Master Components

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

### 2. ETCD

ETCD is a distributed reliated key-value store that is simple, secure & fast.

Some points to understand what is Key-Value Store:
1. A Key value store the infromation in the from of document or pages.
2. The ETCD servive runs on port 2379 (default port).
3. ETCD Control Client is a command line tool for ETCD. We can use it to store & retrieve the Key-Value pairs. 

The below command is to store the data in store:
> etcdctl set key1 value1

The below command is to retrieve the data from store:
>etcdctl get key1 

### 3. Kube Scheduler

The Kube Scheduler is only responsible to decide which pod will run on which node. It doesn't place the pod on specified or any node, it just decides.
The Sceduler treis to look at each pod(specification) and then assign the nodes to them. Those specification may be CPU, Memory or more.

The pods assigned to specific nodes as per the 

**Filter Nodes:** As per the CPU & Memory requirment the scheduler filter the Nodes for the specific pod. 
**Rank Nodes:** The Scheduler checks for the later resoources whcih can be used by other pods (avilable on the Node), on the basis of that the pods are assigned to node.


### 4. Kube-Controller Manager

The controller manager is used to check the details or status of the pods or nodes are running or no. 

- Watch Status
- Remediate Situation

The controller manager controls lots of different things in the cluster. A Controller is a process that continuously monitor the state of various compoments within the system & work towards keeping the whole cluster working as per the deseried state.

**Node-Controller**

The Node Controller is reponsbile for monitoring the status of nodes & takes the necessary action for keeping the nodes running.
Monitoring for the Node-Controller is done through Kube-API Server. 

![node-controler](https://github.com/CraftyCancer/Kubernetes/assets/113592437/ad8a3cc1-23e2-474f-8042-a6298a76447b)

Node Controller checks the status of every nodes in 5 Seconds.
If any of the node stops working, the heartbeat will be marked as "Unreachable" (it will wait for 40s).

Node Controller will wait for 5 mins to check if the nodes come back to reachable or running state orelse it will assign the pods to the new node.

> --node-monitor-period= 5 Sec

> --node-monitor-grace-period= 40 Sec

> --pod-eviction-timeout = 5 Min  

**Replication Controller**

The replication Controller is used for monitoring the replica set for the pods, if they are not matching with the desried state, then RC will go ahead make the state equal.

> Number of PODS Running = Desired State

**What does the above equal sign means?**

Lets take a scenario. 

We are having an application, which needs to be running on 2 pods as alive. So this is an desired state for us. POD=2
In any case if the pods die and it become less then 2, the replication controller will take care of bringing back the pod to alive and match the desried state.


## Kubelet

Kubelet is an agent that runs on each node in a cluster. It listens the infstructions from the KUBE API Server ( to deploy or destroy the containers). The kubelete in the K8s worker nodes, register the node within the K8s Cluster.

When it recieves an instruction to lad a container or pod on the node, it request CRE(Container Runtime Engine) to pull the required image and the run the instance. 

The Kubelet monitors the pods and reports it back to the respective compoments for keeping alive the container or pod. 

## Kube-Proxy

Within a K8Ss cluster, every pod can be reached to other pod. This is accomploished by deploying a pod networking solution to the cluster.

A **pod network** is an internal virtual network that spans across all the nodes in the cluster to which all pods can connect to. 

Kube-Proxy is a process that runs on each node in the K8S Cluster. It's job it too look for new services. If a service is not created, we need to create approiate rule for each node to forward those traffic to services to the backend pods. (It creates a IPTable on each pod & will be able to access other pod). 

## POD

A Pod is an single instance of an application. 

Before getting into next level of creating pods, deployment, RS (replica set) & services, Will go through a basic installtion of running a local cluster for going ahead with practial and hands-on our K8s Cluster.

## Life Cycle

The mentioned Life cycle is additional and which gives us on minimal data how the GET, PUT request works under Kube API-Server and include other compoments, 

![lifecyle](https://github.com/CraftyCancer/Kubernetes/assets/113592437/6075ab5f-4398-4796-9e9e-26c86cb8d18b)

# Installtion & Working with K8s - Local Cluster

**Requirements:**
1. Docker Desktop.
2. Minikube.

Find the Minikube Installation from the specificed website: https://minikube.sigs.k8s.io/docs/start/
Under the mentioned page, you can find the installtion on different Operating System.

Once the both the installtion is done, we can go ahead and start the minikube cluster by,

> minikube start --driver=docker

Once you run the above command, you will see the below details:

<img width="683" alt="minikube start" src="https://github.com/CraftyCancer/Kubernetes/assets/113592437/3b4c6a5b-ac7e-4864-83d8-ed59a0a0b8ac">

To verify the K8s 9s enabled and working on minikube, run the below command:

> kubectl get svc

![image](https://github.com/CraftyCancer/Kubernetes/assets/113592437/a71d81f6-85af-49aa-ad52-1997eeeeefb1)



**Lets Start with Creating & Deleting Resource on our local cluster**


