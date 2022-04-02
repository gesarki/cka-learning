# API Groups
When you make a rest api request:
- curl https://kube-master:6443/version
- curl https://kube-master:6443/api/v1/pods
- the *version* and *pods* are **API Groups**

There are:
- /metrics
- /healthz
- /version
- /api
- /apis
- /logs

## /api ("core" group)
- where all the core functionality exists (resources like pods, jobs, etc)

## /apis ("named" group)
- /apps, /extensions, /networking.k8s.io, /storage.k8s.io, /authentication.k8s.io, /certificates.k8s.io
- newer feature available under here
- /apis/apps/v1/deployments or replicasets or statefulsets
- /networking.k8s.io/v1/networkpolicies
- each resource has verbs (list, get, update, create, delete)
