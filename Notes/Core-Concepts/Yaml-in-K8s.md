# Yaml in K8s
- all objects (pod, replicaset, deployment, etc) can be created with a kubernetes definition file
- all definition files have 4 top-level required fields:
    - apiVersion
        - pods: v1
        - service: v1
        - replicatSet: apps/v1
        - deployment: apps/v1
    - kind
        - Pod/Service/ReplicatSet/Deployment
    - metadata: dictionary of 
        - name: string
        - labels: dict of wtv you want
    - spec: depending on object you're creating
        - for pod with 1 container:
        `containers: (list)
            - name: nginx-container
              image: nginx`

- example:
```yaml
apiVersion: v1 # req'd
kind: pod # req'd
metadata: # req'd
    name: myapp-pod
    labels:
        app: myapp
        type: front-end
        keysandvalues: can-be-anything
spec: # req'd
    containers:
        - name: nginx-container
          image: nginx
```
