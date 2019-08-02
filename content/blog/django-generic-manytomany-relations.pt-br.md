---
title: Relacionamentos ManyToMany genéricos com Django
description: Dicas de como resolver este problema com o django
publishDate: 2016-04-29
images: 
  - /img/django.png
slug: relacionamentos-manytomany-genericos-com-django
tags: ["python", "django", "frameworks"]
aliases: [
    "/blog/relacionamentos-manytomany-genericos-com-django"
]
draft: false

---

A documentação oficial do django cobre muito bem sua funcionalidade de relacionamento genérico na cardinalidade `OneToMany` (um para muitos ou **1:N**), porém, quando é preciso implementar um relacionamento genérico de `ManyToMany` (muitos para muitos ou **N:N**) não há muita documentação a respeito.

Recentemente, precisei implementar um **N:N** com um lado genérico, e encontrei uma biblioteca chamada [django-gm2m](http://django-gm2m.readthedocs.io/en/stable/index.html) que me foi muito útil nesta tarefa, e vou descrever aqui dando dicas de como implementar evitando possíveis dores de cabeça.

{{% tip class="info" %}}
As versões em que já implementei esta modelagem foram django 1.8 e 1.9 com python 2.7 e 3.4
{{% /tip %}} 

## Modelagem

Para demonstrar esta modelagem iremos usar a ideia de uma biblioteca  digital onde os usuários podem alugar mídias em **audio**, **video** e **texto** e a mesma mídia pode estar alugada para mais de um usuário.

Neste caso teremos do lado N os usuários e do lado N genérico as mídias. E seus models no django sem aplicar o relacionamento são:

```python
from django.db import models

class Usuario(models.Model):
    nome = models.CharField(max_length=80)
    email = models.EmailField()

class Audio(models.Model):
    titulo = models.CharField(max_length=60)
    num_faixas = models.PositiveSmallIntegerField()

class Video(models.Model):
    titulo = models.CharField(max_length=60)
    resolucao_x = models.PositiveSmallIntegerField()
    resolucao_y = models.PositiveSmallIntegerField()

class Texto(models.Model):
    titulo = models.CharField(max_length=60)
    num_paginas = models.PositiveSmallIntegerField()
```

## Instalação

Para instalar o django-gm2m rode em sua virtualenv ou ambiente do seu projeto:

```console
$ pip install django-gm2m
```

Em seguida certifique-se de ter em seus `INSTALLED_APPS` o `django.contrib.contenttypes` e adicione `gm2m`:

```python
INSTALLED_APPS = [
   ...
   'django.contrib.contenttypes',
   ...
   'gm2m',
]
```

## Montando o Relacionamento

Para criar o relacionamento insira do lado não genérico o campo `GM2MField`:

```python
from gm2m import GM2MField

class Usuario(models.Model):
    ...
    midias = GM2MField(
        'Video', 'Audio', 'Texto', through='Aluguel', related_name='usuarios'
    )
```

{{% tip class="info" %}}
O parâmetro `related_name` é o nome com o qual você vai acessar a relação inversa, ou seja, acessar os usuários através da mídia. Se você não usar este atributo ele assume o nome padrão que é `<model>_set`.
{{% /tip %}} 

{{% tip class="info" %}}
Outro ponto importante são os parâmetros posicionais do `GM2MField`, que são os models no qual você quer que o django monte o relacionamento inverso automaticamente,  caso não seja informado a vinculação funciona da mesma forma, porém, o acesso aos usuários através da mídia não será montado.
{{% /tip %}} 

Feito isso já estaria pronto e você adicionaria uma mídia a um usuário através de `usuario.midias.add(video)`.

Porém, eventualmente será necessário adicionar algum campo no model intermediário, gerando uma migração de dados seguida de alterações no código pois quando você define um model intermediário para uma relação N:N no django, a vinculação através do `.add()` é bloqueada, te obrigando a usar o manager do model.

Para evitar isso, se você possuí planos de incrementar esta modelagem (que é o nosso caso) já crie da forma que será demonstrada, que é passando o parâmetro `through` para o campo e criando o model intermediário, que no nosso caso chamaremos de `Aluguel`:

```python
from django.contrib.contenttypes.fields import GenericForeignKey
from django.contrib.contenttypes.models import ContentType

class Usuario(models.Model):
    ...
    midias = GM2MField(
        'Video', 'Audio', 'Texto', through='Aluguel', related_name='usuarios'
    )

class Aluguel(models.Model):
    # campos base da modelagem
    usuario = models.ForeignKey(Usuario)
    midia = GenericForeignKey('midia_ct', 'midia_id')
    midia_ct = models.ForeignKey(ContentType)
    midia_id = models.PositiveIntegerField()
    # campos extras
    alugado_em = models.DateField(auto_now_add=True)
    vencido_em = models.DateField(null=True)
```

Observe que para o lado genérico é utilizado o mesmo padrão de [relacionamento genérico](https://docs.djangoproject.com/en/1.9/ref/contrib/contenttypes/#generic-relations) descrito na documentação oficial do django. Uma `GenericForeignKey` composta de uma `ForeignKey` para `ContentType` e um `PositiveIntegerField` para guardar o id da instância do model referenciado.

Para vincular um usuário a uma mídia basta criar e salvar um aluguél da seguinte forma:

```python
>>> Aluguel(usuario=usuario, midia=video).save()
```

## Conclusão

Relacionamento genérico é uma ferramenta muito poderosa e permite alto nível de abstração. Mas modelagens deste tipo costumam a levar a tabelas com um volume de dados muito grande, portanto analise sua arquitetura antes de implementar para ter certeza que esta é a modelagem correta para seu caso antes de implementa-la.
