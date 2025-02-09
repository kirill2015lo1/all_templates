```
apiVersion: v1
kind: Service
metadata:
  name: postgres
spec:
  selector:
    app: postgres
  ports:
    - protocol: TCP
      port: 5432
      targetPort: 5432
  type: ClusterIP
```
Или 
```
apiVersion: v1
kind: Service
metadata:
  name: flask
  labels:
    job: test
    app: flask
spec:
  selector:
    app: flask
  ports:
    - name: web
      protocol: TCP
      port: 80
      targetPort: 5000
```
Loadbalancer:
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service2
spec:
  selector:
    app: nginx2
  type: LoadBalancer
  loadBalancerIP: 192.168.50.137
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 80

```
