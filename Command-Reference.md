# Configurations

/etc/kubernetes/	                                           # Config folder
/etc/kubernetes/pki/	                                       # Certificate files
/etc/kubernetes/kubelet.conf	                               # Credentials to API server
/etc/kubernetes/admin.conf	                                   # Superuser credentials
~/.kube/config	                                               # kubectl config file
/var/lib/kubelet/	                                           # Kubernets working dir
/var/lib/docker/, /var/log/containers/	                       # Docker working dir
/var/lib/etcd/	                                               # Etcd working dir
/etc/cni/net.d/	                                               # Network cni
/var/log/pods/	                                               # Log files
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf	       # Env
export KUBECONFIG=/etc/kubernetes/admin.conf	               # Env
/lib/systemd/system/kubelet.service                            # systemd unit file for kubelet
/lib/systemd/system/docker.service                             # systemd unit file for docker



journalctl -u kubelet                                          # to view the kubelet logs
journalctl -u kube-proxy

# Kubectl 

kubectl get all --all-namespaces

kubectl api-resources
kubectl api-resources --namespaced=true      # All namespaced resources
kubectl api-resources --namespaced=false     # All non-namespaced resources
kubectl api-resources -o name                # All resources with simple output (just the resource name)
kubectl api-resources -o wide                # All resources with expanded (aka "wide") output
kubectl api-resources --verbs=list,get       # All resources that support the "list" and "get" request verbs
kubectl api-resources --api-group=extensions # All resources in the "extensions" API group
kubectl api-versions                         # List api group
kubectl explain resourcename .  # kubectl explain pod


## Pods

kubectl run test --image=docker.io/nginx --dry-run=client -o yaml

kubectl explain pods

kubectl get pods                         # list all running pods in current active namespace
kubectl get pods -n kube-system          # list all running pods in specified namespace
kubectl get pods --all-namespaces        # list all running pods in all namespaces available
kubectl get pods -o wide                 # list all running pods in current active namespace wider output

kubectl describe pod <podname>              # detailed output about a pod in current namespace
kubectl describe pod <podname> -n namespace # detailed output about a pod in current namespace
kubectl describe pod <podname> -o wide      # detailed output about a pod wider output
kubectl describe pod <podname> -o yaml      # detailed manifest file from apiserver yaml format
kubectl describe pod <podname> -o json      # detailed manifest file from apiserver json format

kubectl logs my-pod                                 # dump pod logs (stdout)
kubectl logs -f my-pod                              # stream pod logs (stdout)
kubectl logs -f my-pod -c my-container              # stream pod container logs (stdout, multi-container case)

kubectl exec -it my-pod -- /bin/bash     # get interactive shell of the container in existing pod ( 1 container case )
kubectl exec -it my-pod -c my-container -- /bin/bash # get interative shell of the container in existing pod ( multi container ) 

kubectl expose pod nginx --port=80 --target-port=80  # expose pod as service within the cluster
kubectl expose pod nginx --target-port=80 --type=NodePort # expose pod as service within the cluster & outside of the cluster

kubectl delete pods <podname>                    # delete a pod in current active namespace
kubectl delete pods --all                        # delete all pods 
kubectl delete pod <pod-name> --grace-period=0 --force  # delete pod forcefully


## Controllers

## Kubernetes Controllers / workloads 

> **In Kubernetes, controllers are control loops that watch the state of your cluster, then make or request changes where needed.** 

> **Each controller tries to move the current cluster state closer to the desired state.**

#### Type of Kubernetes Controllers 

1. ReplicaSets
2. Daemonsets
3. Deploymentes
4. Jobs
5. CronJobs
6. StatefulSets
7. Replication Controller - Deprecated