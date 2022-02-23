# Resource requirements
- Each pod has resource requirements:
    - cpu
    - memory
    - disk space

- pod consumes resources of the node that its scheduled on
- scheduler takes into considuration amt of resources required by pod and the amt available
- if no node has sufficient resources, k8s doens't schedule the pod (status=Pending) and gives the reason (which constraint, ex.: failing CPU)

## Default resource requirements
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
      cpu: 1
    defaultRequest:
      memory: 256Mi
      cpu: 0.5
    type: Container
```
- container by default needs:
    - 0.5 CPU
    - 256 Mi memory

## Custom resource requirements
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
    resources:          # Add this to speficy custom resource requirements for your container
      requests:
        memory: "1Gi"
        cpu: 1
```

## CPU
- can be any value as low as 0.1
- 0.1 CPU = 100m CPU
- or can also go as low as 1m
- 1 CPU equivalent to 1 aws vCPU 

## Memory
- "G" = gigabyte, "M", "K" or
- "Gi" = Gibibyte, "Mi", "Ki"
    - those go up by x1024 bytes each time

# Limits
## CPU
- by default: 1vCPU is limit
## Mem
- 512Mi by default

## Custom
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
    resources:
      requests:
        memory: "1Gi"
        cpu: 1
      limits:
        memory: "2Gi"   # Add this to speficy custom res limits
        cpu: 2
```