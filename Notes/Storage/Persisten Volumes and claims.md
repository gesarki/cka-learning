# Persisten Volumes
- instead of configuring volumes in every pod, we want to manage them centrally and have pods ask to use them (claims)

```yaml
# pv-definition.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:             # uses storage from the node's local directory (DO NOT USE FOR PROD)
    path: /tmp/data

  persistentVolumeReclaimPolicy: Retain     # What to do with the PV when the PVC is deleted
```

- use awsElasticBlockStore:
```yaml
...
spec
  awsElasticBlockStore:
    volumeID: <volume-id>
    fsType: ext4
```

### persistentVolumeReclaimPolicy
- retain: PV will be retained and won't be available to be used by other PVCs
- delete: PV will be deleted and won't be available to be used by other PVCs
- recycle: PV will be scrubbed and available to be used by new PVCs

# Persistem Volume Claims
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce           # must match that of the PV
  resources:
    requests:
      storage: 500Mi          # Uses the PV Defined above since there's no other ones available and it fits the needs
```

## Using PVCs in PODs
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"  # mount it just like a volume
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim    # specify PVC name here
```