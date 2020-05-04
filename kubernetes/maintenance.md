#### Adding nodes

* if a node can't be reached for five minutes it is scheduled for deletion and pods on it are migration
* `kubeadm init` guides adding node steps during installing cluster. Adding node later cluster installation is different.
* Here are the steps
    * Create token
    TOKEN=`kubeadm token create`

    * Generate a cert-hash
    CERT-HASH=`openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'`

    * Run join node in new node
    `kubeadm join --token $TOKEN master_ip:port --discovery-token-ca-cert-hash sha256:$CERT-HASH`

#### Rebooting a node

* You should make shure no new Pods will be started on the node
  `kubectl cordon $NODENAME`

* Evict Pods on the node.
  `kubectl drain $NODENAME`

* Reboot node. After reboot kubelet takes care of self-registration

#### Removing a node

* `kubectl drain $NODENAME --delete-local-data --force --ignore-daemonsets`
* `kubectl delete node $NODENAME`

#### Drain and Cordon

* `kubectl drain` gracefully terminate all pods on a node and mark node as unschedulable
* `kubectl drain --force` termimnates all pods
* `kubectl cordon` marks node unschedulable but keep pods running on it
* `kubectl uncordon` undo cordon

#### Static Nodes and Pods

* Static node is a node which runs specific pod allways. These pods only run at this node when node or kubelet is restarted. To define a static pod; pod's yaml should be located under static pod path which is defined in `/var/lib/kubelet/config.yaml` and kubelet should be restarted.

#### Managing etcd Database

* etcd backup

`ETCDCTL_API=3 etcdctl snapshot save mysnapshot.db --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key`

* To verify backup

`ETCDCTL_API=3 etcdctl --write out=table snapshot status mysnapshot.db`

* restore 

`ETCDCTL_API=3 etcdctl snapshot restore mysnapshot.db --name ip-172-31-27-180 --initial-cluster ip-172-31-27-180=https://172.31.27.180:2380 --initial-advertise-peer-urls https://172.31.27.180:2380 10`

* etcd commands aren't installed with kubernetes commands

`yum install etcd -y`

* `ps -ef | grep etcd` helps about certificates parameteres when using etcd commands

#### Monitoring and Logging

* To provide monitoring metrics server should be installed
* After installation with `kubectl top`command resource usage can be displayed

`kubectl top pods`
`kubectl top nodes`
...

* Logs

`kubectl logs pod_name`

* Pod states

**Pending,** pod is created in etcd but not started \
**Running,** pod is at healthy state \
**Failed,** one or more containers at pod failed and pod will not start \
**Unknown,** state of pod can't be get, mostly about network issues \
**Succeeded,** pod did it's job \
**Completed,** pod has run to completion \
**CrashLoopBackOff,** one or more container generated error but scheduler is still trying to run it

