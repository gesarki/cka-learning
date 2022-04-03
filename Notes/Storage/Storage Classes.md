# Storage Classes
- let you create PersistentVolumes in your storage provider automatically, instead of having to define a PV

```yaml
# sc-definition.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: google-storage
provisioner: kubernetes.io/gce-pd       # Google's persistent disk
parameters:
  type: pd-standard                     # additional optional parameters defined in google's docs
  replication-type: none
```

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
  storageClassName: google-storage      # will use the PV provided by the storageClass above
```

- can also provision multiple *classes* of storage
```yaml
# sc-silver-definition.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: silver
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-standard                     # standard disk
  replication-type: none
```

```yaml
# sc-gold-definition.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gold
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd                          # SSD
  replication-type: none
```

```yaml
# sc-platinum-definition.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: platinum
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd                          # SSD
  replication-type: regional-pd
```
- now you can refer to these from your PVCs as "silver", "gold" or "platinum"