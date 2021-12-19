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

1. Check all components: `kubectl get all`
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

## Create a secret component

```yaml
apiVersion: v1
kind: Secret 
metadata:
  name: mongodb-secret # a random name
type: Opaque # default for arbitrary key-value pairs
data: # actual contents
  mongo-root-username: bW9uZ28tdXNlcm5hbWU=  # encoded in base64 for security reasons
  mongo-root-password: bW9uZ28tcGFzc3dvcmQ= 
```
Note that:
- `mongo-root-username` & `mongo-root-password` are the key that will be used by other components
- Storing the data in a Secret component doesn't automatically make it secure. There are built-in mechanism (like encryption) for basic security, which are not enabled by default

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
		- `Deployment` or pod (container)
		- Internal `service` (accessible within k8s only)
    - Mongo Express (deployment)
    	- `Deployment` or pod (container)
    	- Env variables (for db connection, username & password,...) declared in Deployment.yaml
    	- External `Service` (need to be accessed thru a browser) 
    		- browser -> external service -> pod 
    		- url: IP address of Node
    		- port: of external service
    - `ConfigMap`
    	- DB Url
    - `Secret`
    	- DB user
    	- DB password     


### Browser Request Flow through the K8s components

```
Browser -> Mongo external service -> Pod (Mongo express) -> Mongo DB Internal Service (via DB url) -> Pod (Mongo DB)
```
- Since the deployment will use credentials from Secret deployment, the `Secret component must be created before the Deployment`
- After creating a Secret component, it can be referenced in Deployment  

	- Create a secret: `kubectl apply -f <secret ymal file>`
	- Show the secret: `kubectl get secret` 
- reference secret from secret component:
	- secret name (`Secret.metadata.name`): `deployment.spec.template.spec.containers.env.name.valueFrom.name`
	- secret key or variable (`Secret.data.[key-name]`)   `deployment.spec.template.spec.containers.env.name.valueFrom.key`       
 ### Create an `external service` for mongo deployment in order to be accessed by other services
 
 - Deployment & Service are usually in 1 yaml file, because they belong together.
 	- 1 YAML file can hold multiple documents, just add `---` (3 hyphens) between the documents  
 
## Create Mongo Express deployment, & It's external Service, and ConfigMap
 
 ConfigMap contains the following configurations:
 - external configuration
 - centralized configuration
 - other components can use them

Note:
- Since the ConfigMap is used by other components, it should be available in the K8s cluster before other components reference it.

```
 - The order of adding components in the K8s cluster matters! 
 	- start with the components that are depended on by others 
 
 e.g: 1st deploy ConfigMap, then Deployment component
```

## How to make a Service component an External Service?
- Specify the type as `LoadBalancer`; 
	- `Service.spec.type: LoadBalancer`
- assign service an `external IP address` to accept external requests via `nodePort` (port for external IP address that will be used in a browser to access the service),
	- nodePort has range: `30000 - 32767` 
	- `Service.spec.ports.nodePort: 30000`

Note: internal service also acts as a loadbalancer!

- check service: `kubectl get service`

eg:

|NAME            | TYPE         | CLUSTER-IP    | EXTERNAL-IP | PORT(S)       | AGE|
|---             |----          |----           |---          |---            |--- |
|mongoex-external| LoadBalancer | 10.96.178.16  | `<pending>` | 8081:30000/TCP| 5s | 
|mongodb-internal| ClusterIP    | 10.96.86.105  | `<none>`    | 27017/TCP     | 8s | 

### Internal service vs. External service

- Type: 
	- Internal service type is`ClusterIP` which is a DEFAULT type.
		- ClusterIP type will give a service an internal IP address (e.g: 10.96.86.105) 

	- External service type is `LoadBalancer`, should be specified in the service config file. 
      -  LoadBalancer will also give a service an Internal IP address (e.g: 10.96.178.16)
      -  LoadBalancer assigns a service an External IP address (e.g: `<pending>` is for minikube, but for regular cluster, it should be a regular IP address)
			- to set an external IP address to the service in minikube: `$ minikube service [service-name]`   

- Port(s):
	-  Internal service has only internal port number for that service (e.g: `27017/TCP`)
	-  External service has 2 IP addresses for both Internal IP & external IP (e.g: `8081:30000/TCP`)

		To run the external service in browser: http://external-ip:external-port
