inress с tls подключенным через letsencrypt для домена testsmth.ru
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-nginx3
  annotations:
    #nginx.ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: letsencrypt-issuer
spec:
  ingressClassName: nginx
  rules:
  - host: testsmth.ru 
    http:
      paths:
      - path: /katalog
        pathType: Prefix
        backend:
          service:
            name: nginx-service3
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: flask
            port:
              number: 80
  tls:
  - hosts: 
    - testsmth.ru
    secretName: letsencrypt-issuer-key-secret1
```
