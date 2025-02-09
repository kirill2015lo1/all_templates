```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      labels:
        app: flask
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: node-role.kubernetes.io/control-plane
                    operator: DoesNotExist
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app: flask
                topologyKey: "kubernetes.io/hostname"
      initContainers:
        - name: wait-for-db
          image: busybox:1.35.0-uclibc
          command:
            - "sh"
            - "-c"
            - "until nc -z -v -w5 postgres 5432; do echo 'Waiting for PostgreSQL...'; sleep 1; done; echo 'PostgreSQL is up!'"
          resources:
            requests:
              memory: "64Mi"
              cpu: "50m"
      containers:
      - name: flask
        image: tsurankirill/webapp:latest
        env:
        - name: DATABASE_HOST
          value: postgres
        - name: DATABASE_PORT
          value: "5432"
        - name: DATABASE_NAME
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-db
        - name: DATABASE_USER
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-user
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
        ports:
          - containerPort: 5000
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        readinessProbe:
          failureThreshold: 2
          httpGet:
            path: /
            port: 5000
          periodSeconds: 5
          successThreshold: 1
          timeoutSeconds: 1
          initialDelaySeconds: 5
        livenessProbe:
          failureThreshold: 2
          httpGet:
            path: /
            port: 5000
          periodSeconds: 5
          successThreshold: 1
          timeoutSeconds: 1
          initialDelaySeconds: 5
```
