# Services
Q: Pod with web server runnin on it. How do we, as an external user, access it?

A: 
- node has ip addr 192.168.1.2
- laptop has ip 192.168.1.10
- internal pod network in range 10.244.0.0
- pod itself has ip 10.244.0.2

If we were to curl http://10.244.0.2 after ssh'ing into the k8s node, we'd reach the web server.

To reach the pod from outside the node, we need a **Service**.

## Types of Services
### NodePort
- provides external access to the application (like example above)
- helps by mapping a port on the node to a port on the pod
- 1. TargetPort: the port on the pod that the service forwards the requests to
  2. Port: the port on the Service from which the requst is sent. Svc is like a virtual server inside the node. Inside cluster, it has it's own IP address, the "Cluster IP of the Service"
  3. NodePort: on the node itself, accepts incoming external connections. between 30000-32767

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80            # if port isn't provided, defaults to targetPort
    nodePort: 30008     # if nodePort isn't provided, defaults to a random free port on the cluster
  selector:
    app: myapp          # copy paste from the labels: block of the pod
    type: front-end
```
 
 ```bash
$ kubectl create -f service-definition.yaml
$ kubectl get services      # Shows services. 80:30008/TCP means NodePort  is 30008, TargetPort is 80
$ curl http://192.168.1.2:30008
```
If multiple nodes match the selector, the service acts as a load balancer and randomly forwards the requests to the pods.

If you have multiple Nodes, the service is made available to all of them.
```bash
# requesting from any node in the cluster works
$ curl http://192.168.1.2:30008

$ curl http://192.168.1.3:30008

$ curl http://192.168.1.4:30008
``` 
### ClusterIP
You have multiple front-end, back-end and redis pods. Front-end needs to make requests from the back-end pods randomly, back-end needs to request from the redis pods randomly.

You don't want to use the pod's IPs because they may go down and be spun up again anytime.

**Create a service of type ClusterIP for each layer.** Each svc gets an IP and name inside the cluster and that's the name that should be used by other pods to access the service.


```yaml
# service-definition.yaml
apiVersion: v1
kind: Service
metadata:
  name: back-end

spec:
  type: ClusterIP
  ports:
  - targetPort: 80
    port: 80

  selector:         # selects which pods this service should target based on labels
    app: myapp
    type: back-end
```
```bash
$ kubectl create -f service-definition.yaml
# now the service can be accessed by other pods using the cluster's IP or the service name
$ curl http://back-end:80 # if we're exec'd on a pod in the same ns
```

### LoadBalancer
For your frontend apps, if you create NodePort services for each frontend app you'll have  too many IP:Ports combos accepting requests and forwarding. 

Ex: 2 front-end services, 4 nodes in cluster:
- you'll be able to access each service on each node's IP, you'll have 4 different IP each with 2 different ports = 8 IP:Port combos

If you deploy a Load Balancer with nginx or smth in front of the nodes, and set it up to route the requests between them, this would help. But that's what `LoadBalancer` is for.

`LoadBalancer` is only available on clusters running on supported cloud platforms like GCP or AWS or Azure. If it's set on a service without support, it'll default to just behaving like a `NodePort`.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
```