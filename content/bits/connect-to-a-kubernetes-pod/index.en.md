---
title: "Connect to a Kubernetes Pod"
publishDate: 2022-12-07
tags:
  - k8s
  - kubernetes
  - bash
  - shell

---

To open a session it's first required to know the Pod's name.

```console
kubectl get pod -n <namespace>
```

Then open a bash session by running:

```console
kubectl exec --stdin --tty <pod name> -n <namespace> -- /bin/bash
```

It's also possible to open a shell session:

```console
kubectl exec --stdin --tty <pod name> -n <namespace> -- /bin/sh
```

Sometimes it's need to spawn a new Pod from an image that has the required tooling to inspect something.

```console
kubectl run -i --tty <pod name> --image=<image> -- /bin/bash
```

At the end, don't forget to delete the created Pod.

```console
kubectl delete pod/<pod name> -n <namespace>
```
