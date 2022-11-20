---
title: "TLDR: Executando comandos em multiplos hosts com Fabric"
description: "Descubra utilizar o Fabric para automatizar tarefas repetitivas"
publishDate: 2018-02-17
slug: tldr-executando-comandos-em-multiplos-hosts-com-fabric
tags:
  - tldr
  - python
  - fabric
images:
  - /img/posts/tldr-running-commands-at-multiple-hosts-with-fabric.png
aliases:
  - /blog/tldr-executando-comandos-em-multiplos-hosts-com-fabric

---

Me perguntaram como realizar uma conexão ssh em um servidor para rodar o comando `df -h` que mostra o uso de disco da máquina. Logo depois descobri que a dúvida se estendia a como fazer isso em vários servidores.

A resposta me veio rápido na mente, e junto com ela a ideia de documentar estas coisas, o que me trouxe ao começo desta série de postagens curtas carinhosamente batizada de **TLDR**, que é a abreviação de **T**oo **L**ong **D**idn`t **R**ead ou, em português, Muito longo, não li.

Pois bem, para automatizar tarefas repetitivas tal como o tão falado processo de [deploy (implantação de software)](https://pt.wikipedia.org/wiki/Implanta%C3%A7%C3%A3o_de_software) ou a execução de um único comando em múltiplos servidores existe uma ferramenta muito legal e prática chamada [Fabric](http://www.fabfile.org/) que utilizaremos para automatizar a questão levantada no início da postagem.

Para instalar o Fabric rode:

```console
$ pip install Fabric3
```

{{< tip class="warning" >}}
O Fabric original está na versão 2 porém sua API mudou completamente em relação a atual e muitas coisas precisam ser implementadas ainda, Fabric 3 é um fork que possuí compatibilidade com a versão 1 e suporta Python 3
{{< /tip >}}

Agora vamos escrever o código para rodar o `df -h` nos servidores e salvar em um arquivo chamado `fabfile.py`:

```python
from fabric.api import env, run

env.hosts = ['user1@server1', 'user2@server2']


def disk_usage():
    run('df -h')
```

{{< tip class="info" >}}
O arquivo deve se chamar `fabfile.py` pois o Fabric busca automaticamente por este arquivo quando rodamos o comando
{{< /tip >}}

{{< tip class="warning" >}}
O Fabric faz uma conexão ssh com os servidores, portanto tenha uma chave ssh configurada  em sua máquina e liberada no allowed_hosts dos usuários dos servidores de destino
{{< /tip >}}

Com o arquivo pronto é só rodar o comando definido no aquivo:

```console
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
```

O Fabric roda a tarefa em sequência na lista de hosts informada. Caso queira rodar os comandos em paralelo é só passar a flag `-P` para o comando:

```console
$ fab disk_usage -P
```

{{< tip class="warning" >}}
Lembrando que ao rodar a tarefa em paralelo você perde o controle da ordem de execução da sua lista de servidores
{{< /tip >}}

## Bônus

Dica do [@luizirber](https://twitter.com/luizirber) no twitter.

O Fabric possuí um padrão de comando muito legal que nos permite executar o exemplo acima totalmente da linha de comando que é:

```console
$ fab [options] -- [shell command]
```

O comando ficaria desta forma:

```console
$ fab -H 'user1@server1, user2@server2' -- df -h
```
