Пример CM в котором файл с конфигом для nginx, который мы передадим внутрь подов, чтобы изменить стартовую веб страницу.  

CM:
```
kind: ConfigMap
apiVersion: v1
metadata:
  name: my-cm
data:
  default.conf: |
    server {
        listen 80 default_server;
        server_name _;

        charset utf-8;

        location / {
            default_type text/html;
            return 200 '
                <html>
                    <head><title>Success</title></head>
                    <body style="text-align: center; font-family: Arial, sans-serif; padding-top: 15%; font-size: 24px;">
                        <p>host: <strong>$host</strong></p>
                        <p><strong>У тебя все получилось, соединение защищено tls</strong></p>
                        <p><strong>Это под с nginx, в который добавлен configmap с этим текстом</strong></p>
                        <p><img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExeG9zYmVmem5kOW40M3VpNHF5MHNiaHQyMmFseXcxbGpuMWtiMGs3cyZlcD12MV9naWZzX3NlYXJjaCZjdD1n/l41K3o5TzvmhZwd4A/giphy.gif" alt="dancing" width="300"></p>
                    </body>
                </html>
            ';
        }
    }
```
Пример монтирования внутрь deployment:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name:  nginx3
  namespace: default
  labels:
    app:  nginx3
spec:
  selector:
    matchLabels:
      app: nginx3
  replicas: 1
  template:
    metadata:
      labels:
        app:  nginx3
    spec:
      containers:
      - name:  nginx3
        image:  nginx
        ports:
        - containerPort:  80
        volumeMounts:
          - name:  config
            mountPath:  /etc/nginx/conf.d/
      volumes:
        - name:  config
          configMap:
            name: my-cm
```
