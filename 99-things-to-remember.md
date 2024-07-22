# Things to remember

## Control Plane Container

Remember that the manifests for control plane components are in the `/etc/kubernetes/manifests/` directory. This directory will be located on the control plane server, which you will have to SSH into to view or modify.

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```