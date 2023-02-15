**Kubernetes components**

(following is just a subset of all K8s components, but the most common ones):

* Node/worker node
    * simple server (physical or virtual machine)
* pod
    * Smallest unit of K8
    * An abstraction over container
    * Usually 1 application per pod
    * Own IP address from Node’s range (e.g. Node 1 with range 10.2.1.x), but new IP address on re-creation (pods are ephemeral)

* service
    * Pods communicate via their service IP address
    * Permanent IP address
    * Own lifecycle (if pod dies, service lives)
    * Is also a load balancer (see Replicas to understand better how it distributes the load)
    * If communication is only within cluster, you need an internal service.
    * But if we want our application to be accessible via a browser, we need an external service. Example would be that external request would come from http://<node-ip-address>:<node-port>. But this is good only for testing purposes. Often you want a proper url with a secure https protocol and a domain name, which for that there is a kubernetes component called ingress (see separate section dedicated to Service)

* ConfigMap
    * Stores external configuration of your application (e.g. URL of dbs or other services used)
    * We connect the ConfigMap to pod, and no re-building of pod is needed anymore
    * (see separate section dedicated to Volume for more info)

* Secret:
    * Similar to ConfigMap but is used to store secret data in base64 encoded format
    * We connect Secret to pod same as in ConfigMap, and pod knows where to read them from
    * You can use Secret or ConfigMap e.g. via environment vars
    * (see separate section dedicated to Volume for more info)

* Volume
    * If data is inside pod, if pod dies data dies with it
    * You can attach a physical storage on a hard drive to the pod. It can be in the same server node that pod is running, or in a remote storage outside of K8s cluster (e.g. cloud storage)
    * Noting that K8s explicitly does not manage data persistence, therefor imagine storage as an external hard drive plugged into K8s cluster (see separate section dedicated to Volume)

* ServiceAccount
    * is an identity that an application can use to run requests on your behalf (to api server, you will later see its definition), i.e. ServiceAcount is an identity of a process, while User Account is a user (human) identifier.
    * you define IAM (identity and access management) policies for the ServiceAcount
    * When you create a pod, if you do not specify a ServiceAccount, it is automatically assigned the default service account in the same namespace

* Statefulset
    * For stateful applications, Statefulset is used instead of a deployment.
    * Make sure reads and writes are in sync at each point in time. This is done via having a master which can read and write, and the rest of replica pods will be just slave that update their states from master.
    * Take care of scaling up or scaling down the databases
    * But it is more difficult to deploy applications using Statefulset. That is why typically databases are managed outside of K8s and are just connected to the usual stateless deployments.

---
**Deployment**

is an abstraction over pod or is the blueprint for the pod/s. In practice we work with deployments and not with pods: we don’t create pod/s but the deployment, and deployment handles the rest. In deployment, we can specify how many replica (=replicates or clones) of the same pod we want. Replicas are connected to the same service component (share same service IP address) and enable no downtime when one pod dies or even the whole node crashes, i.e. another replica node with alive pod will take the load. And here we see how replicas act as a load balancer. With replica, we can scale up or scale down the system: if we have more loads we scale up number of replica. Noting that we cannot replicate databases if they live in pods, because the state of database matters (=there needs to be one source of truth at all times). For this another component of K8s, StatefulSet, is used.

---
**Kubernetes architecture**

**Worker node**

* Worker nodes do the actual work
* each node has multiple pods on it
* 3 processes must be installed on each node. These processes are used to schedule and manage pods:
    1. container runtime (e.g. docker)
    2. kubelet: is the process that actually schedules and runs the pods or container within a node. It is a process of K8s itself. It interacts with both the container runtime and the node (i.e. the machine) itself. Kubelet starts the pod and assigns resources from the node to the containers (CPU, storage resources, etc)
    3. kube proxy: is responsible for forwarding requests from services to pod. It has intelligent forwarding logic inside that makes sure communication is performant with low overhead.

**Master node**

Controls the cluster state and the worker nodes. 4 processes run on every master node:

