# Kubernetes

Kubernetes or K8s is an open-source container orchestration system for automating software deployment, scaling, and management, which was devloped by Google and now mantined by Cloud Native Computing Foundation. It is a platform for managing application containers across multiple hosts. It provides lots of management features for conatiner-oriented applications, such as auto scaling, rolling deployment, compute resources and volume management. 

K8s considers most of the orperational needs for appliation containers. The highlights are:

 - Container Deployment.
 - Persistent Storage.
 - Container health monitoring.
 - Compute resource management.
 - Auto-Scaling.
 - High Availability by cluster federation. 

# Kubernetes Architecture


![Archi](https://github.com/CraftyCancer/Kubernetes/assets/113592437/1bb9a6ff-5313-4060-a109-689676168cda)


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

![node-controler](https://github.com/CraftyCancer/Kubernetes/assets/113592437/83e63451-4ad8-46c7-9fe7-69da4f5aedc4)


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

![lifecyle](https://github.com/CraftyCancer/Kubernetes/assets/113592437/b40ab64a-fb21-4600-b100-f3cc91a9d667)


# Installtion & Working with K8s - Local Cluster

**Requirements:**
1. Docker Desktop.
2. Minikube.

Find the Minikube Installation from the specificed website: https://minikube.sigs.k8s.io/docs/start/
Under the mentioned page, you can find the installtion on different Operating System.

Once the both the installtion is done, we can go ahead and start the minikube cluster by,

> minikube start --driver=docker

Once you run the above command, you will see the below details:

<img width="683" alt="Minikube-start" src="https://github.com/CraftyCancer/Kubernetes/assets/113592437/8424c69b-a470-4b16-aef1-92e4a428c992">


To verify the K8s 9s enabled and working on minikube, run the below command:

> kubectl get svc

<img width="683" alt="get-svc" src="https://github.com/CraftyCancer/Kubernetes/assets/113592437/1cebdb5e-7c14-44d8-a2c6-9ae2d66a4ad6">

**Lets Start with Creating & Deleting Resource on our local cluster**


## Creation of deployment,service or pods using yaml

K8s uses the yaml as input for object such as pods, replica set, deployment, services etc.

A K8s definition file always contains four top level fields as:
 - apiVersion
 - kind
 - metadata
 - spec


| Kind    | apiVersion |
| ------- | ---------- |
| Pod     | v1         |
| Service | v1         |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

**Example:**

```
apiVersion: v1
kind: Pod
metadata: 
    name: 
spec:
    container: 
```

Under the metadata and spec the name and container are the key value pair, which we need for the deploying the container on specific pod, but the other mentioned fields are mandatory to be in the yaml file.

## Create a POD

Pod is the smallest deployable unit  in K8s. It can contain one or more containers. 

Lets get started with the creation of single container pod on the our K8s Cluster. 

https://github.com/CraftyCancer/Kubernetes/blob/8fd74965b6d88c2242dcf4d83aa6bb7ec4785dc4/Pod/pod-def.yaml#L1-L8

Use the above yaml file and then deploy that on K8s cluster. Use the following command.

> kubectl create -f pod-def.yaml

This will create a pod under the default namespace.

> kubectl get pods

![image](https://github.com/CraftyCancer/Kubernetes/assets/113592437/828a7aaa-5b4f-4895-b0cb-55f2825e3589)

The above pod is created with an single container, lets check how to get it done with two conatiners. 

https://github.com/CraftyCancer/Kubernetes/blob/9f5c8ed8e9ac7d1f67c07637857c65e8b4ad275b/Pod/pod-two-cont.yaml#L1-L11

Use the above yaml file and then deploy that on K8s cluster. Use the following command.

> kubectl create -f pod-two-cont.yaml

This will create a pod under the default namespace.

> kubectl get pods


![image](https://github.com/CraftyCancer/Kubernetes/assets/113592437/8d57f389-90d9-4de2-8c2e-ca97842be7b8)

In this way, we can deploy the pods using single and multiple containers.

To get a detailed information about the pod, use the following code:

> kubectl describe pod pod-name

To check the logs for specific pod we can use this command,

> kubectl logs pod-name

To check the logs for specifc container, we can use this command,

> kubectl logs pod-name -c container-name

To delete the pod from default namespace

> kubectl delete pod pod-name

### Shortcut 

To run a pod without creating a manifest file can you be done using this command

> kubectl run nginx --image nginx

To Store the manifest pod file, without creating the pod, can be used by:

>  kubectl run nginx-1 --image nginx --dry-run=client -o yaml > client.yaml

This above command will be just used for pod manifest creation, which will be stored under client.yaml


## ReplicaSet(RS) / ReplicationController(RC)

A pod is not self-healing. When a pod encounters failure, it won't recover on its own. ReplicaSet & ReplicationController comes into play. Both of these will ensure a specified number of replica pods are always up and running in the cluster. If a pod crashes for any reason, RS & RC will request to spin up a new pod. 

![RC](https://github.com/CraftyCancer/Kubernetes/assets/113592437/638b2812-b566-406f-b38d-7cb4961e191e)

As per the above diagram, we would like to create ReplicationController object with the desired count as two. 

Lets take an example to work on the RC.

https://github.com/CraftyCancer/Kubernetes/blob/4c84dc5b0f56d5382412558af703971f8317906b/Replica-Set/rc-example.yml#L1-L21

Use the create command to apply the yaml which is used for RC.

![image](https://github.com/CraftyCancer/Kubernetes/assets/113592437/7c453b2e-a407-417a-b884-ffb23b5addb2)

To get the current status of RC, we use the below command

>kubectl get rc

![image](https://github.com/CraftyCancer/Kubernetes/assets/113592437/a7075cfe-00ed-4187-afd6-2b01d2595f58)

Lets, try one more thing, Since we saw the two RC Pods came up, we can use the same labels or creation of Pod.

Use the below yaml and create the pod & lets see the result.

https://github.com/CraftyCancer/Kubernetes/blob/9d1cfb80bee9d99180ba9087295070fa1205edb1/Pod/pod_rc.yml#L1-L13

Once we create the pod, we will be seeing like the pod went into termination state and the pod dies, because the RC takes up the selector & check the desired state is matching or no. So the pods gets killed.

### Scale of Replicas Count

If we want to scale on demand, we could simple edit the K8s yaml file and the update replicas spec.

So if we want to scale the pods from 2 to 6, we can just edit and make changes.

> kubectl edit rc nginx

Other method from Scale Up/Down the ports, We can use this command:

> kubectl scale --replicas=4 replicationcontroller/nginx

To delete a RC, we can use the command:

> kubectl delete rc rc-name

## Deployment

A Deployment provides declarative updates for Pods and ReplicaSets.

![Deployemtn](https://github.com/CraftyCancer/Kubernetes/assets/113592437/6b4de42e-d696-4b35-8d64-e22ffdd04311)

Lets try to deploy the pod using the deployment file, which consist the pods & replicaset.

https://github.com/CraftyCancer/Kubernetes/blob/031ba2d48042e8773bc191131d49883fa2e0affc/Deployment/example.yaml#L2-L21

> kubectl apply -f example.yaml

Once this is deployed we use the following codes to view the deployment on our cluster

> kubectl get deploy

Use the same command to get pods.

> kubectl get pod

To create a yaml using the K8s command:

> kubectl create deployment deployment-name --image=image-name --dry-run=client -o yaml > deployment-name.yaml

Example:

> kubectl create deployment nginx-deploy --image=nginx --dry-run=client -o yaml > nginx-deploy.yaml

To directly deploy the deployment use the following code:

> kubectl create deployment nginx-deploy --image-nginx -o yaml> nginx-deploy.yaml

 
