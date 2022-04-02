# Authorization
Define what operations users/serviceaccounts can perform

## Mechanisms
### Node authorizer
    - grants access to nodes when they talk to the kube-apiserver
    - reads the "/O=SYSTEM:NODES" group specifier to determine if they have access

### ABAC
- attribute based access control
    - example: dev-user can view/create/delete PODs
- policy:
```yaml
kind: Policy
spec:
  user: "dev-user"
  namespace: *
  resource: pods
  apiGroup: *
```
- need a policy for every user and pass them all to the apiserver
- everyime you change the policy you must edit it and restart the kube-apiserver
- *difficult to manage*

### RBAC
- role-based-access-control
- create roles with permissions
- assign users to those roles
- can modify one role and it applies to all assigned users immediately

### Webhook
- outsource authorization to 3rd party who knows
    - e.g.: Open Policy Agent
- apiserver asks 3rd party for every request if the user is allowed to do that
- Open Policy Agent decides if user should be permitted or not

### AlwaysAllow

### AlwaysDeny

## How does it work
- Authorization modes are set on the kube-apiserver flags --authorization-mode
- if not set, set to AlwaysAllow by default
- otherwise can set multiple modes that it'll try using in order
    - e.g.: `--authorization-mode=Node,RBAC,Webhook`
        - user makes request
        - node authorizer says no (bc user is not a node)
        - RBAC says yes
        - user is permitted