1. Api server: when we want to deploy an application we actually interact with the Api Sever via a client (kubectl). It is like a cluster gateway which gets initial requests of any updates to the cluster or queries from the cluster, and it also acts as a gatekeeper for authentication. So, if requests are validated, Api server then forwards the requests to other K8s process to schedule etc.
2. Scheduler: if you send a request to schedule a new pod, Api server will then send your request to the Scheduler in order to start the application pod on one of the worker nodes. Scheduler has intelligence logic on where to put the pod, it checks how much CPU and resources your application requires and schedules the new pod on that worker node. Remember that at the end the worker node’s kubelet will handle the scheduling.
3. Controller manager: if a pod dies on any node. Controller manager detects cluster state changes and tries to recover by sending a request to Scheduler to reschedule those dead pods.
4. etcd: is the cluster brain. It is a key value store of all cluster changes. It is the brain because all the processes in Scheduler and Controller manager work because of its data, like what resources are available, which pod died, or Api server knowing the state of the deployment.

In practice there are more than one master nodes where Api servers are load balanced and etcd storage is distributed among all master nodes.

---
**Minikube**

If you want to test your deployment on your local machine, you can use minikube. It is a one node cluster where the master and worker processes both run on one node, and this node has docker runtime processes pre-installed. It creates a virtual box on your system and runs the node there.

---
**Kubectl**

A command line tool for K8s cluster. It acts as a client (the most powerful client) to talk to Api server which is the main entry point for K8s server. For installation on you system, google!

---
**K8s YAML configuration**

Every deployment configuration has 3 parts:
1. metadata of the component you are creating like name.
2. specifications (spec) contains the configuration you want to apply to that component you are creating the deployment for.
    * templates: 
        inside spec, you have templates which has its own:
            * metadata
            * spec (blueprint of pods)
        so, it is a configuration file inside a configuration file. Reason being, the template configuration applies to a pod, i.e. pod’s configuration is inside the deployment configuration.

3. status, will be automatically generated and added by K8s. K8s will always check what is the desired state and what is the actual state, and if they do not match, it knows that there is sth to be fixed there, and this is the basis of K8s self-healing. e.g. if in spec you specified you need 2 replicas, and at some point status says you have 1, and it knows that another replica needs to be created asap. This status comes from etcd explained earlier.

The way deployments are connected is through labels and selectors.
    * Metadata holds labels. In metadata you give the deployment a label (any key-value pair would work). With adding this label for pods of that deployment (via template blueprint), you tell K8s that which pods in this deployment belong to this label.
    * spec holds selectors. In deployment’s selector we still need to tell K8s to match all the labels having same key-value to finalise connecting pods of the deployment. This is done through matchLabels. Now, to connect a Service deployment to this deployment, in Service deployment, you need to add the same label under Service’s spec->selector. This is how connecting different deployment kinds work.

Another thing that needs to be configured is port. Service has its port configuration, that tells at what port service is accessible. So, if other services send a request to a service, they know which port is listening. But the service should know which pod it sends the request to, and also at which port is that pod listening (targetPort). Therefore, the service’s targetPort should match the pod’s container port.. Btw, you can have multiple deployments in same yaml, just separate them with `---`

---
**Kubernetes Namespaces**

You can organise resources in a K8s cluster with namespaces. It is like having virtual clusters inside a cluster. K8s give 4 namespaces out of the box, check them with: `kubectl get namespaces`. These 4 processes are:
1. kube-system: This is for system processes deployments so DO NOT create your deployments there. They are for master processes or kubectl processes etc
2. kube-public: It has publicly accessible data which does not require even any authentication
3. kube-node-lease: gives information on heartbeats of nodes i.e. availability of the nodes
4. default: This is the namespace that you will create your deployments if you haven’t setup any other namespaces.

Benefits of using namespaces:
* You can quickly get big picture of your cluster and its required resources.
* conflicts: many teams, same application. Each team can have their own namespaces with independent deployments.
* Resource sharing between different environments (dev/staging/prod), this way you can re-use those common components in all three environments.
* For blue/green deployments where you may need to have two prod environments with different version and they have common shared resources.
* Access and resource limits on namespaces, so that each team can work on their own namespaces, and even limit the resources (CPU, etc) per team.

Note that you can’t access most of resources from another namespace, e.g. ConfigMap and Secrets, but services can be shared between namespaces. Some components in a cluster can’t be created within a namespace and they live globally in a cluster, examples are persistent volumes and nodes. You can list these resources with: `kubectl api-resources --namespace=false`. To create a deployment in specific namespace, just provide the namespace attribute in the metadata of deployment yaml.

---
**Services**

There are 4 types of services:

