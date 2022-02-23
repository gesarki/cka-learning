# Taints and Tolerations
## Analogy: Bug, spray and person
- Person doesn't want bug to land on them
- Bug spray (**"taint"**) sprayed on person
- bug is **"intolerant"** to the spray, doesn't land on person
- another bug might be **tolerant** to smell, so it can land on the person

Therefore 2 things determine if **pods** can be scheduled on **nodes**:
- the taint on the nodes
- the tolerations on the pods

- taints used to set restrictions on which pods can be scheduled on which nodes

### By default, pods have no tolerations
So if a node has any taint (e.g. "blue"), none of the pods can be scheduled on it.

If you want a pod to be schedulable on a node with taint "blue", you need to give it "blue" toleration

## Example
- `k taint nodes node-name key=value:<taint-effect>`
    - taint-effect specifies what to do to pods without this tolerance
        - `NoSchedule` means don't schedule the pods
        - `PreferNoSchedule` means it'll try to avaoid placing the pods on this node, but not guaranteed
        - `NoExecute` same as NoSchedule, plus evict the old pods that don't have the tolerance that were already on this node before it got the taint
    - `taint nodes node1 app=blue:NoSchedule`
    - pod: 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```

## Master Node?
- By default, has taint on it
    - `k describe node kubemaster | grep Taint` gives:
    - `Taints: node-role.kubernetes.io/master:NoSchedule`