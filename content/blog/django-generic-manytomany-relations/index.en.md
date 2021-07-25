---
title: "Django Generic ManyToMany Relations"
description: "How to solve the issue of dealing with generic many to many relations on Django"
publishDate: 2016-04-29
tags:
  - python
  - django
  - frameworks
images:
  - /img/posts/django-generic-manytomany-relations.png

---

Django's official documentation nicely covers its generic relationship functionality when you have to make an **OneToMany** (one-to-many or **1:N**) relation, but when you need to implement a generic **ManyToMany** (many-to-many or **N:N**) relation, there is not much documentation about it.

I recently had to implement an **N:N** with a generic side, and discovered a library called [django-gm2m](http://django-gm2m.readthedocs.io/en/stable/index.html) that was very useful to me in this task, and I will describe here giving tips on how to make this implementation and how to avoid possible headaches.

{{< tip class="info" >}}
This implementation was tested at Django 1.8 and 2.1 with python 2.7 and 3.7 respectively.
{{< /tip >}}

## Modeling

To demonstrate this modeling we will follow the concept of a digital library were the user profiles can get different formats of media like **audio**, **video** and **text** and the same media can be linked to more than one profile.

In this kind of scenario we have at one side **N profiles** and at the other side **N medias** at different formats and the models on **Django** are:

{{< highlight python >}}
from django.db import models

class Profile(models.Model):
    name = models.CharField(max_length=80)
    email = models.EmailField()

class Audio(models.Model):
    title = models.CharField(max_length=60)
    records = models.PositiveSmallIntegerField()

class Video(models.Model):
    title = models.CharField(max_length=60)
    resolution_x = models.PositiveSmallIntegerField()
    resolution_y = models.PositiveSmallIntegerField()

class Text(models.Model):
    title = models.CharField(max_length=60)
    pages = models.PositiveSmallIntegerField()
{{< / highlight >}}

## Installation

To install `django-gm2m` run at your **virtualenv** or environment of choice:

{{< highlight console >}}
$ pip install django-gm2m
{{< / highlight >}}

After that make sure you have at your `INSTALLED_APPS` the `django.contrib.contenttypes` entry and add `gm2m`:

{{< highlight python >}}
INSTALLED_APPS = [
   ...
   'django.contrib.contenttypes',
   ...
   'gm2m',
]
{{< / highlight >}}

## Making the Relation

To create the relation add to the not generic side the field `GM2MField`:

{{< highlight python >}}
from gm2m import GM2MField

class Profile(models.Model):
    ...
    medias = GM2MField('Video', 'Audio', 'Text', related_name='profiles')
{{< / highlight >}}

{{< tip class="info" >}}
The parameter `related_name` will create the reverse relation at the other models so you can access the profiles trough any media instance. If you don't set it to anything it will follow the pattern `<model>_set`.
{{< /tip >}}

{{< tip class="info" >}}
Another important point are the positional parameters of  `GM2MField` that are the models that you want Django mount the reverse relations automatically. In case of not setting the classes there the field will work but the reverse relation will not be mounted.
{{< /tip >}}

Now you are ready to add a media to a profile through `profile.medias.add(video)`.

Eventually will be needed to add some field on the relation and to do that you will need to implement the relation model. To do that without blocking the `.add()` function you need to set the `through` parameter to define which model that will represent the relation:

{{< highlight python >}}
from django.contrib.contenttypes.fields import GenericForeignKey
from django.contrib.contenttypes.models import ContentType

class Profile(models.Model):
    ...
    medias = GM2MField(
        'Video', 'Audio', 'Text', through='ProfileMediaLink', related_name='profiles'
    )

class ProfileMediaLink(models.Model):
    # required fields to mount the relation
    profile = models.ForeignKey(Profile)
    media = GenericForeignKey('media_ct', 'media_id')
    media_ct = models.ForeignKey(ContentType)
    media_id = models.PositiveIntegerField()

    # extras
    linked_at = models.DateField(auto_now_add=True)
{{< / highlight >}}

See that for the generic side we use the same pattern from [generic relations](https://docs.djangoproject.com/en/dev/ref/contrib/contenttypes/#generic-relations) described at the official documentation of **Django**.  A `GenericForeignKey` composed by a `ForeignKey` to `ContentType` and a `PositiveIntegerField` to keep the id for the model instance referenced.


To link a profile to a media you just need to do the following:

{{< highlight python >}}
>>> ProfileMediaLink(profile=profile, media=video).save()
{{< / highlight >}}

## Conclusion

Generic relation is a powerful tool that allow us to achieve a high abstraction level. However its implementation can scale up at volume and complexity, so think a little before going to this kind of solution right upfront.
