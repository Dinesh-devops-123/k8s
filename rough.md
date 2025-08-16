## 8.Resources, logs & quick fixes.
# Resource usage (nodes)
kubectl top nodes

#Resource usage (pods)
kubectl top pods -n <namespace>

#Tail logs from multiple pods (external tools)
stern <pod-pattern> -n <namespace>
kubetail <pod-pattern> -n <namespace>

#Restart deployment
kubectl rollout restart deployment/<name> -n <namespace>

#Recreate a pod safely (controller will recreate)
kubectl delete pod <pod> -n <namespace>