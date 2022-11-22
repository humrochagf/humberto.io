---
title: "Sessão interativa em um contêiner Docker"
publishDate: 2022-11-22
slug: sessao-interativa-em-um-conteiner-docker
tags:
  - docker
  - bash
  - shell

---

Abrindo uma sessão bash em um contêiner:

```console
docker exec -it <nome-do-conteiner> /bin/bash
```

No caso de imagens minimalistas onde bash não está disponível, ainda é possível abrir uma sessão shell:

```console
docker exec -it <nome-do-conteiner> /bin/sh
```

{{< tip class="warning" >}}
Uma sessão só pode ser aberta em contêineres que estão rodando. Verifique o status com `docker ps` antes de tentar abrir uma sessão.
{{< /tip >}}


Em caso de dúvida sobre o nome do contêiner é possível lista-los com:

```console
docker ps --format "{{.Names}}"
```