e.g: http://192.168.49.2:30000

 
 
 
 # K8s NAMESPACES EXPLAINED
 
 ## What is a Namespace?
 
 > `$ kubectl get namespace`: to get the namespace
 
 - In K8s you can organize resources in namespaces (like packages in Java)
 - Namespace is like a virtual cluster inside a K8s cluster
 - 4 namespaces are created by default when create a new cluster

### default namespaces
- `kubernetes-dashboard`: 
	- interacts directly only with minikube
	- It's specific to minikube installation, this namespace will not exists in a standard cluster

- `kube-system`:
	- Is not made to be used by the user. User don't create or modify it in kube-system
	- The components that are deployed in the `kube-system` are:
		- System processes
		- Master and Kubectl processes

- `kube-public`: 
	- Contains publicly accessible data
	- A ConfigMap, which contains cluster information can be accessible even without authentication
	- check cluster info: `$ kubectl cluster-info`

- `kube-node-lease`:
	- get heartbeats of nodes
	- each node has associated lease object in namespace
	- contains the information that determines the `availability of a node`

- `default`:
	- The resources created by the user are located here


## Create a new Namespace
- Using command:
	- `$ kubectl create namespace [namespace-name]`

 
 ## What are the use cases?
 1. Namespaces are needed to avoid putting all resources (deployment, replicaset, services, configmaps, ...) in a Default namespace, hence easier to manage
	 - It's better to logically grouping the resources in Namespaces. 
	 - eg; you can have;
	 	- Database namespace, 
	 	- Mornitoring namespace,
	 	- Elastic Stack,
	 	- Ngix-Ingress,
	 	- ...
 2. Avoid conflicts among the teams working on the same application/cluster
 	- If two teams use the same namespace, it's possible to silently overwrite the resource created by an other team by creating a resource with the same name but different configuration.
 	- Better to have a namespace per team or project

3. Resource sharing: e.g; Staging and Development
	- we can create namespaces for shared resources contains configurations (e.g Nginx-Ingress controller, Elastic Stack,...) that can be shared by both Staging & Development in one cluster
4. Resource sharing: e.g; Blue/Green Deployment
	- We may need to create different versions of production environment but both share same resources without setting up a separate cluster.
		- e.g: Production Blue & Production Green deployment both share the configs in (Nginx-Ingress controller, Elastic stack, ...) 

5. Access and Resources Limits on Namespaces

- Two teams working in the same cluster but different namespaces, you can limit namespace access only to one team.

## Charactericts of Namespaces
- You can't access most resources from another Namespace.
	- e.g: Each Namespace must define it's own ConfigMap and Secret, even if they refer to the same reference 
- However, Service can be accessed from different Namespace
- Components which can't be created within a Namespace 
	- e.g: Node, Volume,... 
		- Those that live globally in a cluster
		- You can't isolate them 
 
 ## How Namespaces work and how to use it?
 > Before using a Namespace it should exist
 > 
- check the namespaces: `$ kubectl get namespace` or `$ kubectl get ns`

   #### 1. Put a component into default namespace

 	- If you create a component without specifying a namespace, it will be created in a `default` Namespace

	- Check component in a Namespace: `$ kubectl get [component-kind] -n [namespace-name]`
		- e.g; check configmaps in default namespace: `kubectl get configmap -n default`
		- Note that if you don't specify the namespace, components in default namespace are shown. i.e: `kubectl get configmap` is same as `kubectl get configmap -n default`

	- Find the Namespace of a all components by checking it's details: `kubectl get [component-kind] -o yaml`
		- e.g: ConfigMap details: `$ kubectl get configmap -o yaml` 

	- namespace of a specific component: kubectl get [component-kind] [component-name] -o yaml
		- e.g: `kubectl get configmap mysql-configmap -o yaml` 

  #### 2. Put a component in a specific/custom namespace by command
  
 
  - `$ kubectl apply -f [path/to/.yaml] --namespace=[namespace-name]`
  	- e.g: `$ kubectl apply -f mysql-configmap.yaml --namespace=my-namespace`

  #### 3. Put a component in a Namespace using a configuration file (recommended):
	
    ```yaml
    apiVersion: v1
	kind: ConfigMap # It's a kind of ConfigMap
	metadata:
  		name: mysql-configmap
  		namespace: my-namespace # namespace name
	data:
  		db_url: mysql-service.database
    ```

