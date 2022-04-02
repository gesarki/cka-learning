# Image Security
If you just put `image: nginx` in your pod template:
```yaml
image: nginx
=>
image: docker.io/library/nginx      # library is the user/account. docker.io is the registry
```

## Private repositories
Need credentials to access them
`k create secret docker-registry regcred --docker-server=<...> --docker-username=<...> --docker-password=<...> --docker-email=<...>`
- docker-registry is a k8s secret type
Specify the credentials under spec.imagePullSecrets in the pod yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  tier: frontend
spec:
  containers:
  - image: private-registry.io/apps/internal-app
    name: nginx
  imagePullSecrets: regcred      # specify secret to use to authenticate to docker registry
```