# Troubleshooting Kubernetes

## Understanding Application Logs

- `Container engines` like `containerd` are designed to support logging and usually write all their output to standard output (`stdout`) and standard error (`stderr`) streams to a file located in the directory `/var/log/containers`.

## Create a Single Node Cluster

Follow the instructions on [Create Cluster](00-create-cluster.md#create-a-single-node) document to `Create a Single Node`.

## Exam Task

### Create a Namespace

```shell
k create ns db08328
namespace/db08328 created
```

### List the Namespaces

```shell
k get ns
NAME                 STATUS   AGE
db08328              Active   23s
default              Active   33s
kube-node-lease      Active   33s
kube-public          Active   33s
kube-system          Active   33s
local-path-storage   Active   30s
```

### Change the context to the namespace db08328

```shell
k config set-context --current --namespace db08328
Context "kind-cka" modified.
```

### Create a Deployment

```shell
k create deploy mysql --image mysql:8
deployment.apps/mysql created
```

### List the Pods

```shell
k get pods
NAME                     READY   STATUS   RESTARTS      AGE
mysql-56787b9bcf-86zrc   0/1     Error    2 (22s ago)   31s

k get pods
NAME                     READY   STATUS             RESTARTS      AGE
mysql-56787b9bcf-86zrc   0/1     CrashLoopBackOff   3 (36s ago)   86s
```

#### Possible Statuses of a Pod

- CrashLoopBackOff
- OOMKilled
- ErrImagePull
- ImagePullBackoff
- FailedScheduling
- NonZeroExitCode
- CreateContainerConfigError

### Inspect the logs of the Pod

```shell
k logs mysql-56787b9bcf-86zrc
2024-09-24 13:08:45+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.2-1.el9 started.
2024-09-24 13:08:45+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2024-09-24 13:08:45+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.4.2-1.el9 started.
2024-09-24 13:08:45+00:00 [ERROR] [Entrypoint]: Database is uninitialized and password option is not specified
    You need to specify one of the following as an environment variable:
    - MYSQL_ROOT_PASSWORD
    - MYSQL_ALLOW_EMPTY_PASSWORD
    - MYSQL_RANDOM_ROOT_PASSWORD
```

This log is telling us that we need to set the password using one of the environment variables.

### Edit the Deployment

```shell
k edit deploy mysql
deployment.apps/mysql edited
```

Add the following environment variable in the container:

```yaml
containers:
- env:
  - name: MYSQL_ROOT_PASSWORD
    value: password
```

### List the Pods

```shell
k get pod
NAME                     READY   STATUS    RESTARTS   AGE
mysql-756d456979-cwmqv   1/1     Running   0          42s
```

## Create a Multi Container Pod

```shell
k apply -f yaml-definitions/multi-container-pod-for-logging.yaml
pod/busybox created
```

### List the Pod

```shell
k get pod
NAME                     READY   STATUS    RESTARTS   AGE
busybox                  2/2     Running   0          13s
mysql-756d456979-cwmqv   1/1     Running   0          41m
```

As you can see the `busybox` Pod has two containers (`READY 2/2`).

### View the container logs

The pod has two containers called `busybox` and `sidecar`.

#### Logs of the busybox container

```shell
k logs busybox -c busybox
Tue Sep 24 13:56:40 UTC 2024: I am a busybox container
Tue Sep 24 13:56:45 UTC 2024: I am a busybox container
Tue Sep 24 13:56:50 UTC 2024: I am a busybox container
Tue Sep 24 13:56:55 UTC 2024: I am a busybox container
Tue Sep 24 13:57:00 UTC 2024: I am a busybox container
Tue Sep 24 13:57:05 UTC 2024: I am a busybox container
Tue Sep 24 13:57:10 UTC 2024: I am a busybox container
```

#### Logs of the sidecar container

```shell
k logs busybox -c sidecar
Tue Sep 24 13:56:41 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:46 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:51 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:56 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:01 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:06 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:11 UTC 2024: I am a sidecar container
```

#### Logs of all the containers in the pod

```shell
k logs busybox --all-containers
Tue Sep 24 13:56:41 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:46 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:51 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:56 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:01 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:06 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:11 UTC 2024: I am a sidecar container
Tue Sep 24 13:57:16 UTC 2024: I am a sidecar container
Tue Sep 24 13:56:40 UTC 2024: I am a busybox container
Tue Sep 24 13:56:45 UTC 2024: I am a busybox container
Tue Sep 24 13:56:50 UTC 2024: I am a busybox container
Tue Sep 24 13:56:55 UTC 2024: I am a busybox container
Tue Sep 24 13:57:00 UTC 2024: I am a busybox container
Tue Sep 24 13:57:05 UTC 2024: I am a busybox container
Tue Sep 24 13:57:10 UTC 2024: I am a busybox container
```

#### Streaming the logs

```shell
k logs busybox --all-containers -f
```

Press `Control-C` to get the prompt back.tr