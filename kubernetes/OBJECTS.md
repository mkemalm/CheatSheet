## Namespaces

* Linux kernel feature leveraged up to kubernetes.
* Provides resource isolation and restriction among multiple users.
* When cluster is created there are some default namespaces;
	* default: All resources are created in default namespace if it is not specified
	* kube-public: global information or configurations
	* kube-node-lease: node lease info, may be empty or not created
	* kube-system: infrastructure pods
* some commands
	``` 
      kubectl get ns
	  kubectl get ns --all-namepsaces
	  kubectl create ns XXX
	  kubectl describe ns XXX
	  kubectl get ns XXX -o yaml
    ```
## Deployments

* replica sets are created after deployments and are responsible for scale of pods.
* `kubernetes get all` --> shows pods, deployments, services, replicasets
* `kubectl create deployment --dry-run --image=nginx --output=yaml XXX` --> creates a deployment yaml example
* `kubectl scale deployment --replicas=X` --> scales out deployment to 3 copies
* `kubectl edit deployment.apps XXXX` --> edit in vi mode
* `kubectl get deployments --show-labels`
* `kubectl label deployment.apps XXXX label=value`
* `kubectl get deployment.apps --selector label=value`
* `kubectl get all --selector label=value` --> gets all objects about this label

Get update strategy of deployment;

* `kubectl explain deployments.spec.strategy`
	* rolling update; there are two versions of app
	* recreate; deployment is created again
	
* `kubectl rolout history deployment` --> shows rollout history of deployments
* `kubectl --record deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.16.1` --> records detailed history

## Init Containers

* initContainers spec is used.
* if initContainers spec is met containers part is executed in yaml.

ü
```
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
  labels: 
    app: init-demo
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ['sh', '-c', 'sleep 3600']
  initContainers:
  - name: init-container
    image: busybox
    command: ['sh', '-c', 'sleep 30']
```

## Statefulsets

* smilar to deployments but quarentees uniqueness of pods
* use case
	* unique network identifiers
	* stable persistent storage
	* ordered deployment and scaling
	* ordered automated rolling updates
	* storage must be provisioned by persistentvolume

## Daemonset

* ensures all nodes are running a copy of pod
* use cases
	* running storage pods that should run in every node such as ceph
	* log collection daemons 
	* monitoring daemons
	
