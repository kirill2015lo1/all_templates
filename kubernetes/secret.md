```
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
type: Opaque
data:
  postgres-user: dXNlcg==          # 'user' в base64
  postgres-password: cGFzc3dvcmQ=   # 'password' в base64
  postgres-db: bXlkYXRhYmFzZQ==    # 'mydatabase' в base64
```
