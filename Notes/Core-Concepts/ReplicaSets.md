# Replication Controllers

 - when we have our application running in one pod, if it crashes there's nothing to tell it to restart
 - replication controller lets you run multiple instances of a pod in a k8s cluster -> **high availability**
 - automatically starts a new pod when existing one fails
    - ensures specified # of pods are running at all times

## Load balancinc and Scaling
- rep controller will deploy additional pods if demand increases
- can span multiple nodes, so pods in rep controlelr aren't constrained to only run on one node

## Replication controller (old) vs. Replica Set (new)

- replicaSet allows you to control replicas of pods not defined within the definition of the replica set as well. e.g.:

rc-definition.yaml
``` yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template:
  
    metadata:                #    
      name: nginx            #        
      labels:                #    
         app: myapp          #        
         type: front-end     #  } Copy-paste from pod definition yaml, excluding apiVersion and kind               
    spec:                    # 
      containers:            #        
      - name: nginx          #        
        image: nginx         #            

  replicas: 3
```

``` bash
$ k create -f rc-definition.yaml
$ k get replicationcontroller   # shows 3 desired, current, ready
$ k get pods                    # shows 3 pods
```

replicaset-definition.yaml
```yaml
apiVersion: apps/v1          # different than ReplicationController
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
  template:
  
    metadata:                #    
      name: nginx            #        
      labels:                #    
         app: myapp          #        
         type: front-end     #  } Copy-paste from pod definition yaml, excluding apiVersion and kind               
    spec:                    # 
      containers:            #        
      - name: nginx          #        
        image: nginx         #            

  replicas: 3                # same as ReplicationController
  selector:
    matchLabels:
      type: front-end        # allows you to control replicas of pods that aren't defined in this yaml
```

- ^ this replicaset will monitor pods like this:
```yaml
metadata:
  name: myapp-pod
  labels:
    type: front-end
```

- if the above ReplicaSet is deployed and there's already 3 pods with those labels already running, it won't do anything
    - you still need to include the `template` field since it'll use that when it needs to spin up a new pod if one dies
- the selector **MUST** match the labels in the template

## To Scale a Replicaset
```bash
# update replicaset-definition.yaml and 
$ k replace -f replicaset-definition.yaml

# OR 
$ k scale -replicas=6 -f replicaset-definition.yaml

# OR
$ k scale --replicas=6 replicaset myapp-replicaset # use [Type] [Name] format
```

## commands:
```bash
kuebctl create -f replicaset-definition.yaml
kubectl get replicaset
kubectl delete replicaset myapp-replicaset # Also deletes all underlying PODs!!
kubectl replace -f replicaset-definition.yaml
kubectl scale --replicas=6 replicaset myapp-replicaset  
```