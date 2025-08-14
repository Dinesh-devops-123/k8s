# Kubectl Context Switching Commands

## Get all contexts
kubectl config get-contexts

## Switch to a specific context(cluster to cluster)
kubectl config use-context <context-name>
kubectl config use-context kind-cka-cluster2

## Get current context
kubectl config current-context
