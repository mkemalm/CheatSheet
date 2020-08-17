#### Adding fault tolerance

*  **Example;** Retries every 5 seconds and makes 2 attempts when HTTP response code starts with 5XX.

```
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
    retries:
      attempts: 2
      perTryTimeout: 5s
      retryOn: 5xx
```