1. ClusterIP (default): handles internal communication on stateless nodes
2. Headless: is used when we have stateful applications where the client needs to communicate with 1 specific pod directly (not any random pod from any of the replicas). In headless service ClusterIP attribute must be set to None in config yaml. This denotes that Service is headless. In case of stateful apps like databases, we have the normal service that does the load balancing etc, but for writing to database for instance, the request goes through the headless service and that redirects the request to master node since only master node is allowed to write.
3. NodePort: It is an extension of ClusterIP service type while the ClusterIP service is only accessible within cluster, the NodePort service enables external traffic access to fixed port on each worker node. It is accessible for external requests via a static port on each node in the cluster. NodePort has a predefined range between 30000-32767. This service is not secure as client has direct access to the cluster.
4. LoadBalancer: is a better option compared to NodePort and is again another service type that can be used when external requests need to be handled (described earlier). It is an extension of NodePort Service type.

---
**Ingress vs. external service**

For external request to be able to reach your application, ingress should be used, while for testing purposes a simple external service is an option.

YAML configuration:
* External service: of kind service which is of type LoadBalancer, which means we are assigning an external IP address to the service. And there a NodePort which is the port the user should use along the external IP address to communicate with the service (`http://<node-ip-address>:<node-port>`)

* Ingress: of kind Ingress. And in specs you have Routing rules, and it says the domain address must forward/redirect request to the internal service. You should yourself map the domain name to Node’s IP address which is the entry point for your K8s cluster. The path is the url path, anything after the url name (.e.g. `myapp.com/analytics`). Yet, we haven’t configured anything related to secure http (the http in yaml does not correspond to http in url). Note that internal service does not have a type defined which means it is of default type ClusterIP and not of type LoadBalancer. To configure Ingress, just creating the Ingress component is not enough. You need an implementation for Ingres which is Ingress Controller. So, the first step is to install an Ingress Controller which is another pod/set of pods that run on your node in K8s cluster that does the evaluation and processing of the Ingress rules defined in Ingress yaml and manage all the redirections, so it will be the entry point to the cluster. To install it, you just need to decide which one of the many third-party implementations you want to choose from. There is one from K8s called Nginx Ingress Controller. Check out Kubernetes’s documentation on Ingress Controller to see the whole list. If you are using a Cloud service provider like AWS for running your K8s cluster that have out-of-the-box K8s solutions, you will have a Cloud Load Balancer that is specifically implemented by that Cloud provider and the requests first hit the LoadBalancer. Otherwise for a Bare Metal environment (a server dedicated entirely to one tenant/organisation as opposed to Cloud multi-tenant environment), you need to have an entry point and LoadBalancer yourself e.g. a proxy server. With following command, you will see the pod running the ingress controller: `kubectl get pod -n kube-system`

NB. It is possible to configure multiple paths for the same host, i.e. one domain but multiple services (`http://my-app.com/<some-path>`). Also, other way around where you have multiple domains (`http://<sub-domain>.my-app.com/`). It is also easy to configure TLS Certificate (https). Just under spec and above rule section add the tls attribute for the host and the secretName which you put to it in your cluster. Noting that the Secret component needs to be in the same namespace as the Ingress component.

---
**Volumes**

Persistent volume (pv) is a cluster resource, created via YAML file. It needs an actual physical storage (K8s local disk, nfs server, cloud-storage) which you specify in YAML which backend you want to use. Persistent volumes are not namespaced so they are accessible to the whole cluster. PV are resources that need to be there BEFORE the pod that depends on it is created. PV is set-up by the Admins, and the application has to claim the Persistent Volume via PersistentVolumeClaim (PVC) component. Aside from creating this PVC, we have to use that PVC in Pod’s configuration. Now, the pod and all containers within the pod can access that volumes. Claims must be in the same namespace that the pod using the claim is. This way the volume is mounted in the pod, and you can then mount it to any/all containers within the pod. ConfigMap and Secrets are also part of volumes. They are local volumes that not created by PV and PVC but managed by K8s itself. If a pod needs any of these files, the pod has to mount that file into pod/container. In cases where Admins need to create many PV, managing them can be very tedious. To make this process more efficient, there is another K8s component called StorageClass (SC) which provisions PVs dynamically when PVC claims are made. SC is created via StorageClass Yaml config. The StorageBackend for provisioning is defined in the SC component config via “provisioner“ attribute. To use the SC, the PVC claim needs to request the StorageClass via StorageClassName attribute that references the SC to used.

---
**References**
1. [Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://youtu.be/X48VuDVv0do)