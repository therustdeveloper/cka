# Things to remember

## Control Plane Container

Remember that the manifests for control plane components are in the `/etc/kubernetes/manifests/` directory. This directory will be located on the control plane server, which you will have to SSH into to view or modify.

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

## Validate Command Syntax

- To validate what values go where in YAML file during the exam, you can use `kubectl explain`.
- The command `kubectl explain deploy.spec.strategy` will list values that are available for input in the spec field.