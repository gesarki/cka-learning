# Node Affinity
To Ensure that pods are hosted on particular nodes

## Example
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
  nodeSelector:
    size: large
```
Is the same as...

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
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In # or `NotIn` or `Exists`
            values:
              - large
              # can add more values here if you want it to be scheduled on other sizes too
```

## Node Affinity Type
- Ex: `requiredDuringSchedulingIgnoredDuringExecution:` or `preferredDuringSchedulingIgnoredDuringExecution`
What to do if there's no node with that label when trying to schedule a pod???
- 2 times during pod lifecycle to consider:
    - During Scheduling (pod is assigned host)
    - During Execution (something changes in environment (like node label changed) that affects node affinity)

### requiredDuringSchedulingIgnoredDuringExecution
- if no nodes with requested labels exist when scheduling: don't schedule
- if node label changes when pod already running: do nothing

### preferredDuringSchedulingIgnoredDuringExecution
- if no nodes with requested labels exist when scheduling: it's okay, choose another pod
- if node label changes when pod already running: do nothing