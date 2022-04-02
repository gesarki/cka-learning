# Service Accounts
User accounts are for users
Service accounts are for **Services**
- i.e. apps

## Create
`k create serviceaccount dashboard-sa`
- when serviceaccount gets created, it also creates a **token** automatically
- Token is what's used by the external app when authenticating to the k8s api
- token is created as a `secret` object, referred to in the serviceaccount object
    - `k describe secret dashboard-sa-token-kddbm`
- used like so:
    - `curl https://192.168.56.70:6443/api -insecure --header "Authorization: Bearer <token>"`

- assign perimssions to is using RBAC

## If the app is running on k8s itself
Then this whole process can be simplified by passing the secret to the pod using a VolumeMount

### This is done automatically for the `default` sa
- when a ns is created, a `default` sa gets created also
- pods deployed in the namespace get the default sa's token via a volume mount automatically too
    - k describe pod my-pod
        ```
        ...
        Mounts:
            /var/run/secrets/kubernetes.io/serviceaccount from default-token-j4hkv (ro)
        ```
- can set pod's spec.automountServiceAccountToken: false if you don't want this behaviour


### To use a sa you created:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx
  serviceAccountName: dashboard-sa      # specify sa name to use here
```

`k describe pod nginx` will show:
```
...
Containers:
  Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from dashboard-sa-token-kddbm (ro)
...
Volumes:
  dashboard-sa-token-kddbm:
    Type:                    Secret (a volme populated by a secret)
    SecretName:              dashboard-sa-token-kddbm
    Optional:                false
```