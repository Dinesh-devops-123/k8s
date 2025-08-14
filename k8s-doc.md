What Kubernetes (k8s) Offers

📦 1. Cluster Management
Group of nodes (VMs or bare metal)
Master (control plane) + Worker nodes
Handles resource allocation, health checks, deployments, upgrades

🩺 2. Auto-Healing
Liveness and Readiness Probes check if apps are healthy.

If a container:
Crashes → Kubernetes restarts it.
Becomes unresponsive → Kubernetes can replace it.
Works through ReplicaSets and Deployments.

livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5

📈 3. Auto-Scaling
Horizontal Pod Autoscaler (HPA):
Automatically adds/removes pods based on CPU/RAM usage or custom metrics.
Vertical Pod Autoscaler (VPA):
Adjusts resource limits/requests.
Cluster Autoscaler:
Adds/removes worker nodes based on overall load (works with cloud providers like AWS/GCP/Azure).

🧰 4. Enterprise-Level Support
Vendor Support: Red Hat OpenShift, VMware Tanzu, Rancher, Amazon EKS, Azure AKS, GCP GKE.
RBAC & Security Policies
Namespaces for multi-tenancy
Network policies, secrets, service accounts

🌐 5. Load Balancing
Service object provides stable access to pods.

Types:
ClusterIP – internal only
NodePort – accessible via IP:port
LoadBalancer – integrates with cloud provider LB
Ingress Controller – for advanced HTTP routing, SSL termination, URL path routing, etc.

apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 8080

🌐 6. Networking
Pod-to-Pod communication via CNI (Container Network Interface) plugins:
Flannel, Calico, Cilium, Weave, etc.
DNS support (via CoreDNS)
Ingress: Route external traffic to internal services.
Network Policies: Control traffic between pods for security.


---------------------------------------------------------------
##Imperative vs Declerative 

#Imperative - Definition: You tell the system exactly what to do and how to do it.
Action-based — run direct commands to create, modify, or delete resources.
State is applied immediately but not saved for future.
Good for quick fixes or testing.

eg:
# Create a pod imperatively
kubectl run mypod --image=nginx

# Expose a service imperatively
kubectl expose pod mypod --port=80 --type=NodePort

#Declarative - Definition: You define what you want (the desired state) in a file (usually YAML/JSON), and Kubernetes figures out how to get there.
State-based — you focus on the end state, not the exact steps.
Files can be version-controlled (GitOps friendly).
Good for repeatable deployments and automation.

# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: nginx
      image: nginx
##kubectl apply -f pod.yaml