## Change Active Namespace
- It may inconvenient to always remember to assign a Namespace to the compoment by command in order to see them. Therefore, it's possible to set active namespace using a tool called `kubens`.
- To install kubens (MacOs): `brew install kubectx`  
- list namespaces: `$ kubens`  (the active one will be highlighted)
- to change active namespace: `$ kubens [namespace-name]`

- After changing the active namespace -- long command.
	- Before changing active namespace: `$ kubectl get configmap -n db-namespace` 
	- After changing active namespace -- short command: `$ kubectl get configmap`


# K8s Ingress Explained

## What is Ingress?

### External Service vs. Ingress

#### External Service:
- One & simplest way to access the application from ouside the K8s cluster is to use `External Service`
- External service uses IP address & Pod's Port number to access the application. Though, it's OK for testing/development purposes it's NOT recommended in production.
####  Ingress:
- Access the application from outside the K8s cluster
- Ingress helps to access the application by using the Domain names not the IP address & Port number

  ```yaml
   Browser <--> ingress  <--> internal-service <--> pod
  ```

## Ingress YAML Configuration

E.g:
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress  # kind is Ingress
  metadata:
    name: myapp-ingress
    labels:
      name: myingress
  spec:
    rules: # routing rules
    - host: myapp.com
      http:
        paths:
        - pathType: Prefix
          path: "/"
          backend:
            service:
              name: myapp-internal-service # service name
              port: 
                number: 8080

  ```
- `Ingress.spec.rules`: Are routing rules, that forward requests to the internal service.
- From the example above; user will write: http://myapp.com/
 - Notice that we're using `http` not `https`, http is default, https has to be configured.

- `Ingress.spec.rules.http`: is not related to `http:` in the browser. It is for forwarding incoming request to the internal service
- `Ingress.spec.rules.host`;
	- should be a valid domain address
	- should map the Domain name to Node's IP address, which is the entrypoint to the K8s cluster.
		- if K8s cluster has multiple nodes, one of them should have an IP address mapped to the domain name mentioned in Ingress host 

### Ingress vs. External Service (.yaml)

|   | Ingress | External service |
|---|-----    |---- 			 |
|has nodePort/targetPort? | No | Yes|
|Default Type | LoadBalancer | Default |
|Kind | Ingress | Service|



## When do you need Ingress

## Ingress Controller

### How to configure Ingress in your K8s cluster?
- You need an implementation for Ingress
- The implementation is called `Ingress Controller`,

### What is Ingress Controller?
-  It's a tool that 
	-  evaluates all the ingress rules, and 
	-  manages all the redirections
	-  will be the entrypoint to cluster
- K8s Nginx Ingress Controller tool is recommended.
- Note that there are multiple Ingress controller third parties implementations, so choose carefully.

### One thing to consider to set up the whole K8s cluster in order to receive external requests
- Environment on which your cluster is running at.
	- e.g; If you're using Cloud service provider such as AWS, GCP, Linode,... that have their out-of-box K8s solutions, Own virtualized Load Balancer.
		- The setup would look like this: 
	```
    	Cloud Load Balacer (provided by Cloud service provider) --> Ingress Controller Pod --> ingress service --> app service --> application pod
    ```
    - Advantage of using Cloud load balancer,
    	- You don't have to implement load balacer by yourself
- In either cloud service or your bare metal service, you'll need
	- separate server (proxy server, can be hardware or software solution) that will take a role of Load Balancer
	- Public IP address and open ports, to be accessed from external
	- Entrypoint to K8s cluster

- `No server in K8s cluster is accessible from outside`

```
Browser --> Proxy server --> Ingress controller --> K8s cluster services  
```

### Ingress Controller in Minikube
- There are many ways to install Ingress controller. In this case we use Ingress controller in Minikube

1. Installation: `$ minikube addons enable ingress`
	- With just one command Ingress Controller is configured!
	- This will automatically start the K8s Nginx implementation of Ingress Controller

2. Check installed ingress controller: ` $ kubectl get pod -n kube-system`

3. Create `Ingress rule` that will be evaluated by the Ingress controller

	Preprequisite:
   - check if both service & pod belong to the same namespace because the namespace is used in Ingress rule
		` $ kubectl get all -n kubernetes-dashboard` (in this case, we use kubernetes-dashboard namespace) 
        
 
 	```yaml
 	apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: dashboard-ingress
      labels:
        name: kubernetes-dashboard # both service & pod should belong to this namespace
    spec:
      rules:
      - host: dashboard.com # you should manually configure the IP address this host name will resolve to
        http:
          paths:
          - pathType: Prefix
            path: "/"
            backend:
              service:
                name: kubernetes-dashboard # service name
                port: 
                  number: 80 # service port
 	```
 
 	- Got this error? 
 		- ` Internal error occurred: failed calling webhook "validate.nginx.ingress.kubernetes.io"`

			- Execute this command: 
 				`kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission`
           
4. Check installed ingress to the namespace: `$ kubectl get ingress -n kubernetes-dashboard`
5. Check the default setting of Ingress: `$ kubectl describe ingress [ingress-name] -n [namespace]`
	- e.g: `$ kubectl describe ingress dashboard-ingress -n kubernetes-dashboard`



#### Configure Default Backend in Ingress
- Create an internal service, then map it's name & port number to those of ingres default backend.


#### Ingress Use cases

1. Defining Multiple paths for same host
- e.g: `http://myapp.com/analytics`, `http://myapp.com/shopping`, ... 

 ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: dashboard-ingress
      labels:
        name: kubernetes-dashboard # both service & pod should belong to this namespace
    spec:
      rules:
      - host: myapp.com # you should manually configure the IP address this host name will resolve to
        http:
          paths:
          - path: "/analytics"
            backend:
              service:
                name: analytic-service # service name
                port: 
                  number: 3000 # service port
          - path: "/shopping"
            backend:
              service:
                name: shopping-service # service name
                port: 
                  number: 8080 # service port
 ```
 
 2. Multiple sub-domains or domains
 	- Define multiple hosts in Ingress 
	- Want to use `http://anaytics.myapp.com` instead of `http://myapp.com/analytics`. 
 ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: dashboard-ingress
      labels:
        name: kubernetes-dashboard # both service & pod should belong to this namespace
    spec:
      rules:
      - host: http://anaytics.myapp.com
        http:
          paths:
          - path:
            backend:
              service:
                name: analytic-service # service name
                port: 
                  number: 3000 # service port
      - host: http://shopping.myapp.com
        http:
          paths:
          - path:
            backend:
              service:
                name: shopping-service # service name
                port: 
                  number: 8080 # service port
 ```

3. Configuring TLS certificates -- https//

- It's pretty simple to configure HTTPS forwarding in Ingress.
	- Just add `tls` and and `hosts.secretName` attributes in Ingress configuration.

```yaml
 apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: tls-example-ingress
   spec:
     tls:
     - hosts: 
       - myapp.com
       secretName: myapp-secret-tls #Secret kind you have to create in a cluster that holds TLS certificates
     rules:
     - host: myapp.com
       http:
         paths:
         - path:
           backend:
             service:
               name: myapp-internal-service # service name
               port: 
                 number: 8080 # service port

