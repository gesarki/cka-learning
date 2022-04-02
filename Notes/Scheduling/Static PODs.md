# Static Pods

If you have a worker node and no master, (i.e. no kube API server, scheduler. Just kubelet), you can still schedule a pod on it.
- place the pod definition file in the node's filesystem at `/etc/kubernetes/manifests/`
- Kubelet periodically checks this directory for files, creates pods on the hosts
    - maintains it too:
    - if app crashes, it spins up another pod
    - if definition file is updated, recreates pod for changes to take effect
    - if file is removed, pod is deleted automatically


- `/etc/kubernetes/manifests` path can be changed to wtv you want by updating the `--pod-manifest-path` flag
    - in kubelet.service file
- or you can speficy the path in another file, `kubeconfig.yaml` and set the `config=kubeconfig.yaml` flag
    - your kubeconfig.yaml file will just contain `staticPodPath: /etc/kubernetes/manifests`

**These two options are good to know when inspecting an existing cluster**

Then use **`docker ps` to check the status of the pods** since we don't have kubectl utility (kube-apiserver isn't available)

- Kubelet can take input from 2 methods:
    - http requests sent by the kube-apiserver
    - files in the `/etc/kubernetes/manifests` folder
- apiserver will be aware of pods created both ways
    - `k get pods` will return the pod
    - will just be a read-only mirror object
    - **name of the node will always be appended to the name of the pod**

## Use case
- static pods are used to deploy control plane components (apiserver, controller-manager, etcd)
    - this is how the kubeadmin tool deploys these components
    - by placing the definition files in right path (`/etc/kubernetes/manifests`)
    