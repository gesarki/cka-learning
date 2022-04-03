# Network Policies
By default, pods can communicate with any other pod on the cluster, (using svcs) even if they're on different nodes.

What we don't wan't that? (e.g.: frontend pods shouldn't talk to the database pods)

## Example:
- on DB pod
    - allow *ingress* (incoming) traffic only on port 3306 only from API pod
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db                  # Apply this network policy to the role: db pods
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: api-pod         # only incoming connections from  api-pod pods
      namespaceSelector:        
        matchLabels:
          name: prod            # that are in the prod namespace ( AND )
    - ipBlock:
        cidr: 192.168.5.10/32   # as well as from this server ( OR )
    ports:
    - protocol: TCP
      port: 3306                # allow them to connect to port 3306 using TCP
  egress:
  - to:
    - ipBlock:
        cidr: 192.168.5.10/32   # allow outgoing connections to this IP
    ports: 
    - protocol: TCP
      port: 80                  # at port 80 at that IP
    
```

*Note:* Not all network solutions support network policies. These do:
- kube-router
- calico
- romana
- weave-net

## What about responses?
- in example above, we only define ingress role that only allow incoming cmming connections from the api pods
- what about responses to the api pod from the DB pod?
- responses are automatically allowed
    - responses don't require network policy rules