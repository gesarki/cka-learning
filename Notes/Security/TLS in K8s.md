# TLS in K8s
All connections (clients, nodes) in cluster and going in/out of cluster needs to be TLS encrypted
- all clients use client certs
- all servers use server certs

## Server certificats for Servers components
- kube-apiserver generates
    - apiserver.crt
    - apiserver.key 
- etcd server
    - etcdserver.crt
    - etcdserver.key
- kubelet server
    - kubelet,crt
    - kubelet.key

## Client certificates for Clients
- we (admins) need to secure our comms to the kube-apiserver
    - admin.crt
    - admin.key
- kube-scheduler talks to the kube-apiserver
    - scheduler.crt
    - scheduler.key
- kube-controller-manager also talks to kube-apiserver
    - controller-manager.crt
    - controller-manager.key
- kube-proxy also talks to kube-apiserver
    - kube-proxy.crt
    - kube-proxy.key

## kube-apiserver and kueblet-server talk to eachother, and kube-apiserver talks to etcd, so they can also create separate client keys for that
- apiserver-kubelet-client.crt
- apiserver-kubelet-client.key
- kubeliet-client.crt
- kubeliet-client.key
- apiserver-etcd-client.crt
- apiserver-etcd-client.key

## Certificate Authority also has private (.cert) and public (.key) keys
They're used to sign all the server public keys

# Generating client SSL certificates
## First, the Certificate Authority's cert
- `openssl genrsa -out ca.key 2048` creates ca.key private key
- `openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr` creates certificate signing request (CSR)
    - requests a cert (public key) tied to the private key ca.key, to be signed by a CA that others will trust
    - we'll sign it ourselves with our ca.key next
- `openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt`
    - self-sign the csr, generating a cert ca.crt
- for all other certs, we will use this CA key pair to sign them

## Next, generate the Admin user's key pair
- same first 2 steps as above: create admin.key and admin.csr
    - except "/CN=kube-admin" - this is the "username" that shows up in logs when admin communicates
- `openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt`
    - we specify the ca certificate here
    - this means the admin's cert is signed by the CA above
    - now it's a valid cert within cluster
- *note: specify a group that this user should belong to, you must include it in the CSR as such*
    - `... -subj "/CN=kube-admin/O=system:masters"

## Same procedure to generate client certificates for all other components that access kube-apiserver

## kube-scheduler key pair
- same thing but name must be prefixed with the keyword "system"
    - e.g.: `openssl req -new -key kube-scheduler.key -subj "/CN=SYSTEM:KUBE-SCHEDULER" -out kube-scheduler.csr`

## kube-controller-manager
- same as kube-scheduler - "SYSTEM" prefix

## kube-proxy
- same as kube-scheduler - "SYSTEM" prefix

# Integrating with cluster
- kube-config.yaml:
```yaml
apiVerion: v1
kind: Config
clusters:
- cluster:
    certificate-authority: ca.crt       # cluster's ca cert
    server: https://kube-apiserver:6443
  name: kubernetes
users:
- name: kubernetes-admin                # add admin's cert and key here
  client-certificate: admin.crt
  client-key: admin.key
```

# Generating server SSL certs
## kube-apiserver
Needs to have a bunch of SANs (Subject Alternative Names)
- kubernetes, kubernetes.default, kubernetes.default.svc, kubernetes.default.svc.cluster.local, 172.17.0.8, etc
To do this you'll need to modify your csr generation to include an openssl config
- `openssl req -new -key apiserver.key -subj "/CN=kube-apiserver" -out apiserver -config openssl.cnf`
- openssl.cnf:
```cnf
[req]
req_extentions = v3_req
distinguished_name = req_distinguished_name
...
subjectAltName = @alt_names
[alt_names]
DNS.1 = kubernets
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
IP.1 = 10.96.0.1
IP.2 = 172.17.0.87
```
- sign the csr with your ca cert and ca key as usual

### Providing certs to the kube-apiserver process
Either in the kube-apiserver static pod or start script:
```sh
kube-apiserver \\
--etcd-cafile=/var/lib/kubernetes/ca.pem \\                     # ca cert, public and private key for talking to etcd
--etcd-certfile=/var/lib/kubernetes/apiserver-etcd-client.crt \\
--etcd-keyfile=/var/lib/kubernetes/apiserver-etcd-client.key \\
...
--kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \\   # ca cert, public and private key for talking to kubelet
--kubelet-client-certificate=/var/lib/kubernetes/apiserver-kubelet.crt \\
--kubelet-client-key=/var/lib/kubernetes/apiserver-kubelet.key \\
...
--client-ca-file=/var/lib/kubernetes/ca.pem \\   # ca cert, public and private key for clients to talk to kube-apiserver
--tls-cert-file=/var/lib/kubernetes/apiserver.crt \\
--tls-private-key-file=/var/lib/kubernetes/apiserver.key \\
```

## kubelet
Needs key-cert pair for each node in the cluster since kubelet runs on all of them
- "/CN=NODE01"
