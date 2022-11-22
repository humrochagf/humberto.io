---
title: "Interactive session in a Docker container"
publishDate: 2022-11-22
tags:
  - docker
  - bash
  - shell

---

Opening a bash session in a given container:

```console
docker exec -it <container-name> /bin/bash
```

In case the image is more bare bones you can still open a shell session:

```console
docker exec -it <container-name> /bin/sh
```

{{< tip class="warning" >}}
You can only open sessions in containers that are running so check their status with `docker ps` before trying to open the session.
{{< /tip >}}


If you are not sure about the container names you can list them with:

```console
docker ps --format "{{.Names}}"
```
