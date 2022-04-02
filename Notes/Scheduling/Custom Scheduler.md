Default scheduler:
- schedules pods on nodes based on taints, tolerations, affinity. That's it
- what if you want to build a more elaborate, custom scheduler?

# Custom Schedulers
- you can extend k8s to have multiple schedulers at the same time
- can make it so it uses the built in scheduler by default, but have specific pods use your custom scheduler

## Method 1: linux service
The default kube-scheduler is deployed by running the scheduler binary as a service (linux service, not k8s) on the master node.
- kube-scheduler.service:
```sh
ExecStart=/usr/local/bin/kube-scheduler \\
    --config=/etc/kubernetes/config/kube-scheduler.yaml \\
    --scheduler-name=default-scheduler
```
- you can deploy your own scheduler binary in the same way:
- my-custom-scheduler.service:
```sh
ExecStart=/usr/local/bin/my-custom-scheduler \\
    --config=/etc/kubernetes/config/my-custom-scheduler.yaml \\
    --scheduler-name=my-custom-scheduler                        # This is the name we'll use in our pod defs to use this scheduler
```

## Method 2: static pod (kubeadm)
Kubeadm deploys the scheduler via a static pod (def not shown - complicated)
- image is your-scheduler docker image
- command is
```yaml
    - kube-scheduler
    - --<lots-of-flags>
    - --leader-elect=true                       # if just one master, just set this to false. Otherwise do lock-object-name flag
    - --scheduler-name=my-custom-scheduler      # name to differentiate from default scheduler
    - --lock-object-name=my-custom-scheduler    # if you have multiple masters, include this line
```

## Use custom scheduler
```yaml
#pod-definition.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx
  schedulerName: my-custom-scheduler            # to use custom scheduler and not kube-scheduler
```

### View events
`kubectl get events` will show a "reason": "scheduled" and "source": "my-custom-scheduler" event saying "succesfully assigned default/nginx to node01"
 
### View scheduler logs
`kubectl logs my-custom-scheduler -n kube-system`