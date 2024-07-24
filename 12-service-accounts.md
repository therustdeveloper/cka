# Service Accounts

- `Service Accounts` are namespace-scoped resources.
- You'll find at least one in each namespace.

## Create a Namespace

```shell
kubectl create ns web
```

## List the Service Account

```shell
kubectl get sa -n web
```

This default `Service Account` is created automatically.

## Describe the Service Account

```shell
kubectl describe sa -n web
```

## Cat the Service Account Token

```shell
kubectl describe secret default-token-mv7at -n web
```

Technically, we can use this token (as a user) to authenticate with the Kubernetes API.