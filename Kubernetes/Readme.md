#  📘 Kubernetes Notes #

## Introduction to Kubernetes
Kubernetes (often abbreviated as K8s) is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications.
Initially developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF), Kubernetes has become the industry standard for running containerized workloads at scale.

## Key Features:

- **Automated Scheduling:** Places containers on optimal nodes.
- **Self-Healing:** Restarts failed containers and replaces them automatically.
- **Horizontal Scaling:** Increases or decreases the number of replicas based on load.
- **Service Discovery & Load Balancing:** Exposes containers to the network and balances traffic.
- **Storage Orchestration:** Automatically mounts storage systems.

## Example:
Imagine you have a web application running in 10 containers. Instead of manually starting/stopping them, Kubernetes will:
- Deploy them evenly across multiple servers.
- Replace any crashed containers automatically.
- Route traffic to available containers without downtime.

## Why We Need an Orchestration Tool
When working with a small number of containers, manual management may seem simple. But in production environments with hundreds or thousands of containers, manual management becomes inefficient and error-prone.
Challenges Without Orchestration:

- **Scaling**: Manually starting/stopping containers based on demand.
- **Load Balancing**: Distributing traffic manually.
- **High Availability:** Restarting failed containers quickly.
- **Networking:** Connecting containers securely and consistently.
- **Updates:** Deploying new versions without downtime.

Example Problem:
If you run 100 containers across 5 servers and one server fails, you must:
- Detect the failure.
- Start replacement containers on another server.
- Update your load balancer.
An orchestration tool like Kubernetes does all of this automatically.

## Why Kubernetes?
Kubernetes has emerged as the preferred orchestration tool for several reasons:
1. **Open Source**: Vendor-neutral and community-driven.
2. **Scalability**: Designed to handle large-scale workloads.
3. **Portability**: Works across on-premises, cloud, and hybrid environments.
4. **Extensibility**: Highly customizable through APIs and plugins.
5. **Resilience**: Automatic healing of failed containers and rescheduling.
6. **Comprehensive Ecosystem**: Supported by a wide range of tools and platforms.

## Architecture of Kubernetes
Kubernetes follows a master-worker architecture.
### Control Plane (Master Components)
The control plane manages the cluster’s state.
- #### API Server (kube-apiserver):
Entry point for all cluster commands and communications.
Example: kubectl get pods talks to the API Server.
- #### Controller Manager (kube-controller-manager):
Watches the cluster’s state and ensures it matches the desired state.
Example: If a pod crashes, it tells the scheduler to start a new one.
- #### Scheduler (kube-scheduler):
Decides which worker node will run a new pod based on resources, policies, etc.
- #### etcd:
Key-value store holding the cluster's state and configuration.

###  Worker Node Components
- #### Kubelet:
Runs on every worker node; ensures pods are running correctly.
- #### Kube-Proxy:
Handles network rules and routing for pods.
- #### Container Runtime:
Runs containers (e.g., Docker, containerd, CRI-O).

## **Kubernetes Architecture in a Nutshell**

- **Master Node**: The "manager" that makes decisions and keeps the cluster running.
- **Worker Node**: The "worker" that runs your applications.
- **Pods**: The "workers" of your apps, running one or more containers.
- **Additional Components**: Helpers like ConfigMaps, Secrets, Ingress, and Namespaces that make managing apps easier.

---

## **Example Workflow**

1. You tell Kubernetes to run an app using `kubectl`.
2. The **API Server** receives your request and forwards it to the **Scheduler**.
3. The **Scheduler** assigns the app to a **Worker Node**.
4. The **Kubelet** on the Worker Node starts the app in a **Pod**.
5. The **Controller Manager** ensures the app stays running, and **etcd** stores all the details.
6. If the app needs to talk to other apps, **Kube-proxy** handles the networking.

---


