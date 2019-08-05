--- 
title: "TLDR: Running commands at multiple hosts with Fabric"
description: Discover how to use Fabric to automate repetitive tasks at your servers
publishDate: 2018-02-17
images:
  - /img/generics/fabric.png
tags: ["tldr", "python", "fabric"]
draft: false

---

Recently someones asked me how to perform a connection to a server to run the command `df -h` using ssh to display the disk usage of the machine. After a quick chat, I discovered that the question was more than just a single check to a server. It was to extend that to multiple servers.

The answer to the question was quick and came with a "click". How about to document these quick tips at the blog? It can help someone and also help me to remind me how to do something if I need it in the future. With that in mind, I created this series of posts that I kindly baptized as **TLDR** the abbreviation for **T**oo **L**ong ** D**idn't **R**ead.

Now moving to the actual tip, to automate repetitive tasks like at the question above or on more complex processes like on [software deployment](https://en.wikipedia.org/wiki/Software_deployment) there is a handy python library called [Fabric](http://www.fabfile.org/) that we are going to use to help us on that.

To install Fabric you can use pip:

{{< highlight console >}}
$ pip install Fabric3
{{< / highlight >}}

{{% tip class="warning" %}}
The original Fabric is at version 2, but your API changed utterly, and there is thing still to be implemented. Fabric 3 is a fork that has compatibility with the first version of Fabric.
{{% /tip %}}

Then we create the `fabfile.py` with the following content:

{{< highlight python >}}
from fabric.api import env, run

env.hosts = ['user1@server1', 'user2@server2']


def disk_usage():
    run('df -h')
{{< / highlight >}}

{{% tip class="info" %}}
The file must be called `fabfile.py` so Fabric can automatically discover him when we run the command.
{{% /tip %}}

{{% tip class="warning" %}}
Fabric makes the ssh connection with the servers so that you will need an ssh key configured at your machine and its access allowed at the `allowed_hosts` file of the target servers.
{{% /tip %}}

With the file ready, you need to run this command:

{{< highlight console >}}
$ fab disk_usage
[user1@server1] Executing task 'disk_usage'
[user1@server1] run: df -h
[user1@server1] out: Filesystem      Size  Used Avail Use% Mounted on
[user1@server1] out: /dev/root        20G  2.2G   17G  12% /
[user1@server1] out: devtmpfs        488M     0  488M   0% /dev
[user1@server1] out: tmpfs           490M     0  490M   0% /dev/shm
[user1@server1] out: tmpfs           490M   51M  440M  11% /run
[user1@server1] out: tmpfs           5.0M     0  5.0M   0% /run/lock
[user1@server1] out: tmpfs           490M     0  490M   0% /sys/fs/cgroup
[user1@server1] out: tmpfs            98M     0   98M   0% /run/user/0
[user1@server1] out:

[user2@server2] Executing task 'disk_usage'
[user2@server2] run: df -h
[user2@server2] out: Filesystem      Size  Used Avail Use% Mounted on
[user2@server2] out: /dev/root        20G  3.9G   15G  22% /
[user2@server2] out: devtmpfs        488M  4.0K  488M   1% /dev
[user2@server2] out: none            4.0K     0  4.0K   0% /sys/fs/cgroup
[user2@server2] out: none             98M  352K   98M   1% /run
[user2@server2] out: none            5.0M     0  5.0M   0% /run/lock
[user2@server2] out: none            490M     0  490M   0% /run/shm
[user2@server2] out: none            100M     0  100M   0% /run/user
[user2@server2] out:


Done.
{{< / highlight >}}

Fabric will run the task in sequence at the host list informed. If you want to run it in parallel use the `-P` flag:

{{< highlight console >}}
$ fab disk_usage -P
{{< / highlight >}}

{{% tip class="warning" %}}
Just remind that running things in parallel you don't have control of the server order.
{{% /tip %}}

## Bonus

Tip from [@luizirber](https://twitter.com/luizirber) at twitter.

Fabric has a pattern to run its commands at the CLI without needing to write code:

{{< highlight console >}}
$ fab [options] -- [shell command]
{{< / highlight >}}

The disk usage command woud be:

{{< highlight console >}}
$ fab -H 'user1@server1, user2@server2' -- df -h
{{< / highlight >}}
