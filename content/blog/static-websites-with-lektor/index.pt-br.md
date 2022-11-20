---
title: "Sites Estáticos com Lektor"
description: "Introdução a construção de sites estáticos com lektor"
publishDate: 2016-04-23
slug: sites-estaticos-com-lektor
tags:
  - python
  - lektor
  - frameworks
  - blog
  - static-sites
  - gh-pages
images:
  - /img/posts/static-websites-with-lektor.pt-br.png
aliases:
  - static-websites-with-lektor
  - /blog/sites-estaticos-com-lektor

---

Faz pelo menos 4 anos que eu ensaio montar um blog, e neste período já criei alguns para a comunidade,  mas quando chegava a hora de criar o meu nunca conseguia chegar ao ponto de publicar.

Usando ferramentas como o WordPress o problema era a dificuldade de customização e a quantidade de ferramentas e padrões que eu nunca iria usar, além é claro, de não ser **Python**.

Em seguida com o [GitHub Pages](https://pages.github.com) eu descobri o [Pelican](http://blog.getpelican.com) por indicação do [Magnun Leno](http://mindbending.org/pt) e comecei a fazer muita coisa com ele, mas da mesma forma com que eu ganhei em liberdade de customização, o processo autoral era o mesmo do desenvolvimento o que em um primeiro momento parece legal não é mesmo? Pois é, mas com o tempo você percebe que isso acaba virando um problema quando você para a escrita de uma postagem para arrumar um **bug** ou melhorar alguma coisa no layout e lá vamos nós novamente...

![e lá vamos nós novamente](assets/again.png)

Eis que em uma conversa no [grupo de telegram da comunidade python](https://telegram.me/pythonbr) me citam o Lektor e aí começou a aventura.

## Lektor

[Lektor](https://www.getlektor.com) é um gerenciador de conteúdo estático criado por [Armin Ronacher](http://lucumr.pocoo.org) (sim, o criador do [flask](http://flask.pocoo.org)) que permite a criação de websites a partir de arquivos de texto.

## Porque usar o Lektor

Como descrito no próprio [site](https://www.getlektor.com/docs/what) ele bebeu das fontes dos CMSs, dos frameworks e dos geradores de site estático e chegou em algo que eu considero um ponto de equilíbrio entre eles, e que nos leva as seguintes vantagens:

- **Estático:** O site final é totalmente estático, o que permite sua hospedagem em qualquer lugar;
- **CMS:** Uma interface de produção de conteúdo que roda localmente e tira a necessidade de entender programação para poder produzir conteúdo. (no meu caso me tira do mundo do código e me deixa focar no conteúdo);
- **Framework:** Ele possuí um sistema de models em arquivos de texto e um sistema de templates que usa Jinja2 que cria um ambiente familiar para quem já desenvolveu algo em django, flask e similares;
- **Deploy:** O sistema de deploy dele é reduzido á uma configuração em um arquivo, o que permite a rápida publicação sem ficar dias aprendendo técnicas de deploy quando tudo que você quer no começo é colocar seu site no ar.

## Instalação do Lektor

O Lektor roda em Python 2.7 ou superior e depende das bibliotecas `python-dev`, `libssl-dev`, `libffi-dev` e `imagemagick` que você pode facilmente instalar rodando:

```console
$ sudo apt-get install python-dev libssl-dev libffi-dev imagemagick
```

{{< tip class="info" >}}
No Mac você pode dar uma olhada nos pacotes do **brew**, no Windows o **chocolatey** en os outros gerenciadores de pacotes das outras distribuições Linux, ambos possuem suas versões das bibliotecas citadas
{{< /tip >}}

A instalação do Lektor é bem direta:

```console
$ curl -sf https://www.getlektor.com/install.sh | sh
```

Este comando instala diretamente no sistema, se você prefere instalar em sua virtualenv:

```console
$ virtualenv venv
$ . venv/bin/activate
$ pip install Lektor
```

Esta forma é desencorajada pelos desenvolvedores pois o lektor gerencia virtualenvs internamente para instalação de seus plugins, portanto caso seja desenvolvedor e quer ter mais controle sobre o lektor instale a versão de desenvolvimento e esteja pronto para sujar as mãos quando for preciso, e quem sabe até contribuir com o desenvolvimento do lektor:

```console
$ git clone https://github.com/lektor/lektor
$ cd lektor
$ make build-js
$ virtualenv venv
$ . venv/bin/activate
$ pip install --editable .
```

{{< tip class="info" >}}
requer `npm` instalado para montar a interface de administração.
{{< /tip >}}

## Criando o Site

Após a instalação para criar o seu site basta utilizar o comando de criação de projeto:

```console
$ lektor quickstart
```

Ele irá te fazer algumas perguntas e criar um projeto com o nome que você informou.

### Estrutura

Esta é a estrutura básica de um site gerado pelo lektor:

```console
meusite
├── assets/
├── content/
├── templates/
├── models/
└── meusite.lektorproject
```

- **assets:** Pasta onde ficam os arquivos  .css, .js, .ico entre outros recursos estáticos;
- **content:** Pasta onde ficam os arquivos que iram gerar as páginas do site, cada subpasta corresponde a uma página no site gerado;
- **templates:** Pasta onde ficam os arquivos de template que definem a sua estrutura visual;
- **models:** Pasta onde ficam os arquivos que definem a modelagem de dados;
- **meusite.lektorproject:** Arquivo com as configurações gerais do site.

### Executando o projeto

Para rodar o site em sua máquina basta entrar no diretório criado e iniciar o servidor local:

```console
$ cd meusite
$ lektor server
```

Com o servidor rodando acesse [localhost:5000](http://localhost:5000) para ver o resultado:

![primeiro site rodando com Lektor](assets/website.pt-br.png)

### Acessando o Admin

Para acessar o admin clique na imagem de lápis no canto superior direito da página que você criou ou acesse [localhost:5000](http://localhost:5000/admin)

![página de admin do Lektor](assets/admin.pt-br.png)

## Publicando o Site

Exitem duas maneiras de se fazer o deploy do site construído com o lektor, a manual, que é basicamente rodar o comando `build` e copiar manualmente os arquivos para o servidor:

```console
$ lektor build --output-path destino
```

E a forma automática, que pode ser feita (neste caso para o GitHub Pages) adicionando a seguinte configuração no arquivo `.lektorproject`:

```ini
[servers.production]
target = ghpages://usuario/repositorio
```

{{< tip class="warning" >}}
O deploy faz um force push na branch `master` ou `gh-pages` dependendo do tipo de repositório, portanto, cuidado para não sobrescrever os dados de seu repositório. Mantenha o código fonte em uma branch separada, você pode dar uma conferida no [meu repositório](https://github.com/humrochagf/humberto.io-lektor) para ter uma ideia.
{{< /tip >}}

No caso de hospedagem cloud:

```ini
[servers.production]
name = Production
target = rsync://usuario@servidor.dominio.com.br/srv/meusite/www
```

E rodando em seguida o comando:

```console
$ lektor deploy
```

Para informações mais detalhadas você pode acessar a [documentação do lektor](https://www.getlektor.com/docs) e também ficar de olho nas próximas postagens.
