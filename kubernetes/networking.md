### Kubernetes Networking

* Overlay networking provides pod-to-pod networking

**CNI;** Container Network Interface \
**CSI;** Container Storage Interface \
**CRI;** Container Runtime Interface \

#### CNI

* CNI is a common plugin that is used when starting the kubelet on a worker node. 
* The CNI does not take care of Pod-to-Pod networking. Because that is done by the actual network plugin. 
* The CNI ensures the pluggable nature of networking, and make it easy to select between different network plugins such as Linux-bridge, Weave, Flannel and so on.
* Use of the CNI is common but it's not standard. If it's used the day-to-days network plugin with CNI argument has been used when starting the kubelet. 
* Network parameters can also be passed with other options while starting the kubelets. And that's what is happening by default on minikube, for example. 
* Other networking solutions exist as well, such as the relatively simple kubenet interface
* if CNI is used, you can find the plugin configuration in etc/cni/net.d. Some plugins have the complete network setup in this directory. 
* Other plugins only have generic settings, and are using additional configuration. 
* Very frequently, the additional network configuration is implemented by Pods. So if at anytime you're looking at what exactly is happening in the Kubernetes   networking, do have a look at the Kubernetes pods in the kube system, name space as well. Because that might show you some additional information that you need.
* If you're interested, the generic CNI documentation is on github, in containernetworking/cni