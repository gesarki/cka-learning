# Authentication
2 types of users:
- Users (admins and developers)
- Service Accounts (bots)

## Users
- authentication handled by kube-apiserver
    - authenticates requests, then processes them

### Basic Auth mechanisms
- static password files
    - `user-details.csv`: 3 cols - password, username, user ID
    - pass to kube-apiserver with --basic-auth-file=user-details.csv
    - authenticate to apiserver in a curl command like this
    - curl -v -k htps://master-node-ip:6443/api/v1/pods -u "user1:password123"

    - can also have 4th column in `user-details.csv` to assign users to groups
- static token files
    - same as user-details.csv except first column is a token instead of a password
    - pass to kube-apiserver as --token-auth-file=user-details.csv
    - curl -v -k htps://master-node-ip:6443/api/v1/pods --header "Authorization: Bearer KpjCVbI7rCfAHYPafs83mhBCuSDF8234"

- **not a recommended approach**
    - insecure
    - need to consider volume mounts for kubeadm setup