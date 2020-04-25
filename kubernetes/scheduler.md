* kube-scheduler responsible decide which pod will run on which node.

## Scheduler Policies

* kube-scheduler works with filtering and scoring to determine where to run a Pod
* In filtering a set of notes is found where it is feasible to schedule the Pod, because the node meets the required resources.
* In scoring, the scheduler ranks all notes remaining after filtering to choose the most suitable Pod placement.

### Some filters

* `PodFitsHostPorts`, which checks if free network ports are available
* `PodFitsResources` which checks if the node has sufficient CPU and Memory resources.  -PodMatchNodeSelector, which checks if the Node Selector matches the Node labels. -CheckNodeDIskPressure, which checks if node is reporting a filesystem that's almost full, so that the Pod won't be scheduled there. 
* `CheckVolumeBinding`, which evaluates if the volumes that are requested can be serviced by the node using bound and unbound PVCs. 
* There is more, for a complete overview, I advise you to have a look at the documentation.

So after filtering out node, scoring is used to evaluate the remaining nodes.

### Scoring

* `SelecorSpreadPriority` which spreads Pods across hosts, considering Pods that belong to the same service, StatefulSet or ReplicaSet. 
* `LeastRequestedPriority`, which prioritizes nodes with fewer requested resources.
* `NodeAffinityPriority`, which prioritizes nodes according to the node affinity Scheduling preferences and more scoring factors are considered

## Node selector

* custom labels can be used.
* nodeName can be used as selector, but not recommended. If node doesn't live, can cause problem

example
`kubectl label nodes node_name disktype=ssd`

pod

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```

## affinity

* more enhanced than nodeSelector
* two types
	* node affinity
	* inter pod affinity
	
