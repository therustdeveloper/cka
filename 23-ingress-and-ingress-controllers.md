# Ingress & Ingress Controllers

- To perform application layer (layer-7) routing to your application in Kubernetes, the term that's used is `Ingress`.
- The reason it's a preferred method is that Ingress provides a single gateway (only one entry) into the cluster and can route traffic to multiple `Services` using simple path-based routes.
- Along with an `Ingress Controller`, you can set these routes in the `Ingress` resource, and it will be directed to each `Service`.

## Install Ingress Controller

```shell
kubectl apply -f https://raw.githubusercontent.com/chadmcrowell/acing-the-cka-exam/main/ch_06/nginx-ingress-controller.yaml

namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
```

## Validate the Resources

```shell
kubectl get all -n ingress-nginx

NAME                                            READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-lckrh        0/1     Completed   0          40s
pod/ingress-nginx-admission-patch-kls4d         0/1     Completed   2          40s
pod/ingress-nginx-controller-54877c6476-5ktr5   1/1     Running     0          40s

NAME                                         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.96.75.167   <none>        80:32205/TCP,443:32034/TCP   40s
service/ingress-nginx-controller-admission   ClusterIP   10.96.37.139   <none>        443/TCP                      40s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           40s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-54877c6476   1         1         1       40s

NAME                                       COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   1/1           10s        40s
job.batch/ingress-nginx-admission-patch    1/1           23s        40s
```

## Create a Deployment

```shell
kubectl create deploy hello --image=nginxdemos/hello:plain-text --port=80
deployment.apps/hello created
```

## Create a Service for the Deployment

```shell
kubectl expose deploy hello --name hello-svc --port 80
service/hello-svc exposed
```

## Validate the Resources

```shell
kubectl get svc,deploy

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/hello-svc    ClusterIP   10.96.76.212   <none>        80/TCP    52s
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   5m38s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello   1/1     1            1           112s
```

## Create an Ingress Resource

```shell
kubectl apply -f https://raw.githubusercontent.com/chadmcrowell/acing-the-cka-exam/main/ch_06/hello-ingress.yaml
ingress.networking.k8s.io/hello created
```

## Validate the Ingress

```shell
kubectl get ingress

NAME    CLASS    HOSTS       ADDRESS     PORTS   AGE
hello   <none>   hello.com   localhost   80      44s
```