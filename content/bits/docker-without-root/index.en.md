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

Apply the changes to the group.

```shell
newgrp docker
```

Verify if you can run `docker`.

```shell
docker version
```

{{< tip class="warning" >}}
This is not recommended on internet facing servers, keep `docker` privileges to the `root` user.
{{< /tip >}}
