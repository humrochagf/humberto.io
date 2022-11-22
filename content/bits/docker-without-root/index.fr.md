---
title: "Docker sans root"
publishDate: 2022-07-30
slug: docker-sans-root
tags:
  - docker

---

Créez le groupe `docker`.

```console
sudo groupadd docker
```

Ajoutez l'utilisateur au groupe `docker`.

```console
sudo usermod -aG docker $USER
```

Appliquez les changements au groupe.

```console
newgrp docker
```

Vérifiez s'il est possible d'exécuter `docker`.

```console
docker version
```

{{< tip class="warning" >}}
Il n'est pas recommandé de le faire avec des serveurs qui sont branchés a l'Internet, gardez les privilèges d'exécuter `docker` au `root`.
{{< /tip >}}
