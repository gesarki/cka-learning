# Backup and Restore

## Option 1: Backuping Resource Configs
- basically backup all the resource manifests
- Tools like Velero can do this
- by querying the kube-apiserver for all the resources
    - `k get all -A -o yaml > all-deployed-services.yaml

## option 2: ETCD Cluster
- stores state of the cluster
- when configuring ETCD we configure a location where all the data will be stored
    - `--data-dir=/var/lib/etcd`
    - this can be configured to be backed up by backup tool
- ETCD also has its own snapshot method
    - `etcdctl snapshot save snapshot.db` saves it to the file `snapshot.db`
    - `etcdctl snapshot status snapshot.db` shows the status of the snapshot 
- to restore etcd backup:
    - `service kube-apiserver stop` # kube-apiserver needs to be stopped to restore the backup
    - `etcdctl snapshot restore snapshot.db --data-dir /var/lib/etcd-from-backup` # needs to restore to a different directory than the original path so that new members can't join this etcd cluster
    - update the `etcd.service` and change `--data-dir=/var/lib/etcd-from-backup`
    - systemctl daemon-reload # reload the service daemon
    - service etcd restart
    - service kube-apiserver start

*note* For all etcdctl commands:
- `--endpoints=https://127.0.0.1:2379 --ca-cert=/ect/etcd/ca.crt --cert=/ect/ectd/ectd-server.crt --key=/etc/etcd/etcd-server.key`

# Working with ETCDCTL

etcdctl is a command line client for etcd.



In all our Kubernetes Hands-on labs, the ETCD key-value database is deployed as a static pod on the master. The version used is v3.

To make use of etcdctl for tasks such as back up and restore, make sure that you set the ETCDCTL_API to 3.



You can do this by exporting the variable ETCDCTL_API prior to using the etcdctl client. This can be done as follows:

`export ETCDCTL_API=3`

On the Master Node:

```bash
$ export ETCDCTL_API=3
$ etcdctl version
etcdctl version: 3.3.13
api version: 3.3
```



To see all the options for a specific sub-command, make use of the -h or --help flag.



For example, if you want to take a snapshot of etcd, use:

`etcdctl snapshot save -h` and keep a note of the mandatory global options.



Since our ETCD database is TLS-Enabled, the following options are mandatory:

`--cacert`                                                verify certificates of TLS-enabled secure servers using this CA bundle`

`--cert`                                                    identify secure client using this TLS certificate file

`--endpoints=[127.0.0.1:2379]`          This is the default as ETCD is running on master node and exposed on localhost 2379.

`--key`                                                      identify secure client using this TLS key file





Similarly use the help option for snapshot restore to see all available options for restoring the backup.

etcdctl snapshot restore -h`

# Restore backupt with ETCD as static pod (kubeadm)
Same process except you don't need to stop the kube-apiserver pod before doing the restore

after restoring to the new directory, edit the etcd.yaml manifest in /etc/kubernetes/manifests to change the etcd-data hostPath from the old path to the new one. This is the path it uses as its --data-dir