# Cluster Upgrade Process
## Core svcs (kube-apiserver, controller-manager, etc)
- Don't have to all be at the same version
- **kube-apiserver** is the main svc that all other components talk to, so none of the other svcs should be at a higher version than kube-apiserver
- **controller-manager** and **kube-scheduler** can be 1 minor version lower (1.10 -> 1.9)
- **kubelet** and **kube-proxy** can be 2 minor versions lower (1.10 -> 1.8) than kube-apiserver

- **kubectl** can be +- 1 minor version

## Support
Only 3 most current minor versions are supported at a time
- i.e. 1.13 (current), 1.12, 1.11 are supported

### Upgrading
One minor version at a time is recommended

1. upgrade your master node first 
  - core services will go down briefly
  - this means pods won't be rescheduled if they crash, you can't interact with the cluster using kubectl
  - but as long as your pods and nodes don't crash your users shouldn't be impacted

2. upgrade your worker nodes next
  - can do one by one and shuffle the pods so there's no downtime
  - or can do all at once with downtime
  - or you can add a new node to the cluster with the new k8s version
    - keep moving the pods to the new nodes until you only have new nodes with the new k8s version

### kubeadm - upgrade
*Follow upgrade guide for your version in kubernetes.io*
1. `kubeadm upgrade plan` tells you all that you need to do
  - `kubectl get nodes` will still show you the old version for the master node - this shows the kubelet version, not kubeapi server which was upgraded
2. Upgrade kubelet on the master node by ssh-ing to it and running `apt-get upgrade -y kubelet=1.12.0-00 && systemctl restart kubelet`
  - `k get nodes` will now show master's version upgraded
3. upgrade workers **kubelet** and **kubeadm**
  - `k drain node01`
  - ssh into node01
  - `apt-get upgrade -y kubeadm=1.12.0-00 && apt-get upgrade -y kubelet=1.12.0-00`
  - `kubeadm upgrade node config --kubelet-version v1.12.0`
  - `systmectl restart kubelet`
  - `kubectl uncordon Node01`
  - repeat for other nodes