## Lifecycle of a Pod
A Pod is the smallest deployable unit in Kubernetes.
Pod Lifecycle Phases:
- **Pending** – Pod is accepted but not yet scheduled.
- **Running** – Containers are running on a node.
- **Succeeded** – Containers completed successfully.
- **Failed** – Containers terminated with an error.
- **Unknown** – Pod state can’t be determined.


## Cluster Creation Methods
Kubernetes clusters can be created in different ways depending on the environment:

| Tool/Service | Description                                              | Use Case                        |
| ------------ | -------------------------------------------------------- | ------------------------------- |
| **Minikube** | Runs Kubernetes locally inside a VM or Docker container. | Learning and local development. |
| **Kind**     | Runs Kubernetes inside Docker containers.                | CI/CD testing and quick setups. |
| **kubeadm**  | Bootstraps a Kubernetes cluster on your own machines.    | On-premises or cloud servers.   |
| **EKS**      | Managed Kubernetes by AWS.                               | Production workloads on AWS.    |
| **GKE**      | Managed Kubernetes by Google Cloud.                      | Production workloads on GCP.    |
| **AKS**      | Managed Kubernetes by Azure.                             | Production workloads on Azure.  |


## Introduction to Pods and Services

### Pods
A Pod is the smallest deployable unit in Kubernetes, encapsulating one or more containers with shared resources like storage and network.

- **Lifecycle**:
  - Pending → Running → Succeeded/Failed → Terminated

- **Use Cases**:
  - Running a single application container.
  - Running multiple containers that share resources and are tightly coupled (e.g., sidecar patterns).

### Services
Services provide stable networking and expose Pods to other applications or external traffic.

- **Types of Services**:
  1. **ClusterIP**: Exposes the service within the cluster.
  2. **NodePort**: Exposes the service on each node’s IP at a static port.
  3. **LoadBalancer**: Exposes the service to the internet using a cloud provider’s load balancer.

| Service Type     | Accessible From         | Typical Use Case                                | Example Access URL            |
| ---------------- | ----------------------- | ----------------------------------------------- | ----------------------------- |
| **ClusterIP**    | Inside cluster only     | Microservice-to-microservice communication      | `http://backend-service:8080` |
| **NodePort**     | External (via node IP)  | Testing or small setups without a load balancer | `http://<node-ip>:30080`      |
| **LoadBalancer** | Internet (via cloud LB) | Production applications accessible publicly     | `http://<public-ip>`          |

---

## Main Container and Sidecar Containers

### Main Container
The primary container that serves the main purpose of the application. Examples include application servers or web servers.

### Sidecar Container
An auxiliary container that provides supporting functionalities, such as logging, monitoring, or proxying.


## Run First Pod Using kubectl

1. **Create a Pod**:
   ```bash
   kubectl run nginx-pod --image=nginx --restart=Never
   ```
   - `--image`: Specifies the container image.
   - `--restart=Never`: Ensures the creation of a standalone Pod.

2. **Verify Pod**:
   ```bash
   kubectl get pods
   ```

3. **View Pod Details**:
   ```bash
   kubectl describe pod nginx-pod
   ```

---

## Expose Pod Using kubectl expose

1. **Expose Pod**:
   ```bash
   kubectl expose pod nginx-pod --type=NodePort --port=80
   ```
   - `--type=NodePort`: Exposes the Pod on a static port.
   - `--port`: Specifies the port the Pod listens on.

2. **Get Service Details**:
   ```bash
   kubectl get svc
   ```

3. **Access the Pod**:
   - Use the `<NodeIP>:<NodePort>` to access the exposed Pod.

---

## In-Depth: kubectl Usage

### Common Commands
- **View Resources**:
  ```bash
  kubectl get pods
  kubectl get svc
  kubectl get nodes
  ```

- **Delete Resources**:
  ```bash
  kubectl delete pod <pod-name>
  ```

- **Debugging**:
  ```bash
  kubectl logs <pod-name>
  kubectl exec -it <pod-name> -- /bin/bash
  ```

- Viewing Pod Events:
  ```bash
  kubectl describe pod <pod-name>
  ```



---






