# Rollout and Versioning
- When you first create a deployment, it triggers a new rollout, creates new revision: "rev 1"
- when you make a new deployment, it triggers and new rollout, and that creates a new revision, "rev 2"
- enables us to rollback to previous version if necessary

- `k rollout status deployment/<deployment-name>` to get status of the rollout
- `k rollout history deployment/<deployment-name>` to get each revision and the reason for their rollout

# Deployment strategy
**Rolling Update** (default)
- this means instead of taking down all old pods and then spinning up new ones, it takes down the old version and brings up new version one by one, so there's no down time

# What triggers an update?
- updating any of 
    - container image
    - container image version
    - replica count

# Upgrades
- create a new replicaset
- scale scale down preview replicaset and scale up new replicaset (respecting rolling update)

# Rollbacks
- if something is wrong with the new version of build
- `k rollout undo deployment/<deployment-name>`
- scales up old replicaset and scales down new replicaset

# Commands
- 