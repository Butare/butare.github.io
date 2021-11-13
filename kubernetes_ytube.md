# Kubernetes

## Service

- Has static IP
- Service is a load balancer

## ConfigMap component
- External configuration of your application
- ConfigMap would usually contains configuration data like DB_URL, ect...
> Don't put username & password in ConfigMap use Secret instead.
> 

## Secret
- Used to store secret data
- base64 encode

> The built-in security mechanism is not enabled by default
> 


## Volumes
- Basically, attaches a physical hard drive to your Pod. That storage can be on local machine (same machine as pod) or remote storage

- Think of storage as an external hard drive that plugged in to Kubernetes cluster because Kubernetes doesn't manage data persistance. Pods just have the reference on the volumes
- A user or admin is responsible for backing up the data, and making sure data are keept on a proper hardware, etc..


## Replicating mechanisms like `Deloyment` & `StatefulSet`

- Instead of relying on one node, we deplicate or clone the application's Pod on multiple servers
- In order to create a 2nd or another Pod's replica, you don't create an other Pod, instead you have to define a blueprints for Pods known as Deployment
- `Deployment` is a blueprint for the application Pods. i.e, a Pod is a running deployment
- In practice, you'll not be creating Pods, you will create Deployment (Deployment is analogous to container's `image`)
- We say a `Pod` is abstraction layer on top of a `container`, and a `Deployment` is abstraction layer on top of a `Pod`

> DB Pod can't be replicated via Deployment because DB has a state which is data. instead you can use `StatefulSet`
> 

## StatefulSet component
- It's meant for Stateful applications like DBs
- However, Deploying a StatefulSet is not easy that's why it's a common practice to host DB applications outside of Kubernetes cluster


# Kubernetes Architecture

## Node Processes

### Worker machine in K8s cluster
- Each node has multiple Pods on it
- 3 processes must be installed on every node
- Worker nodes do the actual work

- 1st process that needs to run in every node is `Container runtime` (e.g: Docker)
- 2nd process is `Kubelet` services
	- Interacts with both - the container & node (machine)
	- starts the pod with a container inside
- 3rd process is `kube proxy` allows multiple nodes to interact by forwarding the requests to any Pod or replica

## Master processes
- 4 processes run on every master node

1. Api Server
	- when a user want to deploy an application to kubernetes cluster you interact with api server
	- `api server` is like a cluster gateway
	- `api server` acts as a gatekeeper for authentication
	- Its good for security because there's only entrypoint to the cluster

	```
	Some request --> Api Server --> validates request --> .. other processes ... -> Pod
	```


2. Scheduler
- Scheduler just decides on which Node a new Pod should be scheduled

	```
	Schedule new Pod --> Api Server --> Scheduler --> where to put the Pod? --> Kubelet
	```
    
3. Controller manager

- Detect cluster state changes in order to recover ASAP in case a pod dies

	```
 	Controller manger --> scheduler --> Kubelet
	```
    
4. etcd

- `etcd` key value store, it's like a cluster brain
- Cluster changes get stored in the key value store (etcd)


## Example of Cluster Set-Up


### Add new Master or Worker Node server
1. get a new bare server
2. install all the master/worker node processes
3. add it to the cluster


## Minikube and Kubectl (Local setup)
- What is minikube?
- What is kubectl?
- How to set-up Minikube cluster?

## 

## 1. What is Minikube?

