# Daemon Sets
Like deployments but they run **one copy** of your pod on each node in your cluster.
- whenever a node is added to cluster, DaemonSet ensures a replica of the pod is automatically added to the new node
- when node is removed, replica is removed too
- good for monitoring/logging agents
- also good for kube-proxy
- also good for networking solns like weave-net

## Definition
Same as ReplicaSet except change `Kind` to DaemonSet
```yaml
apiVersion: apps/v1          # different than ReplicationController
kind: DaemonSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: nginx
      labels:
         app: myapp
         type: front-end
    spec:
      containers:
      - name: nginx
        image: nginx

  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

## Implementation
Starting in K8s v1.12: uses NodeAffinity rules