# How scheduling works
Every pod has a spec field called `nodeName` that by isn't set by default.

Scheduler goes through all that pods and finds those that don't have the property set. Those are the candidates for scheduler. It then identifies the right node for the pod by running the scheduling algorithm. It sets the name of the node to the value of `nodeName` 

## If there's no scheduler
The pods continue to be in a "Pending" state.

You can manually set the `nodeName` field to a node name **at creation time** to schedule the pod. The pod then gets assigned to the specified node.

If the pod was already created, k8s won't allow you to modify the `nodeName` property of a pod.

Another way to schedule a pod that was already created is to create a `kind: Binding` object just like a scheduler does, ex:
```yaml
apiVersion: v1
kind: Binding
metadata:
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: node02
```
then send a post request to the pod's binding API with the data set to the binding object in JSON format:
``` bash
curl --header "Content-Type:application/json" --request POST --data '{"apiVersion":"v1", "kind": "Binding", ...} http://$SERVER/api/v1/namespaces/default/pods/$PODNAME/binding/
```