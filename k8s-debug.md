# Kubernetes Troubleshooting Cheat Sheet
## 1. Confirm Cluster & Namespace

# Check current context
kubectl config current-context

# List contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <name>

# List namespaces
kubectl get ns

# List pods in a namespace
kubectl get pods -n <namespace>


##2.Big picture:nodes,pods & events.
# List nodes
kubectl get nodes

# List all pods
kubectl get pods -A

# List all deployments
kubectl get deployments -A

# View recent events (sorted by time)
kubectl get events --sort-by=.metadata.creationTimestamp -A


##3.Inspect the failing pod.
# Describe the pod
kubectl describe pod <pod-name> -n <namespace>

# View logs
kubectl logs <pod> -n <namespace>

# View logs for a specific container
kubectl logs <pod> -c <container> -n <namespace>

# Open a shell inside the pod
kubectl exec -it <pod> -n <namespace> -- /bin/sh


##4.Probes & health checks.
# Check readiness/liveness probe info in describe
kubectl describe pod <pod> -n <namespace>

# Test probe endpoint from inside the pod
kubectl exec -it <pod> -n <namespace> -- curl -sv localhost:<port>/health

# Adjust probe timeouts if needed (in Deployment YAML)


##5.Rollouts,history & recover.
# Check rollout status
kubectl rollout status deployment/<name> -n <namespace>

# View rollout history
kubectl rollout history deployment/<name> -n <namespace>

# Rollback if needed
kubectl rollout undo deployment/<name> -n <namespace>

##6.Networking & services.
# List services
kubectl get svc -n <namespace>

# Check endpoints
kubectl get endpoints -n <namespace>

# Describe a service
kubectl describe svc <service-name> -n <namespace>

# Test DNS resolution from inside a pod
kubectl exec -it <pod> -n <namespace> -- nslookup <service>

# Quick local test with port-forward
kubectl port-forward svc/<svc> 8080:80 -n <namespace>


##7.Storage(PVC/PV).
# List Persistent Volumes
kubectl get pv

# List PVCs
kubectl get pvc -n <namespace>

# Describe PV
kubectl describe pv <pv-name> -n <namespace>

# Describe PVC
kubectl describe pvc <pvc> -n <namespace>

# Check mount errors in pod
kubectl describe pod <pod-name> -n <namespace>


##8.Resources, logs & quick fixes.
# Resource usage (nodes)
kubectl top nodes

# Resource usage (pods)
kubectl top pods -n <namespace>

# Tail logs from multiple pods (external tools)
stern <pod-pattern> -n <namespace>
kubetail <pod-pattern> -n <namespace>

# Restart deployment
kubectl rollout restart deployment/<name> -n <namespace>

# Recreate a pod safely (controller will recreate)
kubectl delete pod <pod> -n <namespace>
