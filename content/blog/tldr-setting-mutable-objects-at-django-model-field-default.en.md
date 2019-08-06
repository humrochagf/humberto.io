---
title: "TLDR: Setting mutable objects at Django Model Field default"
description: Learn how to set mutable objects as default on Django Model Fields properly, and save you a lot of time with debugging
publishDate: 2018-04-25
images:
  - /img/generics/django.png
tags: ["tldr", "python", "django"]
draft: false

---

This post it the result of a **bug** that haunted me for three months until I finally could isolate the error properly, and get to the root of the issue.

{{% tip class="warning" %}}
**Disclaimer:** this information exists at the [Django](https://docs.djangoproject.com/en/2.2/ref/models/fields/#default) docs, however strengthening the importance of this info is vital to prevent people to spend a lot of time with this kind of bug.
{{% /tip %}}

One of the core parameters of a Django **Model field** is the **default**. It sets the value of the **Field** when you don't pass a value to it at the **Model** instance creation. The default parameter can receive a **value** or a **callable**, and callable are functions or any object that implements the `__call__` method.

That said, let's go to the tip:

**Never** use a **mutable object** as default. Because if you do that, every instance of the model will share the same object during the code execution.

If you want to use them as default, use the callable instead:

{{< highlight python >}}
# Wrong
class SomeClass(models.Model):
    custom_data = JSONField(default={})

# Right
class SomeClass(models.Model):
    custom_data = JSONField(default=dict)
{{< / highlight >}}

The bigger problem with this kind of mistake is that it causes at most cases a silent bug that will come back to haunt you when you least expect. So check your default parameters!

## Bonus

You can't use **lambda** functions as the default because they are not serializable during the creation of the **migrations**. If you need to use a more elaborated value as default consider creating a function to use as an argument. But, remember to pass the function as the argument (ex.: `default=create_default`), not its result (ex.: `default=create_default()`).
