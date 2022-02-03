## Imperative Commands
### Create objects
```bash
$ kubectl run --image=nginx nginx # run a pod
$ kubectl create deployment --image-nginx nginx # create a deployment
$ kubectl expose deployment nginx --port 80 # create a svc - also pre-populates the correct selector
```

### Update objects
```bash
$ kubectl edit deployment nginx # edit the deployment via the default text editor
$ kubectl scale deployment nginx --replicas=5 # scale the deployment # of pods
$ kubectl set image deployment nginx nginx=nginx:1.18 # update the image of the nginx container in the nginx deployment
```

## Declarative Commands
### Create objects
```bash
$ kubectl apply -f nginx.yaml # creates/updates any obj based on what's in the file
$ kubectl apply -f /path/to/config-files # creates/updates multiple objects
```

### Update Objects
```bash
$ kubectl apply -f  nginx.yaml
```

