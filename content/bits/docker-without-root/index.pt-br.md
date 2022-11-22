---
title: "Docker sem root"
publishDate: 2022-07-30
slug: docker-sem-root
tags:
  - docker

---

Crie o grupo `docker`.

```console
sudo groupadd docker
```

Adicione o usuário ao grupo `docker`.

```console
sudo usermod -aG docker $USER
```

Aplique as modificações ao grupo.

```console
newgrp docker
```

Verifique se é possível de rodar o `docker`.

```console
docker version
```

{{< tip class="warning" >}}
Isso não é recomendado em servidores conectados a internet, mantenha os privilégios `docker` ao usuário `root`.
{{< /tip >}}
