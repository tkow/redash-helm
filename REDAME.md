# re:dash helm

## Clone

```bash
$ https://github.com/Himenon/redash-helm.git
```

## Usage

### From local

```bash
helm install -f redash-helm
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

# Author

- [Himenon](https://github.com/Himenon)

# TODO

- Switch SQL
    - Google Cloud SQL Type
    - Volume Type
