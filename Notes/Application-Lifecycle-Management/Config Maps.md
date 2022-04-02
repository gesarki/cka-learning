# Config Maps

Example:
```yaml
# config-map.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

```yaml
# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx

    envFrom:            # pass a whole configmap to the pod
    - configMapRef:
        name: app-config

    env:                # pass a single key from the CM
      - name: APP_COLOR
        valueFrom:
          configMapKeyRef:
            name: app-config
            key: APP_COLOR
    
    volumes:            # pass CM as a file in a volume
    - name: app-config-volume
      configMap:
        name: app-config
```
## Create imperatively
### From literal
- `k create configmap app-config-name \
    --from-literal=APP_COLOR=blue \
    --from-literal=APP_MODE=prod`

### From file
- `k create configmap app-config-name \
    --from-file=app_config.properties`

