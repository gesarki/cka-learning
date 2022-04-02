# RBAC
## Step 1: Create Role object
```yaml
# developer-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]                                               # "" indicates the core API group
  resources: ["pods"]
  resourceNames: ["blue", "yellow"] #                           # **optional**, if omitted then all pods
  verbs: ["get", "watch", "list", "create", "update", "delete"]
- apiGroups: [""]                                               
  resources: ["ConfigMap"]
  verbs: ["get", "watch", "list"]
```

`kubectl create role -f developer-role.yaml"

## Step 2: Create RoleBinding object
```yaml
# role-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: default # if this is omitted, role gives access to all namespaces 
  name: devuser-developer-binding
subjects:
- kind: User
  name: dev-user # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: developer 
  apiGroup: rbac.authorization.k8s.io
```

- `k get roles`
- `k get rolebinding`
- `k describe role developer`
- `k describe rolebinding devuser-developer-binding`

## Check your own access
```sh
$ kubectl auth can-i create deployments
no
```
```sh
$ kubectl auth can-i delete nodes
no
```
## Check another user's access
```sh
$ kubectl auth can-i create deployments --as dev-user
yes
```
## Can also check namespace
```sh
$ kubectl auth can-i create deployments --as dev-user --namespace test
no
```

**NOTE: use `k api-resources` to see the apiGroup of different resources**