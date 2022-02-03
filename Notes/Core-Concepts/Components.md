# Components

## etcd
Distributed, reliable key-value store that is simple and fast

- key-value store: used to store simple data like configuration settings
- ETCD is like database in that you can install and host it on your machine, install a etcd client to interact with it, and use client to run commands like  `set key1 value1`, `get key1` 

- stores info on:
    -  nodes
    - PODs
    - configs
    - secrets
    - accounts
    - roles
    - bindings
    - others

- every change you make such as deploying pods, replicasets, wtv is recorded in the etcd server
- only once it's recorded in the etcd server is the change considered "complete"

### setup
- manual: download and install etcd from scratch
    - have to install it as a `service` in your master node yourself
    - pass in options
        - `advertise-client-urls` is an option, it's the IP, port that the etcd db listens on

- kubeadm: it's installed onto the cluster as a pod
    - can interact with it using the etcdctl command when exec'd on the pod

## Kube-api server
When a request is made to the cluster the kube-api server
- authenticates the user
- validates request
- retrieves data
- updates ETCD
- scheduler finds best node and notifies kube-api server
- kubeapi-server talks to the kubelet on that pod to start the 

kubeapi server is the only thing that talks to etcd

- kubeapi server is available as binary on the k8s release page, need this if installing k8s without kubeadm
- otherwise it's deployed as a pod in the kube-system namespace on the master node

## Controller manager
- controller is process that continuously monitors state of entire system, works towards bringing system to the desired state

### Node Controller
    - responsible for monitoring status of the nodes and taking necessary actions to keep app running
    -  uses API-server
    - checks status of the nodes every 5s
    - if it stops receiving a heartbeat from a node for 40s it's marked as "unreachable"
        - gives it 5 min to come back up, after which it reassigns pods on that node to another node (if the pods are part of a replicaset)

### replication-controller
    - monitors status of each pod
    - if dies, creates another one

### Many more controllers

kubeadm deploys kube-controller-manager as a pod in the kube-system namespace, when k8s is manually installed it's run as a service on the master node

## kube-scheduler
chooses which node to assign each pod to
- first filters out pods that just won't work (e.g. not enough CPU capacity)
- then ranks possible nodes based on some criteria
    - e.g. leftover CPU on the node after getting the pod - the more leftover the better
can write your own scheduler as well

- installed manually by running it on master node, otherwise kubeadm installs as a pod in kube-system ns on the master node 

## kubelet
- registers node w clustre
- when gets instructions to run a pod, tells container runtime engine to pull image and run a container
- kubelet monitors state of pods and containers on it and reports them to kubeapi server on a timely basis

**kubeadm does NOT deploy kubelets!**
- you must always manually install kubelets on the worker nodes
    - download, install, run as service on each worker node

## Kube-proxy
- in cluster, every pod can reach every other pod
- bc of pod network deployed to cluster
- kube-proxy runs on each node in cluster
    - looks for new services, every time a service is created, it creates appropriate forwarding rules in the iptables of every node to forward the requests to the services to the backend pods
    - can also use other tools than iptables

to install:
- kubeadm deploys as a daemonset, so a single pod is always deployed on each node in cluster
- w/o kubeadm,  download, extract, install  and run kube-proxy as a service on each node
