# Configure secrets in applications
- like config maps except store values in  encoded or hashed format 

- `k create secret generic <secret-name> \
    --from-literal=DB_HOST=mysql \
    --from-literal=DB_USER=root `

- k create secret generic <secret-name> \
    --from-file=<path-to-file> `

```yaml
# secret-data.yaml
apiVersion: v1
kind: secret
metadata:
  name: app-secret
data:
  DB_HOST: bX1zcWw=     # encoded with echo -n 'mysql' | base64
  DB_USER: c1zdcW==
  DB_Password: a1zcWwk
```

- `k get secret app-secret -o yaml` shows you the hashed values

## Secrets in Pods
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

    envFrom:                # inject all keys from secret
    - secretRef:
        name: app-secret
    
    env:                    # inject just one env var secret
      - name: DB_PASSWORD
        valueFrom:
          secretKeyRef:
            name: app-secret
            key: DB_PASSWORD

    volumes:                # inject secrets as files in a volume
    - name: app-secret-volume
      secret:
        secretName: app-secret
```
- *note* mounting secrets as volumes:
    - each key in the secret is created as a file with the value of the secret as it's contents
    - ```cat /opt/app-secret-volumes/DB_Password
         passwrd
      ```
 