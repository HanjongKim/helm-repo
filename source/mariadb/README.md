# MariaDB

[MariaDB](https://mariadb.org) is one of the most popular database servers in the world. It’s made by the original developers of MySQL and guaranteed to stay open source. Notable users include Wikipedia, Facebook and Google.

MariaDB is developed as open source software and as a relational database it provides an SQL interface for accessing data. The latest versions of MariaDB also include GIS and JSON features.

## TL;DR;

```bash
$ helm install stable/mariadb
```

## Introduction

This chart bootstraps a [MariaDB](https://github.com/bitnami/bitnami-docker-mariadb) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.6+ with Beta APIs enabled
- PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release stable/mariadb
```

The command deploys MariaDB on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following tables lists the configurable parameters of the MariaDB chart and their default values.

<table>
<thead>
<tr>
<th>Parameter</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>image</code></td>
<td>MariaDB image</td>
<td><code>bitnami/mariadb:{VERSION}</code></td>
</tr>
<tr>
<td><code>service.type</code></td>
<td>Kubernetes service type to expose</td>
<td><code>ClusterIP</code></td>
</tr>
<tr>
<td><code>service.nodePort</code></td>
<td>Port to bind to for NodePort service type</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>service.annotations</code></td>
<td>Additional annotations to add to service</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>imagePullPolicy</code></td>
<td>Image pull policy.</td>
<td><code>IfNotPresent</code></td>
</tr>
<tr>
<td><code>usePassword</code></td>
<td>Enable password authentication</td>
<td><code>true</code></td>
</tr>
<tr>
<td><code>mariadbRootPassword</code></td>
<td>Password for the <code>root</code> user.</td>
<td>Randomly generated</td>
</tr>
<tr>
<td><code>mariadbUser</code></td>
<td>Username of new user to create.</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>mariadbPassword</code></td>
<td>Password for the new user.</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>mariadbDatabase</code></td>
<td>Name for new database to create.</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>persistence.enabled</code></td>
<td>Use a PVC to persist data</td>
<td><code>true</code></td>
</tr>
<tr>
<td><code>persistence.existingClaim</code></td>
<td>Use an existing PVC</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>persistence.storageClass</code></td>
<td>Storage class of backing PVC</td>
<td><code>nil</code> (uses alpha storage class annotation)</td>
</tr>
<tr>
<td><code>persistence.accessMode</code></td>
<td>Use volume as ReadOnly or ReadWrite</td>
<td><code>ReadWriteOnce</code></td>
</tr>
<tr>
<td><code>persistence.size</code></td>
<td>Size of data volume</td>
<td><code>8Gi</code></td>
</tr>
<tr>
<td><code>resources</code></td>
<td>CPU/Memory resource requests/limits</td>
<td>Memory: <code>256Mi</code>, CPU: <code>250m</code></td>
</tr>
<tr>
<td><code>config</code></td>
<td>Multi-line string for my.cnf configuration</td>
<td><code>nil</code></td>
</tr>
<tr>
<td><code>metrics.enabled</code></td>
<td>Start a side-car prometheus exporter</td>
<td><code>false</code></td>
</tr>
<tr>
<td><code>metrics.image</code></td>
<td>Exporter image</td>
<td><code>prom/mysqld-exporter</code></td>
</tr>
<tr>
<td><code>metrics.imageTag</code></td>
<td>Exporter image</td>
<td><code>v0.10.0</code></td>
</tr>
<tr>
<td><code>metrics.imagePullPolicy</code></td>
<td>Exporter image pull policy</td>
<td><code>IfNotPresent</code></td>
</tr>
<tr>
<td><code>metrics.resources</code></td>
<td>Exporter resource requests/limit</td>
<td><code>nil</code></td>
</tr></tbody></table>

The above parameters map to the env variables defined in [bitnami/mariadb](http://github.com/bitnami/bitnami-docker-mariadb). For more information please refer to the [bitnami/mariadb](http://github.com/bitnami/bitnami-docker-mariadb) image documentation.

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
  --set mariadbRootPassword=secretpassword,mariadbUser=my-user,mariadbPassword=my-password,mariadbDatabase=my-database \
    stable/mariadb
```

The above command sets the MariaDB `root` account password to `secretpassword`. Additionally it creates a standard database user named `my-user`, with the password `my-password`, who has access to a database named `my-database`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml stable/mariadb
```

> **Tip**: You can use the default [values.yaml](values.yaml)

### Custom my.cnf configuration

The Bitnami MariaDB image allows you to provide a custom `my.cnf` file for configuring MariaDB.
This Chart uses the `config` value to mount a custom `my.cnf` using a [ConfigMap](http://kubernetes.io/docs/user-guide/configmap/).
You can configure this by creating a YAML file that defines the `config` property as a multi-line string in the format of a `my.cnf` file.
For example:

```bash
cat > mariadb-values.yaml <<EOF
config: |-
  [mysqld]
  max_allowed_packet = 64M
  sql_mode=STRICT_ALL_TABLES
  ft_stopword_file=/etc/mysql/stopwords.txt
  ft_min_word_len=3
  ft_boolean_syntax=' |-><()~*:""&^'
  innodb_buffer_pool_size=2G
EOF

helm install --name my-release -f mariadb-values.yaml stable/mariadb
```

## Consuming credentials

To connect to your database in your application, you can consume the credentials from the secret. For example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
    - name: my-app
      image: bitnami/mariadb:latest
      env:
        - name: MARIADB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-release-mariadb
              key: mariadb-root-password
      command: ["sh", "-c"]
      args:
      - mysql -h my-release-mariadb.default.svc.cluster.local -p$MARIADB_ROOT_PASSWORD -e 'show databases;'
  restartPolicy: Never

```

## Persistence

The [Bitnami MariaDB](https://github.com/bitnami/bitnami-docker-mariadb) image stores the MariaDB data and configurations at the `/bitnami/mariadb` path of the container.

The chart mounts a [Persistent Volume](http://kubernetes.io/docs/user-guide/persistent-volumes/) volume at this location. The volume is created using dynamic volume provisioning, by default. An existing PersistentVolumeClaim can be defined.

### Existing PersistentVolumeClaims

1. Create the PersistentVolume
1. Create the PersistentVolumeClaim
1. Install the chart
```bash
$ helm install --set persistence.existingClaim=PVC_NAME postgresql
```

## Metrics

The chart can optionally start a metrics exporter endpoint on port `9104` for [prometheus](https://prometheus.io). The data exposed by the endpoint is intended to be consumed by a prometheus chart deployed within the cluster and as such the endpoint is not exposed outside the cluster.
