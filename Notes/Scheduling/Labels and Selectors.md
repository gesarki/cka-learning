# Labels
Attach properties to each item for their `class`, `kind` and `color`.
# Selectors
Let you filter the items.
E.g.: `class=mamal` returns all the items with that class.

# How to define labels in k8s
In an object definition yaml, you can add labels under the `metadata` -> `labels` map property.

Then, you can do `k get pods --selector app=App1`

# Labels and selectors used by k8s in the background
## E.g.: ReplicaSet
```yaml
apiVersion: apps/v1          # different than ReplicationController
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
  template:

    metadata:
      name: nginx
      labels:
         app: myapp
         type: front-end
    spec:
      containers:
      - name: nginx
        image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end        # allows you to control replicas of pods that aren't defined in this yaml
```
The labels under `metadata` (top level) apply to the ReplicaSet. The labels under `spec.template.metadata` apply to the pods controlled by the RS.

# E.g.: Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
  selector:
    app: myapp          # Selector used to specify which pods this service targets.
    type: front-end
```

# Annotations
Used to record other details for information purposes. This can be tool details info like name, version, build info, etc. Or contact details like phone #s, emails, etc.