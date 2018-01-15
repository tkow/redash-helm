# re:dash helm

## Clone

```bash
$ https://github.com/Himenon/redash-helm.git
```

## Usage

### From local

#### Postgres

```
$ kubectl apply -f pv.yaml

$ helm install -f psql-values.yaml --name redash stable/postgresql

# Make Password Secret
$ PGPASSWORD=$(kubectl get secret --namespace redash redash-postgresql -o jsonpath="{.data.postgres-password}" | base64 --decode; echo)
$ kubectl create secret generic redash-db --from-literal=password=$PGPASSWORD

# Check Service Port
$ kubectl get svc
```

### Install redash

```bash
$ helm install --name my .

$ ./bin/docker-entrypoint create_db
```

## Uninstall

```bash
$ helm del --purge my
```


```bash
$ helm install ./redash-helm

# Go to servcer container
$ ./bin/docker-entrypoint create_db
```

# Start redash on Minikube

## Set context to 'minikube'

```bash
# Start Minikube
$ minikube start
Starting local Kubernetes v1.8.0 cluster...
Starting VM...

# Check Current Context
$ kubectl config current-context
minikube
```

### Change the context manually

```bash
# Show context list
$ kubectl config get-contexts
...

# Switch context
$ kubectl config set-context minikube
Context "minikube" modified.
```

## Create Volume

- https://kubernetes.io/docs/concepts/storage/persistent-volumes/

## Expose from NodePort

```$xslt
$ kubectl expose deployment redash --type=NodePort --port=80 --target-port=5000

$ minikube service list
```

- https://github.com/kubernetes/minikube/blob/master/README.md

# Reference

- https://github.com/kubernetes/charts/tree/master/stable/postgresql

# Author

- [Himenon](https://github.com/Himenon)

# TODO

- Switch SQL
    - Google Cloud SQL Type
    - Volume Type
