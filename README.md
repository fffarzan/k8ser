# What is it?

Container orchesteration framework. Manages containerized apps.

- High availability
- Scalability
- Disaster recovery

# Arch

Each k8s cluster has multiple Nodes on it. Communication between Nodes are possible with Services. Service with its load balancer routes the traffic between Pods.

Worker machine in k8s cluster:

- Each node has multiple Pods on it.
- 3 processes must be installed on every node.
  - container runtime (independent).
  - kubelet: Schedules the runtime. It interacts with the Container and Node. It starts the Pod with a Container inside.
  - Kube proxy: Forwards the requests to Pods.

All the managing processes are done by master nodes. 4 processes run on it:

- Api Server: We have just 1 entrypoint into the cluster. It's load balanced.
  - Cluster gateway
  - Acts as a gatekeeper for authentication.
- Scheduler: It is enough inteligent to just decides on which Node new Pod should be scheduled based on resource management.
- Controller Manager: Detects state changing of clusters like crashing. It sends a notif to Scheduler to reschedule the Pod.
- etcd: Key-value store of the cluster states. Cluster brain. Logs of all Pods status and other thing is stored there. It's distributed through all master nodes.

# Main comps

Pod: Smallest unit of k8s. An abstraction over a container. Usually 1 app per pod. Each Pod gets its own IP address (new IP address on recreation).

Service: permanent IP address. Also a load balancer. Lifecycle of Pods and Services are not connected.

External Server (public) and Internal Service (private).

Ingress: The request to goes to Ingress and then to (private) Services.

ConfigMap: External configuration of the app.

Secret: Stores secret data, credentails, ... . Based64 encoded.

Volume: Attaches a physical storage (on local or remote storage) to the Pod.

We replicate everything. The nodes that are replicated are Replica. These Replicas are connected to the same Service.

Deployment: Blueprint for the app Pods. We create Deployments not Pods. They are abstraction of Pods. Deployment is for stateLess apps.

If one of our Replicas of the app Pod dies, Service will forward the request to another one.

StatefulSet: DB can't be replicated via Deployment. All the DBs access to same shared data storage. It's hard to manage them. DBs are often hosted outside of k8s dev.

# Minikube and Kubectl

minikube: One-Node cluster that runs master and worker processes in one Node (machine) and this Node has Docker per-installed. It runs through a Virtual Box. It is used for testing purposes.

kubectl: Command-line tool. the way to interact with clusters of k8s. We should work with ApiServer of Master pocess. We can do it in three ways: UI, API and CLI (kubectl).

Kubectl is used for interacting with any cluster of k8s including Minucube, Cloud, ... .

Installation:

```bash
brew update
brew install hyperkit
brew install minikube # minikube has kubectl as dependency

kubectl # check working
minikube # check working
```

Creating a minikube cluster:

```bash
minikube start --vm-driver=hyperkit
kubectl get nodes # get status of nodes
minikube status
kubectl version
```

# k8s CLI

Note: Pod is the smallest unit on k8s but we are creating Deployment (abstraction over Pods).

```bash
kubectl get all
kubectl get nodes # get status of nodes
kubectl get pod # get status of pods
kubectl get services
kubectl get deployment
kubectl get replicaset # replicaset: managing the replicas of a Pod

kubectl create deployment NAME --image=image [--dry-run] # image goes and download latest doecker image of the tool like nginx. This command creates bluprint for creating Pods.
```

layers of abstraction:

- 4. Deployment
- 3. ReplicaSet
- 2. Pod
- 1. Container

Everything below the Deployment should be managed by K8s.

```bash
kubectl edit deployment [name]

kubectl logs [podname]

kubectl exec -it [podname] -- bin/bash # open up an interactive terminal of the pod

kubectl delete deployment [name]
kubectl delete -f [filename] # delete with config file

kubectl apply -f [filename] # execute and apply the config file
```

# YAML config file

3 parts:

- metadata
- specification
- status: k8s automatically compares the desired state and the actual state and updates continuasly. k8s gets the data for comparison from `etcd`.

```bash
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
kubectl describe service nginx-service
kubectl get pod -o wide
kubectl get deployment nginx-deployment -o yaml > nginx-depl-result.yaml # get updated info of deployment which is inside of `etcd`.
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

# k8s namepaces

It's a virtual cluster inside a k8s cluster. We can organize resources in namespaces.

k8s has 4 namespaces by default:

- kube-system: Don't touch!
  - system processes
  - master and kubectl processes
- kube-public: publicely accessible data
  - a configmap (cluster info)
- kube-node-lease
  - heartbeats of nodes
  - each node has associated lease object in namespace
  - determines availability of node
- default: resourses we created

Note: k8s-dashboard only comes with minikube and it's not a default namespace.

```bash
kubectl get namespace

kubectl create namespace [name]
```

### usage

- We shouldn't use it for smaller projects.
- several teams on just one cluster app. (avoiding conflicts)
- If you wanna have stage or dev environments, that a good idea to have different namespaces (we re-use componentes in different envs).
- Blue/Green deployment (you have two versions of production inside a cluster).
- access and resource-limit.

You can't access most resources from another namespace.

Each namespace must have its own configMap and secret.

Services can be shared across namespaces.

Some components globally live in the cluster and can't be inside of any namespace. Like Volume and Node. we can check them in this way:

```bash
kubectl api-resources --namespaced=false
```

By default components create in default namespace. We can create components inside of custom NS by this:

```bash
kubectl apply -f mysql-configmap.yaml --namespace=fff-ns
```

We can put it inside config file:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-configmap
  namespace: fff-ns
data:
  database_url: mysql-service.database
```

### change active ns

If we want to see inside of ns, we should add `-n [my-namespace-name]` flag at the end of all commands. To prevent this we can change showing ns from default to our custom ns:

```bash
brew install kubectx

kubens
kubens [name] # to switch ns
```

# Ingress

# Helm

# Volumes

# k8s statefulSet

# Services

# Refs

- [k8s nana](https://www.youtube.com/watch?v=X48VuDVv0do)
