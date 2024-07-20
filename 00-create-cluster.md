# Kind Cluster

## Install kind in macOS

Install Kind in macOS using `brew`.

```shell
brew install kind
```

## Create a simple cluster with a configuration file

** This is the main cluster we are going to use.

```shell
kind create cluster --name cka --config cluster.yaml
```

## Create a HA Cluster

```shell
kind create cluster --name cka-ha --config ha-cluster-one.yaml
```

## Delete a Cluster

```shell
kind delete cluster --name cka
```

## Get the nodes

```shell
k get nodes                                                                                                                                                                                        ─╯
NAME                STATUS   ROLES           AGE   VERSION
cka-control-plane   Ready    control-plane   36s   v1.29.1
cka-worker          Ready    <none>          13s   v1.29.1
cka-worker2         Ready    <none>          18s   v1.29.1
```

## Connect to a Kind Docker Control-Plane Container

```shell
docker exec -it cka-control-plane bash                                                                                                                                                             ─╯
root@cka-control-plane:/#
```

### View running Pods in the kube-system namespace


```shell
root@cka-control-plane:/# kubectl get po -n kube-system
NAME                                        READY   STATUS    RESTARTS   AGE
coredns-7db6d8ff4d-c9z5d                    1/1     Running   0          2m21s
coredns-7db6d8ff4d-vdnj5                    1/1     Running   0          2m21s
etcd-cka-control-plane                      1/1     Running   0          2m37s
kindnet-mdjtd                               1/1     Running   0          2m19s
kindnet-n6dhh                               1/1     Running   0          2m21s
kindnet-r2r6m                               1/1     Running   0          2m19s
kube-apiserver-cka-control-plane            1/1     Running   0          2m37s
kube-controller-manager-cka-control-plane   1/1     Running   0          2m37s
kube-proxy-8gmzk                            1/1     Running   0          2m21s
kube-proxy-q5xbj                            1/1     Running   0          2m19s
kube-proxy-sr7f8                            1/1     Running   0          2m19s
kube-scheduler-cka-control-plane            1/1     Running   0          2m37s
```

## Connect to a Kind Docker Worker Container

```shell
docker exec -it cka-worker bash                                                                                                                                                                    ─╯
root@cka-worker:/#
```
