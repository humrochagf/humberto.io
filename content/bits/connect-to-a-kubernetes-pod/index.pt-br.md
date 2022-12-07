---
title: "Conectar a um Pod Kubernetes"
publishDate: 2022-12-07
slug: conectar-a-um-pod-kubernetes
tags:
  - k8s
  - kubernetes
  - bash
  - shell

---

Para abrir uma sessão é preciso saber o nome do Pod.

```console
kubectl get pod -n <namespace>
```

Em seguida abra uma sessão bash:

```console
kubectl exec --stdin --tty <nome do pod> -n <namespace> -- /bin/bash
```

Também é possível abrir uma sessão shell:

```console
kubectl exec --stdin --tty <nome do pod> -n <namespace> -- /bin/sh
```

Algumas vezes é preciso rodar um novo Pod a partir de uma imagem com as ferramentas necessárias para inspecionar algo.

```console
kubectl run -i --tty <nome do pod> --image=<image> -- /bin/bash
```

No final, não se esqueça de remover o Pod criado.

```console
kubectl delete pod/<nome do pod> -n <namespace>
```
