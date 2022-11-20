---
title: "Docker without root"
publishDate: 2022-07-30
tags:
  - docker

---

Create the `docker` group.

```shell
sudo groupadd docker
```

Add your user to the `docker` group.

```shell
sudo usermod -aG docker $USER
```

Activate the changes to groups.

```shell
newgrp docker
```

Verify if you can run docker.

```shell
docker version
```

{{< tip class="warning" >}}
Do not use this step to internet facing servers, keep docker privileges to the root user.
{{< /tip >}}