### Production Cluster setup
- Multiple Master (atleast 2)
- Multiple Worker nodes
- Separate virtual or physical machines that each represents a node (if you don't have enough resources the application management would be hard or even impossible) so minikube is a rescue

- With a minikube, both Master and Worker node processes run on ONE machine. but you should have a docker container pre-installed.

Minikube will;
- create virtual box on your PC
- create Node runs in that virtual box

Therefore, Minikube is 1 Node K8s cluster that runs in VirtualBox on your PC which is used for testing purposes

After creating Node using Minikube, you will need to `kubectl` to interface with cluster, components...

## 2. What is kubectl?
- kubectl is command line tool for k8s cluster

Master node has a process called `Api Server` which is entry point to the cluster, if you want to do anything you go thru it.
To access the Api Server you can use one of these 3 clients:
- UI
- API endpoint
- CLI which `kubectl`

Kubectl is most powerful of above clients

Kubectl is not only meant for minikube cluster, it can also be used on Cloud cluster

- `kubectl` CLI, for configuring the Minikube cluster 
- `minikube` CLI, is for start up/deleting the cluster

## Main Kubectl commands

1. check node: `kubectl get nodes`
2. check pods: `kubectl get pods`
	- pod name consists of `<podName>-<replicaId>-<podId>`

3. check pods with more ouptput: `kubectl get pod -o wide`
4. check services: `kubectl get services`
5. check the service details: `kubectl describe service [service-name]`
6. create deployment: `kubectl create deployment <name> --image=<image-name>`
	> e.g: kubectl create deployment nginx-depl --image=nginx
	> 
	- a deployment is a blueprint for creating Pods
	- That command is most basic configuration for deployment (name & image to use)
	- the rest is defaults settings 


- Between `deployment` and `Pod` there's an other layer called `replicaset` which is automatically managed by Kubernetes deployment.
	- `$ kubectl get replicaset `

- Replicaset is managing the replicas of a Pod


### Layers of Abstraction

- `Deployment` manages a `ReplicaSet`
- `ReplicaSet` manages a `Pod`
- `Pod` is an abstraction of `Container`
- Everything below `Deployment` is handled by Kubernetes
	- e.g: edit deployment: `kubectl edit deployment <deployment-name>` 
		- when successfully edited, the old pod will be terminated, new pod will be created with the new settings 


### Debugging Pods
- check pod logs: `$ kubeclt logs [pod-name]`
- check the pod's decriptions: `$ kubectl describe pod [pod-name]`
- To enter inside the pod/container: `$ kubectl exec -it [pod-name] -- bin/bash`


### Delete deployment 
- Delete: 
	- use deployment name: `$ kubectl delete deployment [deployment-name]`
	- use deployment config file: `$ kubectl delete -f [path/to/deployment-configuration.yaml]`
- 
> CRUD operations happen on deployment level, and everything underneath follow automatically. ie. if you delete deployment, it's `replicaset`, `pod`, and `container` will be deleted as well.

### Apply configuration file
- When you create a deployment with few settings you can use a command line, however, in practice it's better to add a deployment settings into a configuration file, and execute that file.
- To create or update a deployment or service by using a configuration file: `$ kubectl apply -f [path/to/file-name]`


## YAML Configuration File
- Main tool for configuring and creating Kubernetes components inside the cluster
- Sample


```ymal
 apiVersion: apps/v1
 kind: Deployment  # component
 metadata:
   name: nginx-deployment
   labels:
     apps: nginx
 spec: # specs for deployment
   replicas: 2
   selector:
     matchLabels:
       app: nginx
   template: # pod specs start from here
     metadata: # for a pod
       labels:
         app: nginx
     spec: # blueprint for a pod
       containers:
         - name: nginx
           image: nginx:1.16
           ports:
           - containerPort: 80
```

### The 3 parts of K8s configuration file
- Every configuration file (deployment config file, service config file,...) in K8s has 3 parts
1. First part is a `metadata` of a component (i.e; service, deployment, ...) you're creating
2. Second part is `specification` of a component. The attribute of `spec` which are specific to the kind of component you're creating
3. Third part is `status` it's automatically generated and added by Kubernetes.  K8s will be validating what's the `desired` Vs `actual` status of the component. 
	- if `desired` != `actual` K8s will try to fix 
	- K8s will update the state continously
	- K8s uses data from `etcd` to keep track of the status. since, etcd holds the current status of any K8s component

### Format of configuration file
- extention is `YAML`
- try to use ymal validator plugin for YAML syntax validation
- Recommended to store config file with your application code or you can create a git repo for the config files
- pod specifications are inside the component's `spec.template.spec`

### Connecting Deployments to Service to Pods

#### Connecting components (Labels & Selectors & Ports)
- The way connection is established is using `labels` and `selectors`
	- metadata parts contain labels:
		- `<component>.metadata.labels`, and
		- `<component>.spec.template.metadata.labels`
	- spec part contains selector
		- `<component>.spec.selector` 

##### Connecting Deployment to Pod
- In metadata you give any key-value pair for component 
	- .eg; 
	```yaml
	 	labels:
			app: nginx   
     ```      
- Pods get the label through the template blueprint (i.e; `<component>.spec.template.metadata.labels` )
- The Pods label is matched by the selector (i.e; `<component>.spec.selector`) to create connection

	- e.g;  
 		```yaml
		selector:
    		matchLabels:
        		app: nginx
    	```
##### Connecting Services to Deployments

Services and Pods/deployments are connected by the following:
1. `selectors and labels`: 
To connect service to deployment and pod, i.e; the `service.spec.selector`, `deployment.metadata.labels`, and `deployment.spec.template.metadata.labels` should have the same `key:value`.
- In otherwords, service uses selector to select the deployment and pods whose labels' `key:value` are same as service's selector's `key:value`
e.g:

	- deployment
		```yaml
        ...
    	metadata:
        	name: nginx-deployment
        	labels: # for deployment
	              apps: nginx
        spec:
        	...
            template:
               metadata:
                  labels: # for pod
                     app:nginx 
        ...
    	```
        
     - service
     	```yaml
        ...
        spec:
          selector:
            app: nginx
        ...
        ```
 2. `ports`

	Service get connected to the pod via port number, since a service can support many pods/replicas it may have multiple ports for each pod
    `service.spec.ports.targetPort` & `deployment.spec.template.spec.containers.ports.containerPort` should be the same
    
- Notice a service also has it's own port that other services use to connect to it (i.e; `service.spec.ports.port`).
- However, a service should know which pod to forward requests to, via targetPort label (ie; `service.spec.ports.targetPort`) which indicates the pod's port (i.e; `deployment.spec.template.spec.containers.ports.containerPort`).
        

- How to check if a service has the correct replicas/pods endpoints

	1. run: `kubectl describe service [service-name]` then, check the endpoints (each endpoint connect to each replica/pod)
	2. check the IP address of a pod: `kubectl get pod -o wide` 


#### Deployment Status automatically created
- To check deployment's status: 
	- `kubectl get deployment [deployment-name] -o yaml` 

	or you can send the output to the file
    - `kubectl get deployment nginx-deployment -o yaml > nginx-deployment-result.yaml`


## PROJECT DEMO
- We will deploy 2 applications; 
	- `mongo-express` (web based mongoDB admin interface), and
	- `mongoDB`
- Demo K8s components
  ```
  2 deployment
  2 service
  1 ConfigMap
  1 secret
	```

	- MongoDB (deployment)
		- Deployment or pod (container)
		- Internal service (accessible within k8s only)
    - Mongo Express (deployment)
    	- Deployment or pod (container)
    	- Env variables (for db connection, username & password,...) declared in Deployment.yaml
    	- External Service (need to be accessed thru a browser) 
    		- browser -> external service -> pod 
    		- url: IP address of Node
    		- port: of external service
    - ConfigMap
    	- DB Url
    - Secret
    	- DB user
    	- DB password     	 	


        
       
        
        

