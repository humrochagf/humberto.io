---
title: Separando o Frontend do Backend com Angular e Django
description: Descubra como separar suas stacks para ganhar independência entre o frontend e o backend utilizando Angular 6 e Django 2
publishDate: 2018-02-13
images:
  - /img/angular-django/django-startproject.gif
slug: separando-o-frontend-do-backend-com-angular-e-django
tags: ["python", "django", "typescript", "angular", "api"]
aliases: [
  "/blog/separando-o-frontend-do-backend-com-angular-e-django"
]
draft: false

---

No mundo do desenvolvimento web as coisas vem evoluindo muito rápido e crescendo em complexidade em um ritmo intenso. Aquele modelo antigo de criação de sites onde tinamos o webmaster que dominava o html e logava no servidor para atualiza uma página é algo cada vez mais raro de se ver. Até para criar sites estáticos estamos utilizando ferramentas para agilizar nosso trabalho.

Nos últimos anos tivemos um crescimento expressivo dos frameworks javascript e uma grande evolução do frontend como um todo, e estas novas tecnologias possuem ferramentas e fluxos de desenvolvimento próprios, o que vem tornando mais oneroso para um sistema só entregar o backend e o frontend.

Ao mesmo tempo vem se popularizando e ficando mais interessante o uso de API`s e a separação do frontend do backend em diferentes projetos, o que trás a proposta de dar  mais autonomia aos times de desenvolvimento além de tornar a aplicação mais escalável.

Contudo, ao deparar com esta realidade muita gente (eu incluso em minha primeira tentativa de implementação) fica perdida em como botar em prática esta forma de se implementar sistemas web.

Nesta postagem veremos um exemplo simplificado de como botar em prática esta arquitetura. Criaremos uma aplicação de lista de compras onde poderemos adicionar visualizar e excluir itens da lista. Para isso utilizaremos Django 2 para o backend e Angular 6 para o frontend.

Os repositórios com o código desta postagem são:

- **Backend**: [https://github.com/humrochagf/post-back-do-front-backend](https://github.com/humrochagf/post-back-do-front-backend)
- **Frontend**: [https://github.com/humrochagf/post-back-do-front-frontend](https://github.com/humrochagf/post-back-do-front-frontend)

{{% tip class="info" %}}
**UPDATE (22/08/2018)** Postagem atualizada e testada para utilizar Angular 6
{{% /tip %}}

## Separando as Responsabilidades

Para começar vamos falar um pouco sobre esta separação de responsabilidades dentro do projeto.

Existem duas grandes responsabilidades dentro de uma aplicação web interativa. A gestão e a visualização dos dados.

## Backend

Em uma arquitetura web de cliente e servidor, nos referimos ao backend como a camada de acesso aos dados que é processada totalmente pelo servidor responsável pelo serviço. É dele a responsabilidade de acessar, processar, armazenar e controlar o acesso os dados requisitados pelo cliente. Seu modelo de implementação mais famoso e encontrado na internet são as API`s REST.

## Frontend

No modelo citado acima, o frontend é responsável pela camada de apresentação dos dados e seu processamento acontece em sua maioria no cliente, mas em alguns casos parte deste processamento pode ser feito pelo servidor.

## Implementando o backend

Definidas as responsabilidades vamos implementar nossa aplicação, partindo do backend.

Começamos com a instalação do django seguido da criação do projeto:

{{< highlight console >}}
$ pip install django
$ django-admin startproject backend
$ cd backend
$ python manage.py migrate
$ python manage.py runserver
{{< / highlight >}}

