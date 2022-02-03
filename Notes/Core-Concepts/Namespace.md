# Namespaces

Analogy:
- every ns is like a house with people with names
    e.g.: House1: mark smith, mary smith. House2: Mark Doe, Jane Doe.
- Within the house, you can call eachother by your first names
- if you're referring to another house, you call eachother by your full names
- if you're not in a house, you call people by their full names
- **Each namespace is a house**

##  Default Namespaces
- `kubesystem` is a namespace that's automatically created with the cluster that runs built-in services/deployments
- `kube-public` is another. Contains resources that should be available to all users
- `Default` is the ns you're in by default. Meant to be used to deploy your resoucres

## Creating Namespaces
- example: want to run prod and dev on the same cluster? Create `prod` and `dev`
- each namespace can have it's own policies
- can each have a different resource limit

```yaml
# namespace-dev.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

```bash
$ kubectl create -f namespace-dev.yaml
# OR simply
$ kubectl create namespace dev # equivalent
```
 
 ## Namespace networking
 - for a client in the `Default` ns to connect to a mysql db in the `dev` ns:
    - `mysql.connect("db-service.dev.svc.cluster.local")`
    - [service-name].[namespace]."svc.cluster.local" format

## Forcing Namespace
```yaml
# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev # pin the ns by specifying it under the metadata obj
  labels:
    app: myapp
    type: front-end
spec:
  containers:
  - name: nginx-container
    image: nginx
```bash
$ kubectl create -f pod-definition.yaml # will deploy it in dev no matter where you run this from
```

## Changing namespace in context
```$ kubectl config set-context $(kubectl config current-context) --namespace=dev```
- now `kubectl get pod` returns the pods in ns dev
- ```kubectl get pods``` --all-namespaces works too

## Resource Quota
To allocate a range of resources and pods to the `dev` ns:
```yaml
# compute-quota.yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```
```kuebctl create -f compute-quota.yaml```