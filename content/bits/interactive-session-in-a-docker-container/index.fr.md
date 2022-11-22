---
title: "Session interactive dans un conteneur Docker"
publishDate: 2022-11-22
slug: session-interactive-dans-un-conteneur-docker
tags:
  - docker
  - bash
  - shell

---

Pour ouvrir une session bash dans un conteneur executez:

```console
docker exec -it <nom-du-conteneur> /bin/bash
```

Dans le cas où l'image est minimaliste il est toujours possible d'ouvrir une session shell:

```console
docker exec -it <nom-du-conteneur> /bin/sh
```

{{< tip class="warning" >}}
Il est seulement possible d'ouvrir une session dans un conteneur qui est en cours d'exécution, donc vérifiez son statut avec `docker ps` avant de essayer ouvrir une session.
{{< /tip >}}


Si vous ne rappelez pas le nom du conteneur, listez-les avec:

```console
docker ps --format "{{.Names}}"
```