Ao iniciar o navegador em [localhost:8000](http://localhost:8000) veremos a nova tela de sucesso de criação de aplicação do Django 2 mostrando que a inicialização da sua nova aplicação foi um sucesso (muito bonita a nova tela né?!).

![novo projeto](/img/angular-django/django-startproject.gif)

O próximo passo é instalar o Django Rest Framework, para montar a api do nosso backend:

{{< highlight console >}}
$ pip install djangorestframework
{{< / highlight >}}

Em seguida editamos o `settings.py` para habilitá-lo:

{{< highlight python >}}
INSTALLED_APPS = (
    ...
    'rest_framework',
)
{{< / highlight >}}

No `urls.py` registramos as urls de autenticação para acessar a interface de navegação de api

{{< highlight python >}}
from django.urls import include, path

urlpatterns = [
    ...
    path('api-auth/', include('rest_framework.urls')),
]
{{< / highlight >}}

Agora podemos visualizar a tela de login acessando [localhost:8000/api-auth/login/](http://localhost:8000/api-auth/login/)

![rest login](/img/angular-django/djangorestframework-login.png)

Com isso, daremos inicio a nossa aplicação de lista de compras:

{{< highlight console >}}
python manage.py startapp shopping
{{< / highlight >}}

A aplicação será bem direta ao ponto com um único **model** que é quem representa a lista de compras no banco de dados, um **serializer** que transforma os dados de objeto para **json** e vice versa, além de e validar os dados da requisição e uma **view** que recebe, encaminha e responde a estas requisições:

{{< highlight python >}}
# settings .py

INSTALLED_APPS = (
    ...
    'shopping',
)

# models.py

from django.db import models


class ShoppingItem(models.Model):

    name = models.CharField(max_length=60)
    quantity = models.PositiveSmallIntegerField()
    checked = models.BooleanField(default=False)

# serializers.py

from rest_framework import serializers

from .models import ShoppingItem


class ShoppingItemSerializer(serializers.ModelSerializer):

    class Meta:
        model = ShoppingItem
        fields = '__all__'

# views.py

from rest_framework import viewsets

from .models import ShoppingItem
from .serializers import ShoppingItemSerializer


class ShoppingItemViewSet(viewsets.ModelViewSet):

    serializer_class = ShoppingItemSerializer
    queryset = ShoppingItem.objects.all()

# urls.py

from django.urls import include, path
from rest_framework import routers

from shopping.views import ShoppingItemViewSet

router = routers.DefaultRouter()
router.register(
    'shopping-item', ShoppingItemViewSet, base_name='shopping-item'
)


urlpatterns = [
    ...
    path('', include(router.urls)),
]
{{< / highlight >}}

Para finalizar criamos o arquivo de migração do banco de dados seguido da migração do mesmo:

{{< highlight console >}}
$ python manage.py makemigrations
$ python manage.py migrate
{{< / highlight >}}

E voilá, nosso backend está funcionando:

![shopping list](/img/angular-django/djangorestframework-shopping-list.png)


## Implementando o frontend

Para iniciar a implementação do frontend vamos instalar a ferramenta de linha de comando do **Angular** e criar um novo projeto:

{{< highlight console >}}
$ npm install -g @angular/cli
$ ng new frontend
$ cd frontend
$ ng serve
{{< / highlight >}}

Se tudo tiver corrido como esperado ao acessar [localhost:4200](http://localhost:4200) veremos a página inicial de um novo projeto Angular.

![home angular](/img/angular-django/angular-startproject.png)

Agora criaremos um **service** para acessar nossa api e uma **view** para listar os itens da nossa lista de compras:

{{< highlight typescript >}}
// shopping-item.interface.ts

export interface ShoppingItem {
  id: number;
  name: string;
  quantity: number;
  checked: boolean;
}

// api.service.ts

import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable()
export class ApiService {

  private apiRoot = 'http://localhost:8000/';

  constructor(private http: HttpClient) { }


  getShoppingItems() {
    return this.http.get(this.apiRoot.concat('shopping-item/'));
  }
}

//  app.component.ts

import { Component, OnInit } from '@angular/core';

import { ApiService } from './api.service';
import { ShoppingItem } from './shopping-item.interface';

@Component({
  selector: 'app-root',
  template: `
  <div style="text-align:center">
    <h1>
      Lista de compras
    </h1>
  </div>
  <ul>
    <li *ngFor="let item of items">
      <h2>{{ item.name }}</h2>
    </li>
  </ul>
  {{ error?.message }}
  `
})
export class AppComponent implements OnInit {

  items: ShoppingItem[];
  error: any;

  constructor(private api: ApiService) { }

  ngOnInit() {
    this.api.getShoppingItems().subscribe(
      (items: ShoppingItem[]) => this.items = items,
      (error: any) => this.error = error
    );
  }
}

// app.module.ts

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';

import { ApiService } from './api.service';
import { AppComponent } from './app.component';


@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
  ],
  providers: [
    ApiService,
  ],
  bootstrap: [
    AppComponent,
  ],
})
export class AppModule { }
{{< / highlight >}}

