```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  serviceName: "postgres"
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
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
                    app: postgres
                topologyKey: "kubernetes.io/hostname"
      containers:
      - name: postgres
        image: postgres
        env:
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-user
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-password
        - name: POSTGRES_DB
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: postgres-db
        - name: PGDATA
          value: /var/lib/postgresql/data/pgdata
        ports:
          - containerPort: 5432
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        volumeMounts:
          - name: postgres-storage
            mountPath: /var/lib/postgresql/data
        readinessProbe:
          tcpSocket:
            port: 5432
          failureThreshold: 2
          periodSeconds: 5
          successThreshold: 1
          timeoutSeconds: 1
          initialDelaySeconds: 5
        livenessProbe:
          tcpSocket:
            port: 5432
          failureThreshold: 2
          periodSeconds: 5
          successThreshold: 1
          timeoutSeconds: 1
          initialDelaySeconds: 5
      volumes:
        - name: postgres-storage
          persistentVolumeClaim:
            claimName: postgresql-pvc
```
