# Helm Chart for Konga


## Konga installation in K8S

### Create the Postgres DB for your Konga

```
CREATE USER konga;
CREATE DATABASE konga OWNER konga;
ALTER USER konga WITH PASSWORD <your secret>;
```

### Prepare the values.yaml file

Update the values.yaml with these following information

```yaml
config: {}
  port: 1337
  node_env: <development or production>
  db_adapter: postgres
  db_host: <address of your postgres db host>
  db_port: 5432
  db_user: konga
  db_password: <postgres password>
  db_database: konga
```

and

```yaml
runMigrations: true
```

### Install Konga helm chart

Assuming you already installed KONG API GATEWAY in the kong namespace
```
helm install konga ./ -n kong -f values.yaml
```

### Expose Konga using ingress 

Prepare the ingress.yaml file as following
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: konga-ingress
  namespace: kong
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: konga.yourdomain.com
    http:
      paths:
      - path: "/"
        pathType: Prefix
        backend:
          service:
            name: konga
            port:
              number: 80
```

Run kubectl to create the ingress
```
kubectl apply -f ingress.yaml
```

Now you can access Konga at konga.yourdomain.com.

