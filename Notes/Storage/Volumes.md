# Volumes
- Without volumes, the data that gets created by a pod is destroyed when the pod is destroyed

## Example: Volume on a host node
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: random-number-generator
spec:
  containers:
  - name: alpine
    image: alpine
    command: ["/bin/sh", "-c"]
    args: ["shuf -i 0-100 -n 1 >> /opt/number.out;"]    # saves a random number in file /opt/number.out
    volumeMounts:
    - mountPath: /opt                                   # Mount the volume at at /opt
      name: data-volume
  volumes:
  - name: data-volume
    hostPath:                                           # volume is a directory on the host node at path /data
      path: /data
      tyep: Directory
```

## Example 2: Volume on AWS EBS
```yaml
...
volumes:
- name: data-volume
  - awsElasticBlockStore:
    volumeId: <volume-id>
    fsType: ext4
```