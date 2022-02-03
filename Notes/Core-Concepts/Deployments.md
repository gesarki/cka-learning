# Deployments
Example requirements of deploying product to production:
- multiple instances: for load balancing
- when your docker image gets an update in the registry, the instances update seamlessly
    - not all at once, "rolling update"
- ability to roll back changes that relased an error
- ability to apply changes in bulk to an running app

## Example
```yaml
# deployment-definition.yaml
apiVersion: apps/v1
kind: Deployment  # exactly the same as replicaset except change `kind` to Deployment
metadata:
  name: myapp-deployment
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
      type: front-end       
```

```bash
$ kubectl create -f deplotment-definition.yaml
$ kubectl get deployments # shows myapp-deployment with 3/3
$ kubectl get replicaset # shows myapp-deployment-6245325b1 which was created by the deployment
$ kubectl get pods # 3 pods called myapp-deployment-*
```