---
title: "TLDR: Configurando corretamente o default de um Model Field do Django"
description: Poupe muitas horas de debug em seu código com esta dica de como definir o valor default de um campo no Model do Django
publishDate: 2018-04-25
images:
  - /img/generics/django.png
slug: tldr-configurando-corretamente-o-default-de-um-model-field-do-django
tags: ["tldr", "python", "django"]
aliases: [
  "/blog/tldr-configurando-corretamente-o-default-de-um-model-field-do-django"
]
draft: false

---

Esta postagem é o resultado de um **bug** que me assombrou por uns três meses até conseguir isolar totalmente o erro e, por acontecer em condições muito específicas de temperatura e pressão, tomou muito tempo quebrando cabeça até perceber que o problema era este.

{{< tip class="warning" >}}
**Disclaimer:** essa informação existe na documentação do [Django](https://docs.djangoproject.com/en/2.0/ref/models/fields/#default), porém reforçá-la pode lhe poupar um belo tempo de **debugging**.
{{< /tip >}}

Um dos atributos básicos de um **Model field** do Django é o **default** que determina o valor padrão de um campo quando criamos uma instância do **Model**. Você pode atribuir um **valor** a ele ou um **callable** que nada mais é que uma função ou uma classe que implemente o método `__call__`.

Agora vem a dica importante:

Nunca atribua um valor **mutável** a este campo. Pois isso irá fazer com que todas as instâncias deste **model** que foram criadas em uma mesma execução do seu código compartilhem aquela mesma instância do objeto mutável. Isso vale para dicionários, listas, sets e qualquer outro objeto mutável.

Quando quiser atribuir um deles como **default** passe o **callable** como parâmetro:

{{< highlight python >}}
# Errado
class SomeClass(models.Model):
    custom_data = JSONField(default={})

# Certo
class SomeClass(models.Model):
    custom_data = JSONField(default=dict)
{{< / highlight >}}

O maior problema deste erro é que ele muitas vezes é silencioso e só aparece em alguma sequência de criações e pode te deixar muito confuso.

## Bônus

Funções **lambda** não podem ser usadas como **default** pois elas não conseguem ser serializadas na hora de gerar suas **migrations**, se precisar de um valor mais elaborado crie uma função e passe ela como argumento. Mas, lembre-se de passar a função como argumento (ex.: `default=create_default`), não o resultado dela (ex.: `default=create_default()`).
