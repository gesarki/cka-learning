# Metrics server
- can have one per cluster
- retrieves metrics from each of the k8s nodes and pods, aggregates them, stores them in mem
    - *note* relies on in-memory storage, so can't use to see historical data
- kubelet contains cAdvisor sub-component (container advisor)
- retrieves performance metrics from the pods and exposes them to the kubelet api to make the metrics avaiable to the metrics server
- **to deploy**
    - `git clone github.com/kubernetes-incubator/metrics-server.git`
    - `k create -f deploy/1.8+/`
- this enables you to run the command `k top node` and `k top pod`