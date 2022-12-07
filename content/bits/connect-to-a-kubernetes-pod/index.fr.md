---
title: "Connecter dans un Pod Kubernetes"
publishDate: 2022-12-07
slug: connecter-dans-un-pod-kubernetes
tags:
  - k8s
  - kubernetes
  - bash
  - shell

---

Pour ouvrir une session il faut d'abord connaître le nom du Pod.

```console
kubectl get pod -n <namespace>
```

Ensuite, ouvrez une session bash:

```console
kubectl exec --stdin --tty <nom du pod> -n <namespace> -- /bin/bash
```

Il est aussi possible d'ouvrir une session shell:

```console
kubectl exec --stdin --tty <nom du pod> -n <namespace> -- /bin/sh
```

Quelques fois, il est nécessaire de démarrer un nouveau Pod à partir d'une image ayant les outils nécessaires pour faire des inspections.

```console
kubectl run -i --tty <nom du pod> --image=<image> -- /bin/bash
```

A la fin, n'oubliez pas de supprimer le Pod crée.

```console
kubectl delete pod/<nom du pod> -n <namespace>
```
