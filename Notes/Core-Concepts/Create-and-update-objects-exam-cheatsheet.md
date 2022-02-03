# Exam tip:
***Use the `--dry-run=client` and `-o yaml` options!***
## Cheatsheet:
```sh
# create an nginx pod
$ k run nginx --image=nginx 

# generate pod manifest without actually creating it
$ k k run nginx --image=nginx --dry-run=client -o yaml 

# create a pod and a service both called "httpd", exposing pod port 80
$ kubectl run httpd --image=httpd:alpine --port=80 --expose

# create deployment
$ k create deployment --image=nginx nginx 

# generate deployment yaml file without creating deployment
$ kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

# generate deployment with 4 replicas
$ kubectl create deployment nginx --image=nginx --replicas=4
# scale deployment
$ kubectl scale deployment nginx --replicas=4

# Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
$ k expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml # will use the pod's labels as selectors

$ kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml # won't use pod's labels as selectors, instead will just assume selectors as app=redis. Generate file then edit the selectors before creating

# Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes
$ kubectl kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml # generate a definition file and then add the node port in manually before creating the service with the pod
```
