# OS Upgrade
In case you need to take nodes down for upgrading their underlying OS

## Pod eviction timeout
If a node goes down it's pods will be unavailable
- if pods are unavailable for more than pod-eviction-timeout (5min by default) then they're considered dead 
- `kube-controller-manager --pod-eviction-timeout=5m0s...`

- `k drain node-1` rmeoves all the pods from the node (for them to be scheduled on other nodes if they're part of RSs)
    - also cordons the node so other pods don't get scheduled on it
- when pod comes back up its still cordoned, so run `k uncordon node-1` to let pods schedule on it

