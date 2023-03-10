# Content Notes

- `default` namespace used when a namespace is not specified (if kube-context is not set)
- `kube-system` namespace created by kubeadm
- `etcd` - stacked (on each control plane node) vs external (external to cluster)
- `Kompose` - translates docker-compose files into k8s objects
- `Kustomize` - share and re-use templated configs for k8s apps

## Draining Nodes
```
# drain node workloads and reschedule to another node (Node Status => SchedulingDisabled)
# --force required to drain pods not managed by Deployments, etc 
# --ignore-daemonsets required to drain pods from daemonsets on node
kubectl drain <node> [--force] [--ignore-daemonsets]

# allow drained node to be scheduleable again (removes SchedulingDisabled status from node)
# uncordoning process will not result in pods being distributed to uncordoned node
kubectl uncordon <node>
```


## Upgrading k8s w/ kubeadm

Control plane upgrade steps:
1. upgrade kubeadm on control plane node
2. drain control plane node
3. plan upgrade (kubeadm upgrade plan <k8s-ve)
4. apply upgrade (kubeadm upgrade apply)
5. upgrade kubelet and kubectl on control plane node & reload kubelet
6. uncordon control plane node

Worker node upgrade steps:
1. drain node
2. upgrade kubeadm
3. upgrade kubelet configuration (kubeadm upgrade node)
4. upgrade kubelet and kubectl & reload kubelet
5. uncordon node


## kubectl
```
# list all k8s resource types available in cluster (useful for short names)
kubectl api-resources

# list object(s) or return definition of object in cluster
kubectl get <object-type> <object-name> -o <output> --sort-by <JSONPath> --selector <selector>

# get detailed info about a k8s object (consolidates data from multiple api calls)
kubectl describe <object-type> <object-name> 

# create k8s resource (will fail if resource already exists)
kubectl create -f <resource-config>

# update a k8s resource (will create if it doesn't exist)
kubectl apply -f <resource-config>

# delete k8s resource in cluster
kubectl delete <object-type> <object-name> 

# run command/ connect to container - container must have command installed
kubectl exec <pod> -c <container> -- <command> 
```
