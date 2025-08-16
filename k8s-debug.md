1.confirm cluster & namespace.
#kubectl config current-context (check current context)
#kubectl config get-contexts  (list contexts)
#kubectl config use-context <name> (switch context)
#kubectl get ns |kubectl get pods -n <namespace> (list namespaces)

2.Big picture:nodes,pods & events.
#kubectl get nodes (list nodes)
#kubectl get pods -A (all pods)
#kubectl get deployments -A (All deployments)
#kubectl get events --sort-by=.metadata.creationTimestamp -A (recent events)

3.Inspect the failing pod.
#kubectl describe pod <pod-name> -n <namespace> (describe the pod)
#kubectl log <pod> -n <namespace> (view logs)
#kubectl logs <pod> -c <container> -n <namespace> (logs for a specific container)
#kubectl exec -it <pod> -n <namespace> --/bin/sh (open a shell inside the pod)

4.Probes & health checks.
#check readiness/liveness probe info in 'kubectl describe'
#kubectl exec -it <pod> -n <namespace> --curl -sv localhost:<port>/health (test the probe endpoint from inside the pod)
#Adjust probe timeouts if needed

5.Rollouts,history & recover.
#kubectl rollout status deployment/<name> -n <namespace> (check rollout status)
#kubectl rollout history deployment/<name> -n <namespace> (view rollout history)
#kubectl rollout undo deployment/<name> -n <namespace> (rollback if needed)

6.Networking & services.
#kubectl get svc -n <namespace> (list services)
#kubectl get endpoints -n <namespace> (check endpoints)
#kubectl describe svc <service-name> -n <namespace> 
#kubectl exec -it <pod> -n <namespace> --nslookup <service> (DNS test from a pod)
#kubectl port-forward svc/<svc> 8080:80 -n <namespace> (quick local test with port-forward)

7.Storage(PVC/PV).
#kubectl get pv
#kubectl get pvc -n <ns> (list PVC's)
#kubectl describe pv <pv-name> -n <ns> (describe a PVC)
#kubectl describe pvc <pvc> -n <ns> 
#check mount errors in pod 'kubectl describe pod'

8.Resources, logs & quick fixes.
#kubectl top nodes                              # Resource usage (nodes)
#kubectl top pods -n <namespace>                # Resource usage (pods)
# Tail logs from multiple pods (external tools)
#stern <pod-pattern> -n <namespace>
#kubetail <pod-pattern> -n <namespace>
#kubectl rollout restart deployment/<name> -n <ns> (restart deployment)
#kubectl delete pod <pod> -n <ns> (recreate a pod safetly & controller will recreate)