```

```yaml
apiVersion: v1
kind: Secret 
metadata:
  name: myapp-secret-tls # a random name
  namespace: default
type: Opaque # default for arbitrary key-value pairs
data: # actual contents
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key 
```
Note:

  - Note that TLS certificate & key names should be `tls.crt` & `tls.key` respectively.
  - Values are file contents NOT file paths/locations
  - Secret component must be in the same namespace as the Ingress component.
 
 
 # Helm - Package Manager of K8s
 
 ## What's Helm?
 - Package manager for Kubernetes (like homebrew (mac)).
To package YAML files and distribute them in public and private repositories
 - In a real application we need multiple K8s components to work together (such as service, deployment, secret, configMap, ...). Therefore it can be difficult to manage these configuration (YAML) files manually. So, it's needed to bundle all related YAML files together to easily distribute & share with other members. A bundle of YAML files is called `Helm Chart`
 - Usually you can;
 	- Create your own Helm charts with Helm and Push them to Helm repository to make it available for others, or
 	- You can download and use existing Helm charts

- Helm charts that are commonly used such as Database Apps (e.g; MongoDB), Minitoring Apps (e.g; Promotheus), & etc. are already available in Helm repository
	- Can be obtained just by installing Helm 

## How to use Helm?
- To check if a particular Helm Chart is available;
	- Command: `$ helm search <keyword>` 
	- or Search on Helm Hub (https://artifacthub.io/)

 ## When to use Helm?
 1. #### Sharing Helm Charts:
 	- Public registries for everyone
	- Private registries for individuals or organizations who wish to share Charts 

 2. #### Templating Engine 
	- If you have multiple Microservices to be deployed in K8s cluster, and those microservices differ only on few configs like application name, version, etc. you can use Helm to declared Template YAML config that retrives shared values from another YAML file called `values.yaml`

- Template YAML Config:
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: {{ .Values.name }}
spec:
    containers:
    - name: {{ .Values.container.name }}
      image: {{ .Values.container.image }}
      port: {{ .Values.container.port }}
```
- values.yaml:
```yaml
name: my-app
container:
    name: my-app-contianer
    image: my-app-image
    port: 9001
```

