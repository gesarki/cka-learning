# Certificates api

- instead of the admin having to ssh into the master node and approve all CSRs manually using the CA key on it
- k8s has a certificate signing process

## process
1. User generates key
`openssl genrsa -out jane.key 2048`

2. User creates new csr and sends it to admin
`openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr"`

3. admin creates a CertificateSigningRequest object in k8s with the base64 encoded csr
`cat janecsr | base64`, copy this to:
```yaml
# jane-csr.yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
    name: jane
spec:
    groups:
    - system:authenticated
    usages:
    - digital signature
    - key encipherment
    - server auth
    request:
        <copy encoded csr here>
```

4. Admin can see k8s CSRs with `kubectl get csr`

5. `kubectl certificate approve jane`
K8s will sign the cert using the CA key pairs and generates a certificate for the user.

6. `kubectl get csr jane -o yaml`
The generated certificate is shown here as part of the "status" section. It's encoded in base64 encoded:
`echo "LS0...Qo=" | base64 --decode` to get the decoded cert

Kube controller manager is responsible for doing this. You need to pass in the cluster signing cert and key file.
```yaml
# /etc/kubernetes/manifests/kube-controller-manager.yaml
spec:
  containers:
  - command:
    - kube-controller-manager
    - ...
    - --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
    - --cluster-signing-key-file=/etc/kubernetes/pki/ca.key