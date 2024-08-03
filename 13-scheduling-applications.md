# Scheduling Applications

## Create a Pod YAML Definition

```shell
kubectl run ssd-pod --image=nginx --port=80 --dry-run=client -o yaml > yaml-definitions/ssd-pod.yaml
```

## Get Nodes Labels

```shell
kubectl get nodes --show-labels

NAME                STATUS   ROLES           AGE     VERSION   LABELS
cka-control-plane   Ready    control-plane   3m50s   v1.29.0   beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=cka-control-plane,kubernetes.io/os=linux,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=
cka-worker          Ready    <none>          3m27s   v1.29.0   beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=cka-worker,kubernetes.io/os=linux
cka-worker2         Ready    <none>          3m28s   v1.29.0   beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=cka-worker2,kubernetes.io/os=linux
```
## Apply a Label to a Node

The `cka-worker` node is the first of the worker nodes. There are three nodes:

- cka-control-plan
- cka-worker
- cka-worker2

```shell
kubectl label no cka-worker disktype=ssd
node/cka-worker labeled
```

### Check the labels in the node

```shell
kubectl get no cka-worker --show-labels

NAME         STATUS   ROLES    AGE     VERSION   LABELS
cka-worker   Ready    <none>   7m12s   v1.29.0   beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,disktype=ssd,kubernetes.io/arch=arm64,kubernetes.io/hostname=cka-worker,kubernetes.io/os=linux
```

### Add a nodeSelector to the Pod YAML Definition (ssd-pod.yaml)

Add the line `nodeSelector:` in line with word `containers:`

```shell
nodeSelector:
  disktype: ssd
```

### Create the Pod

```shell
kubectl apply -f yaml-definitions/ssd-pod.yaml
pod/ssd-pod created
```

### Verify Pod Location

```shell
kubectl get pod ssd-pod -o wide

NAME      READY   STATUS    RESTARTS   AGE    IP           NODE         NOMINATED NODE   READINESS GATES
ssd-pod   1/1     Running   0          112s   10.244.2.2   cka-worker   <none>           <none>
```