- Notice the `Values` in `{{.Values...}}` refers to the yaml file name, which will be like Object name created with instance fields

- This is practical especially when you're doing CI/CD, because it will allows you to replace the values of K8s component dymamically.


3. #### Sample Applications across different environments

- If you want to deploy the same application in different enviroments (DEV, STG, PROD). you can publish your own Chart that contains shared configurations, then use it from different environments.

## Helm Chart Structure
- Directory Structure:

```yaml
mychart/  # Top level mychart forlder, which is name of chart
  Chart.yaml # file contains meta info about chart. e.g: name, version, dependencies...
  values.yaml # file contains values for  the template files
  charts/ # folder contains other charts that this chart depends on.
  templates/  # folder where actual template files are stored
  ...
```
Optionally, you can have other files like Readme or lisence files in this folder

- When you execute: `$ heml install <chart-name>`
	- The Template files will be filled with values from `values.yaml`

### Values Injection into template files
- Assume that
values.yaml containts Default values
  ```yaml
  imageName: myapp
  port: 8080
  version: 1.0.0
  ```
- The default values in values.yaml can be overriden, by doing either of this:
1. Specify the config file name to get the values from a custom config file (Recommended):
	- `helm install --values=my-values.yaml <chartname>`

	my-values.yaml to override default values
    ```yaml
    version: 2.0.0
    ```
  
  	- Only the version will be overriden (`2.0.0` will override `1.0.0`)

  	- the result of `.Values` object will be:
    ```yaml
    imageName: myapp
    port: 8080
    version: 2.0.0 # version value from custom values file (my-values.yaml)
    ```
2. Override default value using command line:
	- `helm install --set version=2.0.0`

 ## What is Tiller?
 - Was used on Helm 2.0 but is nolonger exists from the Helm 3.0
 
 
# K8s Volumes Explained

- How to `persist data` in K8s using volumes
	- Persistent Volume
	- Persistent Volume Claim
	- Storage Class 

- You have to configure the volume to persist data manually. Otherwise data will lost when you stop a Pod

## Use case

### 1. Store DB data

- You need a DB `Storage`

	- Storage requirements:
      1. You need a Storage that doesn't depend on the Pod lifecyle
      2. Storage must be available on all nodes. 
      3. Storage needs to survive even if cluster crashes

### 2. Store files that will be used by K8s components
- You can use Persistent Volume
- Persistent Volume requirements:
	- It's like any other cluster resource
	- Its created via YAML file 

	e.g: Local Persistent volume
	```yaml
    apiVersion: v1
    kind: PersistentVolume # kind is PersistentVolume
    metadata:
      name: mypv
    spec:
      capacity:
        storage: 5Gi #
      volumeMode: Filesystem
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Recycle
      storageClassName: slow
      mountOptions:
        - hard
        - nfsvers=4.1
      nfs:
        path: /dir/path/on/nfs/server  # path in physical storage
        server: nfs-server-ip-address 
    ```
    
    e.g of Google Cloud Persitent volume:
        
	```yaml
    apiVersion: v1
    kind: PersistentVolume # kind is PersistentVolume
    metadata:
      name: mypv
      labels:
      	failure-domain.beta.kubernetes.io/zone: us-central1-a__us-central1-b
    spec:
      capacity:
        storage: 400Gi #
      accessModes:
        - ReadWriteOnce
      gcePersistentDisk:
        pdName: my-data-disk
        fsType: ext4
    ```
    
    - It needs actual physical storage, like;
    	- Local disk in a cluster node, 
    	- External nfs server outside the cluster, or 
    	- Cloud storage

 	- How to make the physical storage available to the cluster?
 		- It's done by Data Persistent in K8s. K8s doesn't care about the actual storage, It uses `PersistentVolume` as an interface to interact with actual/physical storage
 		- You have to specify the `type of storage` you need.
 		- You need to create and manage it by yourself
	
    Note: 
    > - Think of storage as an external plugin to your cluster 
    > - Persistent Volumes are NOT namespaced, which means they're accessible to the whole cluster
    > 
