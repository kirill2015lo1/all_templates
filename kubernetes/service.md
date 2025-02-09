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
