# ConfigMaps and Secrets

## Create a ConfigMap

```shell
kubectl create configmap redis-config --from-literal=key1=config1 --from-literal=key2=config2 --dry-run=client -o yaml > yaml-definitions/redis-config.yaml
```

## Replace the key-value Pairs and add a Redis Configuration

```shell
data:
  redis-config: |
    maxmemory 2mb
    maxmemory-policy allkeys-lru
```

## Create the ConfigMap

```shell
kubectl apply -f yaml-definitions/redis-config.yaml

configmap/redis-config created
```

## Validate the ConfigMap

```shell
kubectl describe configmap redis-config

Name:         redis-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
redis-config:
----
maxmemory 2mb
maxmemory-policy allkeys-lru


BinaryData
====

Events:  <none>
```

## Create a Redis Definition File

```shell
kubectl run redis-init --image=redis:7 --port 6379 --command 'redis-server' '/redis-master/redis.conf' --dry-run=client -o yaml > yaml-definitions/redis.yaml
```

### Add a Volume Mount to the Redis File

```shell
resources: {}
volumeMounts:
- mountPath: /redis-master-data
  name: data
- mountPath: /redis-master
  name: config
```

### Add Volumes Configuration

```shell
containers:
volumes:
  - name: data
    emptyDir: {}
  - name: config
    configMap:
      name: redis-config
      items:
      - key: redis-config
        path: redis.conf
```

### Apply the Redis Definition

```shell
kubectl apply -f yaml-definitions/redis.yaml

pod/redis-init created
```

### Validate Redis Pod

```shell
kubectl get pod

NAME         READY   STATUS    RESTARTS   AGE
redis-init   1/1     Running   0          38s
```

### Connect to Redis in the Pod

```shell
kubectl exec -it redis-init -- redis-cli
127.0.0.1:6379>
```

### Get maxmemory Value

```shell
127.0.0.1:6379> CONFIG GET maxmemory
1) "maxmemory"
2) "2097152"
127.0.0.1:6379>
```

### Get maxmemory-policy Value

```shell
127.0.0.1:6379> CONFIG GET maxmemory-policy
1) "maxmemory-policy"
2) "allkeys-lru"
127.0.0.1:6379>
```

### Delete Pod and ConfigMap

```shell
kubectl delete pod redis-init
pod "redis-init" deleted
```

```shell
kubectl delete cm redis-config
configmap "redis-config" deleted
```