### Local vs. Remote Volume Types
- Each volume type has it's own use case!
- Local Volume types violates requirement for data persistence (DB)
	- They're tied to 1 specific node
	- They don't survive when a cluster crashes 
  Therefore, for that reasons you should almost always never use Local Volume for DB persistence, use remote storage instead
  
### K8s Administrator and K8s User
- Who creates the Persistent Volumes and when?
	- Persistent Volumes (PV) are resources that need to be there BEFORE in a cluster so that a Pod that depends on it is created

- Two roles in K8s cluster:
  1. Administrator (e.g: DevOps or System admin)
      - Sets up and maintains the cluster
      - Create Storage resources maybe on Cloud, then Create Persistent Volume storage to link with storage resource 	 
  2. User (e.g: Developer)
      - deploys applications in cluster
      - Explicitly configure the application to use the given Persistent Volumes (i.e; Application has to claim the Persistent Volume) by using a K8s component called `Persistent Volume Claim (PVC)` which is created with YAML configuration
Example of Persistent Volume Claim:
    
    
      ```yaml
      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-name
      spec:
        resources:
          requests:
            storage: 10Gi
        volumeMode: Filesystem
        accessModes:
          - ReadWriteOnce
      ```
      - Then, Use the claimed PVC in Pods configuration

      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: myapp
        labels:
          name: myapp
      spec:
        containers:
        - name: myfrontend
          image: nginx
          volumeMounts:
            - mountPath: "/var/www/html"
              name: mypod
        volumes:
          - name: mypod
            persistentVolumeClaim:
              claimName: pvc-name # name of PVC component
      ```
      
      ### Levels of Volume abstractions
      
      ```
      1. Pod requests the volume through the PV claim, 
      2. Claim tries to find volume in a cluster that meets the criteria (e.g; size), 
      3. Volume has the actual storage backend on the disk

		When a volume is found:
		- It will be mounted into the Pod (i.e; Pod.volumes.persistentVolumeClaim.claimName), then
		- Volume is mounted into the Container (i.e; Pod.VolumeMounts),
		 so that application container can read/write directly to the Storage
      ```
      Note: 
      > The Claim (PVC) & the Pod that uses it, must be in the same Namespace
      > 
	### Why so many abstractions to use volume?
    As a developer:
    - You want to deploy an application on the cluster without caring where the data is being stored, can be either Cloud-storage or Local storage
    - You don't want to set up the actual storages because it maybe bigger to store on your machine. instead, you just have to claim the storage, then the cluster will handle the rest

 #### ConfigMap and Secret
 - The volumes for these two components are different from other components' volumes.
 	- 	Both of them are local volumes
 	- 	Are not created via PV and PVC, they're created and managed by Kubernetes

- How to mount ConfigMap or Secret to your container:
	- Create a ConfigMap and/or Secret component
	- Mount that into your pod/container. i.e; `Pod.volumes.configMap.name`

#### Summary
- Volume is a directory with some data
- These volumes are accessible in containers in a pod
- The volume directory is made available, backed by which storage medium:
	- defined by specific volume types
	- Container accesses the mounted volume via: `Pod.containers.volumeMounts.mountPath` 

- It's possible to configure multiple volume types in a Pod; just by listing them.


#### Storage Class

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: storage-class-name
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
  iopsPerGB: "10"
  fsType: ext4
```

- StorageBackend is defined in the Storage Class component via `provisioner` attribute
- Each storage backend has own provisioner
	- Internal provisioner: `kubernetes.io`
	- There are external provisioners
- Storage Class is another abstraction level
	- abstracts underlying storage provider, and
	- parameters for that storage 

##### How Storage Class is used
- Storage class is requested by PersistentVolumeClaim (PVC), `PersistentVolumeClaim.spec.storageClassName: [storage-class-name]`

Now, when;
- a Pod claims storage PVC
- PVC requests storage from Storage Class (SC)
- SC creates PV that meets the needs of the Claim


# K8s StatefulSet explained



## Others
- Generate base64 in terminal: `echo -n 'text' | base64` 
	- e.g: `$ echo -n 'mongo123' | base64` //output bW9uZ28xMjM=

