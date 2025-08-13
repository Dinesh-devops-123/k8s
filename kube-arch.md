#Control plane - control plane is responsible for manging the state of cluster.It runs and manage multiple nodes and span across multiple servers and data center.

#Components:
1.API Server
2.scheduler
3.etcd
4.controller manager
5.cloud controller manager


1.API Server – It is the central component of the control plane. Any incoming request from a client first reaches the API Server, which then interacts with other components on the client's behalf. It serves as the primary interface between the control plane and the rest of the cluster.

2.Scheduler – It receives workloads from the API Server and determines the most suitable node for each pod. It is responsible for scheduling pods onto worker nodes in the cluster, using information about the resources required by the pods and the available resources on the worker nodes to make placement decisions.

3.etcd - It is a distributed key-value datastore.Think of it as the brain of the Kubernetes cluster, storing all the critical data that defines the cluster’s desired and current state.only API server will interact to etcd database it has the authority to change the data

🔑 What Does etcd Store?
It holds configuration data and metadata for the entire cluster, including:
- ✅ Cluster state (nodes, pods, services, etc.)
- 📦 Resource definitions (Deployments, ReplicaSets, ConfigMaps, etc.)
- 🔐 Secrets and access control policies
- 📊 Status of workloads and components
- 📅 Scheduling information

Here’s how it works:

#Master (Control Plane) events stored in etcd
-Creation, update, or deletion of Kubernetes objects (pods, services, deployments, etc.).
-Changes in cluster state (node joins, node leaves, status changes).
-Configuration updates (ConfigMaps, Secrets, RBAC rules).
-Scheduling decisions made by the scheduler.
-Rolling update or rollback operations.

#Worker node–related events stored in etcd
-Node registration and heartbeats (status updates from kubelet).
-Pod status changes (Running → Failed → Restarted).
-Resource usage updates (CPU/memory requests/limits in pod specs).
-Node readiness/unreadiness state.
-Deletion of pods or nodes from the cluster.

4.Controller Manager – It is a combination of different controllers such as the Node Controller, Namespace Controller, Deployment Controller, and Replication Controller. It ensures that all controllers are running properly, everything is monitored, and the cluster components remain healthy. For example, if a pod goes down, it monitors the pod and restarts it as needed. It is responsible for running controllers that manage the state of the cluster, ensuring that the desired number of pod replicas are running. The Deployment Controller also manages rolling updates and rollbacks of deployments.

✅ Key Responsibilities:
- Runs multiple controllers such as:
- Node Controller – Monitors node health and manages node lifecycle.
- Namespace Controller – Handles creation and deletion of namespaces.
- Deployment Controller – Manages deployments, including rolling updates and rollbacks.
- Replication Controller – Ensures the specified number of pod replicas are running.

5.Cloud Controller Manager – It is responsible for integrating K8s with cloud provider APIs, enabling the management of cloud-specific resources such as load balancers, storage, and networking. The cloud controller manager allows k8s to interact with the underlying cloud infrastructure, automating tasks like provisioning persistent volumes, configuring external load balancers, and handling node lifecycle events in cloud environments.

#Worker node

1.Kubelet
2.Container runtime
3.Kube proxy

1.Kubelet – It is a daemon that runs on each worker node, responsible for communicating with the control plane and interacting with the API Server. It acts as a node-level agent, enabling communication between the worker node and the control plane.

2.Container Runtime – Responsible for pulling container images from a registry, starting and stopping containers, and managing their resources.

3.Kube-proxy – It enables networking within the nodes and allows pods to communicate with each other. It creates IP table rules that enable pod-to-pod communication and allow communication between pods and services. It routes traffic to the correct pod, provides load balancing for the pods, and ensures that traffic is distributed evenly across them.

