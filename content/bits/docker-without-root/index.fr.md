---
title: "Docker sans root"
publishDate: 2022-07-30
slug: docker-sans-root
tags:
  - docker

---

Créez le groupe `docker`.

```shell
sudo groupadd docker
```

Ajoutez l'utilisateur au groupe `docker`.

```shell
sudo usermod -aG docker $USER
```

Appliquez les changements au groupe.

```shell
newgrp docker
```

Vérifiez s'il est possible d'exécuter `docker`.

```shell
docker version
```

{{< tip class="warning" >}}
Il n'est pas recommandé de le faire avec des serveurs qui sont branchés a l'Internet, gardez les privilèges d'exécuter `docker` au `root`.
{{< /tip >}}
