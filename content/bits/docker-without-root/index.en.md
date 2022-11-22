---
title: "Docker without root"
publishDate: 2022-07-30
tags:
  - docker

---

Create the `docker` group.

```console
sudo groupadd docker
```

Add your user to the `docker` group.

```console
sudo usermod -aG docker $USER
```

Apply the changes to the group.

```console
newgrp docker
```

Verify if you can run `docker`.

```console
docker version
```

{{< tip class="warning" >}}
This is not recommended on internet facing servers, keep `docker` privileges to the `root` user.
{{< /tip >}}
