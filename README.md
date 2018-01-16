# re:dash helm

## Usage

The following two items are explained.

- GKE + Google Cloud SQL
- GKE + Persistent Volume

These two differences is db of redash (postgres) which host on GC SQL or PV.

### 1. GKE + Google Cloud SQL (postgres)

- Google Cloud SQL: re:dash Database

#### 1-1. Setup Cloud SQL and Service Account

- https://cloud.google.com/sql/docs/

#### 1-2. helm install gcloud-cloudsql

```yaml
# cloudsql-values
image: "gcr.io/cloudsql-docker/gce-proxy"
imageTag: "1.11"
serviceAccountKey: [SA_KEY]
cloudsql:
  instance: [INSTANCE_CONNECTION_NAME]
  port: [DB_PORT]
```

```bash
$ helm install -f cloudsql-values.yaml --name=cloudproxy stable/gcloud-sqlproxy
```

Links

- https://github.com/kubernetes/charts/tree/master/stable/gcloud-sqlproxy

#### 1-3. helm install redash (this repository)

```bash
$ git clone https://github.com/Himenon/redash-helm.git && cd ./redash-helm
```

```yaml
# redash-values.yaml
db:
  name: [DB_NAME]
  user: [DB_USER]
  pass: [DB_PASS]
  host: [DB_HOST]
service:
  type: [SERVICE_TYPE]
```

```
$ helm install -f redash-values.yaml redash-server .
```

**Parameter**

- `[DB_NAME]`: redash using database name
- `[DB_USER]`: redash using database user name
- `[DB_PASS]`: Plain Text
    - However, [template/\_helpers.tpl](/Himenon/redash-helm/blob/master/templates/_helpers.tpl) change base64 text.
    - And use only above value in [template/secret.yaml](/Himenon/redash-helm/blob/master/templates/secret.yaml)
- `[DB_HOST]`: Kubernetes Service Name
    - **Cloud SQL**: Cloud SQL Service Name (Look 1-2).
    - **Persistent Volume**: Postgres Service Name (Look 2-3).
- `[SERVICE_TYPE]`: Kubernetes Service Type
    - https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services---service-types


### 2. GKE + Persistent Volume (postgres)

- `[pv name]`: Persistent Volume name
- `[pv size]`: Persitent Volume size
- `[storage class]`: Storage Class Name 

#### 2-1. Make Persistent Volum on GCE

```bash
$ gcloud compute disks create [pv name] --size=[pv size] --type pd-standard
```

#### 2-2. Manage PV with kubernetes

```yaml
# pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: psql-pv
  annotations:
    pv.beta.kubernetes.io/gid: "0"
spec:
  capacity:
    storage: [pv size]Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: [storage class]
  gcePersistentDisk:
    pdName: [pv name]
    fsType: ext4
```

```bash
$ kubectl apply -f pv.yaml
```

Links

- https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volumes

#### 2-3. helm install postgres

```yaml
# psql-values.yaml
imageTag: "9.5.6-alpine"
persistence:
  enabled: true
  storageClass: [storage class]
service:
  type: ClusterIP
  port: 5432
```

```bash
$ helm install -f psql-values.yaml --name redash-db stable/postgresql
```

Links

- https://github.com/kubernetes/charts/tree/master/stable/postgresql

#### 2-4. helm install redash (this repository)

Same: 1-3

# Author

- [Himenon](https://github.com/Himenon)


# TODO

- networkpolicy
- NOTES



