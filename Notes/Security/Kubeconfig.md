# kubeconfig
3 sections:
- clusters
    - name
    - ca
    - server (address)
- users
    - name
    - client certificate
    - client key
- context
    - marry users with clusters
    - speciy which users you want to use to login to which cluster

example:
```yaml
apiVersion: v1
clusters:
- name: cluster.ada-dev.support
  cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM4akNDQWRxZ0F3SUJBZ0lNRnA4NWFUU1krbGhrTWFVdE1BMEdDU3FHU0liM0RRRUJDd1VBTUJVeEV6QVIKQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13SGhjTk1qRXdPREkxTVRjeE9ETTBXaGNOTXpFd09ESTFNVGN4T0RNMApXakFWTVJNd0VRWURWUVFERXdwcmRXSmxjbTVsZEdWek1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBCk1JSUJDZ0tDQVFFQXBMN0hmckM3NmlYQisrT1hvQk1zZG9MWTM3cGhkMDF2ZWFmS3l5YzB0S0I1a2lObFpmbGEKaXBFUndXRDZCYVc1Ri8rMGVzdGpQb3JJRnRUT3BTcjQ3Z3k2RktCcFlUb2c5QTlkMnZvVDFDMnV4ZGo4WmFOWApPTEVHNmZlUFl3SXpjSlBla2VsUWdNbWFLbDcweHExVU41eFdRMWI2WDNsZ25VazMrTjQ1amhoaVNTRmJSY1JyCjZ5aVBxc2NETkpTTDJmM2RJLzFaQmNETWw5QzRFYWF1c1ExWXE3aHFqYU1xbHFxZm04NG00T0g4NXUvdjBPVCsKSXFKbE85c2dSNWhVcTdpZFlYbGNRbi9jYnNYaXZJNTc0UzZXcHRwd0ZKT1FZeWdnVWIwTTVqaXoxZldESEtQRQpvZW1aeTlPWkFnT3hZNHNDc2l0TmFNVnFyTnRWUUlRQzlRSURBUUFCbzBJd1FEQU9CZ05WSFE4QkFmOEVCQU1DCkFRWXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QWRCZ05WSFE0RUZnUVVYdkE4RHBtUUlvMVFlS2xybk9MYmUyb2IKK0djd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFKNlI5LzlqQnc2dlFCOGhTRGsvSXJycmNEam5SQWcrNk5tOAp1NzNlemZ3N2FhMjJuUXdzZ21RZ2dhOXR1RkRRc0pieUphWVhqOXN0by9RcHp6YjZ3cTREbU5XNlpWVkF4N3d6Ck9zbzRFdjdDQmpuTklEb3lNdHBDTFA1dmR4MlRFaHVvTG5ya1FiMmpqS092U3c4QU1xaTBYMllRdE5USUEyQ2sKR29wdVFDNWNkeVI4Q0F2UVdBdkJGbnppSGtUR1VIZGZ6aDZKYXc0eGVmMkxDeFU2S2RzQ1RGZVNNeWtOSEczSgpRVGhRZ1ZwRjIrdFdSY0VJanF2amFOWld3QXR1T0RBSW9qWm12ZUhWL2ZtbnB5Z1pFNFNXc1VCd3lHOCttN1luCkNxQXNFZDQrem5ZQ2NlazUvbFo3Q1ZzQzFjbmZNM2laKzM2dGJ6am9nYjNCcnZHRytuYz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://api.cluster.ada-dev.support

contexts:
- name: cluster.ada-dev.support
  context:
    cluster: cluster.ada-dev.support
    user: user@ada-dev.support
    namespace: airflow # optional

users:
- name: user@ada-dev.support
  user:
    client-certificate: /users/home/kubernetes/client.crt
    client-key: /users/home/kubernetes/client.key

current-context: cluster.ada-dev.support # marks default context
```

# Commands
- `k config use-context cluster.ada-dev.support`
- `k config current-context`