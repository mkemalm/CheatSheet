### Service Mesh Features
* Traffic management
    * Discovery and routing
    * Load-balancing
    * Failure handling
* Security
    * Encryption
    * Authentication
    * Authorization
* Observability
    * Visualisation
    * Tracing
    * Monitoring

### Before the beginning

* After Istio installed according to steps in istio.io, all Istio pods run in istio-system namespace.\
`kubectl get all -n istio-system`

* To enable Istio in a namespace;\
`kubectl label namespace namespace_name istio-injection=enabled`

* All pods of application contain also proxy containers. Two processes run at proxy containers; envoy and pilot agent. Envoy checks all the traffic, pilot agent checks if there is a new configuration.

### Components

![Topology](istio.png)

* Traffic between services goes through the proxies(sidecar proxy container: envoy)
* Pilot is responsible for syncing configurations to proxies
* Mixer collects telemetries and applies policies
* Citadel does certificate jobs(generating, renewing, encrypt-decrypt...etc.)

### Gateway

* Gateway is a resource type to define inbound and outbound points of applications. Example;

```
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
````

### Virtual Service

* Provides routing functions between services. 
* It is a resource type above services, is installed with Istio.
* Traffic splits, fault tolerance scenarios, retry mechanisms are done with virtual service definition. Example;

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
  - "*"
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        exact: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: productpage
        port:
          number: 9080
````

