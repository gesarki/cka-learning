# Node Selecotrs
Scenario
- 1 largre node, 2 small nodes
- 1 large workload, 2 smaller workloads
- want to schedule large workload on large node because other 2 will run out of mem

To schedule a pod on a large node: add nodeSelector to your pod's spec
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
    size: large  # corresponds to labels on pods
```

To label a node:
`kubectl label node node-1 size=large`

## nodeSelector has limited functionality
- doesn't let you allow large OR medium for ex.
- doesn't let you say "Not small" for ex.
- need **node affinity**