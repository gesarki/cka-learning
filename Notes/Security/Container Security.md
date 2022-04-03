# Container Security
In Docker when you run the ubuntu image you can pass in options to configure the user ID, the linux capabilities, etc.
These can be added to the pods or containers in k8s as well.

*Note* You can configure these at the pod or container level. If you configure it at the pod level, the settings will carry over to all the containers running in the pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  tier: frontend
spec:
  securityContext:          # can define it at pod level
    runAsUser: 1000
    capabilities: ["MAC_ADMIN"]
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep", "3600"]
    securityContext:          # or at the container level
      runAsUser: 1000
      capabilities: 
        add: ["MAC_ADMIN"]
```