Acessando [localhost:4200](http://localhost:4200) veremos nossa belíssima lista de comp...

![ops](/img/angular-django/ops.png)

![wtf](/img/memes/wtf.png)

Algo está errado com nossa aplicação, vamos dar uma olhada no console do browser:

![cors error](/img/angular-django/cors-error.png)

Ding! ding! ding! parece que encontramos o problema!

![xeroque holmes](/img/memes/xeroque-holmes.png)

# Cross-Origin Resource Sharing (CORS)

Por questões de segurança navegadores implementam a [política de mesma origem](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy), que impede que scripts executem requisições HTTP de origem cruzada, ou seja, requisições que possuam protocolo, domínio ou porta diferentes de seu host de origem.

Para habilitar requisições de origem cruzada de forma segura foi criado o [Mecanismo de Compartilhamento de Recursos entre Origens (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) que serve para permitir a comunicação entre um frontend e backend em domínios distintos, e também para requisições de fontes, imagens dentre outros recursos armazenados em uma CDN.

O mecanismo opera adicionando novos cabeçalhos **HTTP** que permitem que o servidor descreva o conjunto de origens que podem ler a informação através do navegador. Além de requerer que os navegadores "pré-enviem" requisições que podem causar efeitos colaterais ao servidor (como **POST**, **PUT** e **DELETE**) com uma requisição **OPTIONS** que depois de aprovada libera o envio da requisição verdadeira.

O navegador (por consequência nosso frontend também) já está pronto para lidar com este mecanismo, nos resta agora implementá-lo em no backend.

{{< highlight console >}}
$ pip install django-cors-headers
{{< / highlight >}}

Após instalado, basta adicionar as configurações no `settings.py`:

{{< highlight python >}}
INSTALLED_APPS = (
    ...
    'corsheaders',
)

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    ...
]

CORS_ORIGIN_WHITELIST = (
    'http://localhost:4200',
)
{{< / highlight >}}

{{% tip class="warning" %}}
Lembre-se de colocar o `CorsMiddleware` o mais a cima o possível na lista de middlewares para que ele consiga adicionar o cabeçalho na requisição antes que ela seja tratada pelo backend.
{{% /tip %}}

Em `CORS_ORIGIN_WHITELIST` colocamos somente a origem que queremos liberar o acesso que no nosso caso é `localhost:4200`, mas em ambiente de produção seria algo como `api.backend.com`.

Feito isso agora podemos ver que a comunicação entre frontend e backend está funcionando \o/.

![funcionou](/img/angular-django/it-works.png)

Agora para finalizar vamos adicionar um pouco de interatividade em nossa lista de compras:

{{< highlight typescript >}}
// api.service.ts

import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable()
export class ApiService {

  private apiRoot = 'http://localhost:8000/';

  constructor(private http: HttpClient) { }


  getShoppingItems() {
    return this.http.get(this.apiRoot.concat('shopping-item/'));
  }

  createShoppingItem(name: string, quantity: number) {
    return this.http.post(
      this.apiRoot.concat('shopping-item/'),
      { name, quantity }
    );
  }

  deleteShoppingItem(id: number) {
    return this.http.delete(this.apiRoot.concat(`shopping-item/${id}/`));
  }
}

//  app.component.ts

import { Component, OnInit } from '@angular/core';

import { ApiService } from './api.service';
import { ShoppingItem } from './shopping-item.interface';

@Component({
  selector: 'app-root',
  template: `
  <div style="text-align:center">
    <h1>
      Lista de compras
    </h1>
  </div>
  <ul>
    <li *ngFor="let item of items">
      <h2>{{ item.quantity }}x {{ item.name }}
      <button (click)="delete(item.id)">x</button></h2>
    </li>
  </ul>

  <input #itemQuantity type='text' placeholder='Qtd'>
  <input #itemName type='text' placeholder='Name'>
  <button (click)="add(itemName.value, itemQuantity.value)">Add</button>
  {{ error?.message }}
  `
})
export class AppComponent implements OnInit {

  items: ShoppingItem[];
  error: any;

  constructor(private api: ApiService) { }

  ngOnInit() {
    this.api.getShoppingItems().subscribe(
      (items: ShoppingItem[]) => this.items = items,
      (error: any) => this.error = error
    );
  }

  add(itemName: string, itemQuantity: number) {
    this.api.createShoppingItem(itemName, itemQuantity).subscribe(
      (item: ShoppingItem) => this.items.push(item)
    );
  }

  delete(id: number) {
    this.api.deleteShoppingItem(id).subscribe(
      (success: any) => this.items.splice(
        this.items.findIndex(item => item.id === id)
      )
    );
  }
}
{{< / highlight >}}

E agora sim, temos a lista de compras completa e funcionando:

![lista de compras](/img/angular-django/frontend-done.gif)

## Conclusão

Separar o frontend do backend em projetos distintos gera um pouco mais de trabalho em relação a manter tudo em um único projeto monolítico. Porém, depois de acostumar com as particularidades desta separação é que começam a surgir seus frutos como poder dar mais flexibilidade e liberdade aos times de desenvolvimento para utilizarem suas ferramentas e fluxos de desenvolvimento da forma que bem entenderem, além de retirar a carga de processamento de template de seu servidor de backend deixando-o focado na gestão e processamento dos seus dados e, talvez uma das maiores vantagens caso você tenha uma empresa, a possibilidade de poder contratar profissionais especializados em frontend e em backend para evoluir seu produto sem demandar de todos um conhecimento de fullstack.
