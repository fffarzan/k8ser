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

# Minikube and Kubectl

# k8s CLI

# YAML config file

# k8s namepaces

# Ingress

# Helm

# Volumes

# k8s statefulSet

# Services

# Refs

- [k8s nana](https://www.youtube.com/watch?v=X48VuDVv0do)

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
