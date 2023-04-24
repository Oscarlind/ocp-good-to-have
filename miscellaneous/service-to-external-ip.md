# Create a service to resolve to a external IP
There might be a reason why one would like to be able to make use of an OCP service object in order to connect to an external service. In other words, to connect to something outside the actual cluster.

## Considerations
If you have multiple endpoints that you want to be able to reach, consider using a proper loadbalancer with a VIP as the endpoint.

In the examples below, the ip 10.1.0.97 is a loadbalancer running keepalived.

## How
Below are two different ways of solving this. Either using **endpoints** or using an **endpointslice**.

## Using Endpoints
We will create a service and a endpoint object.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: rgw-direct
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: v1
kind: Endpoints
metadata:
  name: rgw-direct
  namespace: s3
subsets:
  - addresses:
    - ip: 10.1.0.32
    ports:
    - port: 8080
  - addresses:
    - ip: 10.1.0.33
    ports:
    - port: 8080
  - addresses:
    - ip: 10.1.0.34
    ports:
    - port: 8080
  - addresses:
    - ip: 10.1.0.35
    ports:
    - port: 8080
  - addresses:
    - ip: 10.1.0.36
    ports:
    - port: 8080
```

## Using Endpointslices
Create a **service** object e.g.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: rgw-ingress
  namespace: s3
spec:
  clusterIP: 172.30.152.70
  clusterIPs:
  - 172.30.152.70
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  sessionAffinity: None
  type: ClusterIP
  ```

  Create a **endpointslice** object that maps to the service:

  ```yaml
  apiVersion: discovery.k8s.io/v1
kind: EndpointSlice
metadata:
  name: rgw-ingress
  labels:
    kubernetes.io/service-name: rgw-ingress
addressType: IPv4
ports:
  - name: ''
    appProtocol: http
    protocol: TCP
    port: 8090
endpoints:
  - addresses:
      - "10.1.0.97"
```
## Verification
Verify by connecting to a pod and accessing the service. E.g. from another namespace you can use curl on the service like this:

```sh
curl rgw-ingress.s3.svc.cluster.local`
```
Here **rgw-ingress** is the service name and **s3** is the namespace the service resides in.

> NOTE: This sends the data to this endpoint, but does not verify that the endpoint is listening.

