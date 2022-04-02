# ENV Variables in Pods

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

    env:
    - name: DB_USER
      value: POSTGRES   # method 1: define value directly
    - name: DB_URI
      valueFROM: 
        configMapKeyRef # method 2: reference ConfigMap key (up next)
    - name: DB_PASS
      valueFROM: 
        secretKeyRef    # method 3: reference ConfigMap key (up next)
```