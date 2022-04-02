# Multi-Container Pods

- like DD agents on our services container
- share lifecycle
    - created and destroyed together
    - share network space (refer to eachotehr as localhost)
    - share same storage volumes

```yaml
# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  tier: frontend
spec:
  containers:
  - name: nginx # container 1
    image: nginx
    ports:
    - containerPort: 8080

  - name: log-agent # container 2
    image: log-agent
```