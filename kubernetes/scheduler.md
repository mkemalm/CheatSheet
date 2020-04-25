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

## Affinity

* more enhanced than nodeSelector
* two types
	* node affinity
	* pod affinity
    * anti pod affinity

### Node Affinity

* like nodeSelector but allows you constain which nodes a Pod is eligible to be scheduled
* two types
    * ***requiredDuringSchedulingIgnoredDuringInstallation***, hard restriction, node should met all rules for pod
    * ***prefferedDuringSchedulingIgnoredDuringExecution***, prefers node that met rules best but not enforcing. a weight may be used to specify how hard it is.

```
pods/pod-with-node-affinity.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/e2e-az-name
            operator: In
            values:
            - e2e-az1
            - e2e-az2
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: with-node-affinity
    image: k8s.gcr.io/pause:2.0
```
	
### Pod Affinity

* Pod affinity compares labels in pods that need to be scheduled with labels of pods that are already running on a node. And pod anti-affinity is doing the opposite. 
* The rules are formulated as the pod should run in X if that X is already running one or more pods that meet rule Y. So Y is expressed as a LabelSelector with an optional list of namespaces. And X is a topology domain name like node, or rack or zone and so on, which is expressed by using a ***topologyKey*** which is the key for the node label that the system uses. 
* So this topology domain name allows you to create logical groups of nodes to manage where the pods are going to run. 
* Before starting to work with pod affinity, you should note that pod affinity negatively impacts performance in large clusters. 
* Using pod affinity requires all nodes in the clusters to be labels with a topologyKey

#### topologyKey

* In pod affinity, the topologyKey property plays an important role. 
* You will use the topologyKey to create logical groups, also known as zones. 
* Nodes, as well as pods, can be identified with a topologyKey. And a topologyKey can also be used as a unique node identifier. 
* topologyKeys are quite important and offer lots of possibilities. To explore the further possibilities, follow the link on the slide.

#### pod affinity example

```
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: failure-domain.beta.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: failure-domain.beta.kubernetes.io/zone
  containers:
  - name: with-pod-affinity
    image: k8s.gcr.io/pause:2.0
```


## Taints and Tolerations

* A taint is applied to a node to mark that the node should not accept any pod that doesn't tolerate the taint.
* A toleration is applied to a pod, and it allows but does not require pods to schedule or nodes with matching * things. So they are an exception to the taints that are applied. 
* Where affinities are used on pods to attract them to specific nodes, taints allow our pods to repel a set of nodes. 
* Taints and toleration are used to ensure pods are not scheduled on inappropriate nodes, and just make sure that the dedicated nodes can be configured for dedicated tasks. 
* Taints and tolerations have no effect daemonsets