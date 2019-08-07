---
title: Autenticação JWT com Angular e Django
description: Implemente um modelo de autenticação JWT com frontend em Angular 6 e um backend de api em Django 2
publishDate: 2018-04-15
images:
  - /img/generics/jwt.jpg
slug: autenticacao-jwt-com-angular-e-django
tags: ["python", "django", "typescript", "angular", "api", "jwt", "auth"]
aliases: [
  "/blog/autenticacao-jwt-com-angular-e-django"
]
draft: false

---

Quase todo sistema que opera na internet e armazena dados de usuário hoje em dia depende de uma camada de autenticação. E com o crescimento do modelo de API`s e separação do **frontend** do **backend** a implementação desta camada cresceu em complexidade.

Esta postagem tem como foco mostrar o caminho das pedras em uma stack de frontend **Angular 6** e backend **Django 2** utilizando o modelo de separação discutido na postagem ["Separando o Frontend do Backend com Angular e Django"](/blog/separando-o-frontend-do-backend-com-angular-e-django/).

Os repositórios com o código desta postagem são:

- **Backend**: [https://github.com/humrochagf/post-jwt-backend](https://github.com/humrochagf/post-jwt-backend)
- **Frontend**: [https://github.com/humrochagf/post-jwt-frontend](https://github.com/humrochagf/post-jwt-frontend)

{{% tip class="info" %}}
**UPDATE (18/09/2018)** Postagem atualizada e testada para utilizar Angular 6
{{% /tip %}}

{{% tip class="info" %}}
**UPDATE (05/07/2019)** Postagem atualizada com o processo de cadastro
{{% /tip %}}

## Autenticação

Existem diversas formas de se autenticar em um sistema. A mais famosa e adotada pela internet como padrão é o modelo de usuário e senha, onde apresentamos nosso identificador (id, nome de usuário, email ou semelhante) e um segredo compartilhado com o serviço que queremos acessar, que após sua validação, nos é devolvido um **token** que funciona como um bilhete de acesso aos nossos recursos pessoais naquele serviço.

Quando implementamos sistemas monolíticos onde o **backend** e o **frontend** estão juntos esta manutenção deste **token** acontece de forma automática pela maioria das bibliotecas de autenticação disponíveis por aí. No máximo decidimos coisas como tempo de validade da sessão ou onde o **token** será armazenado trocando uma variável de configuração ou outra.

Porém, ao separarmos o **backend** do **frontend** esta gestão fica um pouco mais por nossa conta as decisões sobre o que fazer e usar para isso deve ser tomadas por nós durante a implementação. E existem enumeras formas de se fazer esta gestão  de acesso, uma delas é simplesmente utilizar o **token** padrão devolvido pelo sistema, mas neste caso como eles estão separados perdemos a possibilidade de atualizar a validade deste **token** nos obrigando a colocar novamente a senha para obter um novo acesso.

Para nos auxiliar com estes problemas utilizaremos o **JWT**.

## JWT

**JWT** ou **JSON Web Token** nada mais é que um objeto **JSON** definido na [RFC 7519](https://tools.ietf.org/html/rfc7519) para realizar transferência informação de permissões de acesso entre duas pontas. Ele é codificado e assinado e possuí o seguinte formato:

{{< highlight text >}}
header.payload.signature
{{< / highlight >}}

No **header** (cabeçalho) ficam os dados do **token**, que informam seu tipo e o algoritmo utilizado em sua assinatura:

{{< highlight json >}}
{
  "alg": "HS256",
  "typ": "JWT"
}
{{< / highlight >}}

No **payload** (carga) ficam os dados do usuário e alguns metadados como a expiração do **token**:

{{< highlight json >}}
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
{{< / highlight >}}

Finalmente na **signature** (assinatura) os dados de **header** e **payload** codificados em **base 64** e unidos por `.` (ponto) para serem assinados usando o algoritmo definido no **header**:

{{< highlight text >}}
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
{{< / highlight >}}

Com a assinatura é possível verificar se o **token** não foi alterado no caminho, garantindo sua integridade. Com ela também é possível confirmar a autenticidade de sua fonte.

Estes três blocos unidos por `.` (ponto) cada um codificado em **base 64** compõem o **JWT Token**:

{{< highlight text >}}
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.PcmVIPbcZl9j7qFzXRAeSyhtuBnHQNMuLHsaG5l804A
{{< / highlight >}}

Após confirmar os dados de usuário e receber o **JWT** ele deve ser armazenado, normalmente em **local storage** para ser utilizado nas requisições autenticadas usando o esquema de cabeçalho `JWT`:

{{< highlight text >}}
Authorization: JWT <token>
{{< / highlight >}}

Este é um mecanismo de autenticação que não guarda estado, ou seja, não requer armazenamento de dados de sessão no banco de dados do servidor.

Agora, com todo este conhecimento é hora da implementação!!!

{{< videogif "/img/memes/coding-cat.webm" >}}

E para isso, utilizaremos como base a aplicação desenvolvida na postagem anterior ["Separando o Frontend do Backend com Angular e Django"](/blog/separando-o-frontend-do-backend-com-angular-e-django/).

## Backend

A primeira coisa que faremos é instalar o pacote `djangorestframework-jwt`:

{{< highlight console >}}
$ pip install djangorestframework-jwt
{{< / highlight >}}

Em seguida iremos adicionar suas configurações no `settings.py`:

{{< highlight python >}}
from datetime import timedelta

# REST Framework settings

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ),
    'NON_FIELD_ERRORS_KEY': 'global',
}

# JWT settings

JWT_AUTH = {
    'JWT_ALLOW_REFRESH': True,
    'JWT_EXPIRATION_DELTA': timedelta(days=2),
}
{{< / highlight >}}

Seguido das rotas de login e atualização de **token jwt** em `urls.py`:

{{< highlight python >}}
from rest_framework_jwt.views import obtain_jwt_token, refresh_jwt_token

urlpatterns = [
    ...

    path('auth/login/', obtain_jwt_token),
    path('auth/refresh-token/', refresh_jwt_token),
]
{{< / highlight >}}

Como foi alterada a permissão padrão do sistema no `settings.py` para `IsAuthenticated` ao tentar acessar a lista de compra nos deparamos com a seguinte mensagem:

![denied](/img/angular-django/denied.png)

Agora que o acesso está sendo restrito a usuários logados chegou a hora de implementar a autenticação no **frontend**.

## Frontend

A primeira tarefa a ser executada no **frontend** é a de mover o conteúdo de `app.component.ts` para um componente separado nomeado como `list.component.ts` e adicionar rotas na aplicação, criando um segundo componente para o login:

{{< highlight typescript >}}
// list.component.ts

import { Component, OnInit } from '@angular/core';

import { ApiService } from './api.service';
import { ShoppingItem } from './shopping-item.interface';

@Component({
  selector: 'app-list',
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
  <p>{{ error?.message }}</p>
  <p *ngIf="error">{{ error?.error | json }}</p>

  <button (click)="logout()">deslogar</button>
  `
})
export class ListComponent implements OnInit {

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
      (item: ShoppingItem) => this.items.push(item),
      (error: any) => this.error = error
    );
  }

  delete(id: number) {
    this.api.deleteShoppingItem(id).subscribe(
      (success: any) => this.items.splice(
        this.items.findIndex(item => item.id === id)
      ),
      (error: any) => this.error = error
    );
  }
}

// app.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <router-outlet></router-outlet>
  `
})
export class AppComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }
}

// login.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-login',
  template: `
  <div style="text-align:center">
    <h1>
      Login
    </h1>
  </div>

  <input #username type='text' placeholder='username'>
  <input #password type='password' placeholder='password'>
  <button (click)="login(username.value, password.value)">login</button>
  <p>{{ error?.message }}</p>
  <p *ngIf="error">{{ error?.error | json }}</p>
  `
})
export class LoginComponent implements OnInit {

  error: any;

  constructor() { }

  ngOnInit() {
  }

  login(username: string, password: string) {
    // TODO: call login
  }
}

// signup.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-signup',
  template: `
  <div style="text-align:center">
    <h1>
      Cadastrar
    </h1>
  </div>

  <input #username type='text' placeholder='username'>
  <input #email type='text' placeholder='email'>
  <input #password1 type='password' placeholder='password1'>
  <input #password2 type='password' placeholder='password2'>
  <button (click)="signup(username.value, email.value, password1.value, password2.value)">cadastrar</button>
  <p>{{ error?.message }}</p>
  <p *ngIf="error">{{ error?.error | json }}</p>
  `
})
export class SignupComponent implements OnInit {

  error: any;

  constructor() { }

  ngOnInit() {
  }

  signup(username: string, email: string, password1: string, password2: string) {
    // TODO: call signup
  }
}

// app-routing.module.ts

import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { ListComponent } from './list.component';
import { LoginComponent } from './login.component';
import { SignupComponent } from './signup.component';

const routes: Routes = [
  { path: '', redirectTo: 'login', pathMatch: 'full' },
  { path: 'login', component: LoginComponent },
  { path: 'signup', component: SignupComponent },
  { path: 'list', component: ListComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }

// app.module.ts

...
import { AppRoutingModule } from './app-routing.module';
import { ListComponent } from './list.component';
import { LoginComponent } from './login.component';
import { SignupComponent } from './signup.component';
...

@NgModule({
  declarations: [
    AppComponent,
    ListComponent,
    LoginComponent,
    SignupComponent,
  ],
  imports: [
    ...

    AppRoutingModule,
  ],
  ...
})
export class AppModule { }
{{< / highlight >}}

Para auxiliar na implementação do **frontend** utilizaremos duas bibliotecas:

{{< highlight console >}}
$ npm install -s moment
$ npm install -s jwt-decode
$ npm install -s @types/jwt-decode
{{< / highlight >}}

A biblioteca [moment](https://momentjs.com/) facilitará o trabalho com tempo, já que precisamos controlar a expiração do **token** e sua renovação enquanto a biblioteca [jwt-decode](https://github.com/auth0/jwt-decode) cuidará do **token** em si.

Com as bibliotecas em mãos iniciaremos pelo **service** de autênticação:

{{< highlight typescript >}}
// auth.service.ts

import { Injectable } from '@angular/core';
import { HttpClient, HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { CanActivate, Router } from '@angular/router';

import { Observable } from 'rxjs';
import { tap, shareReplay } from 'rxjs/operators';

import * as jwtDecode from 'jwt-decode';
import * as moment from 'moment';

import { environment } from '../environments/environment';

@Injectable()
export class AuthService {

  private apiRoot = 'http://localhost:8000/auth/';

  constructor(private http: HttpClient) { }

  private setSession(authResult) {
    const token = authResult.token;
    const payload = <JWTPayload> jwtDecode(token);
    const expiresAt = moment.unix(payload.exp);

    localStorage.setItem('token', authResult.token);
    localStorage.setItem('expires_at', JSON.stringify(expiresAt.valueOf()));
  }

  get token(): string {
    return localStorage.getItem('token');
  }

  login(username: string, password: string) {
    return this.http.post(
      this.apiRoot.concat('login/'),
      { username, password }
    ).pipe(
      tap(response => this.setSession(response)),
      shareReplay(),
    );
  }

  signup(username: string, email: string, password1: string, password2: string) {
    // TODO: implement signup
  }

  logout() {
    localStorage.removeItem('token');
    localStorage.removeItem('expires_at');
  }

  refreshToken() {
    if (moment().isBetween(this.getExpiration().subtract(1, 'days'), this.getExpiration())) {
      return this.http.post(
        this.apiRoot.concat('refresh-token/'),
        { token: this.token }
      ).pipe(
        tap(response => this.setSession(response)),
        shareReplay(),
      ).subscribe();
    }
  }

  getExpiration() {
    const expiration = localStorage.getItem('expires_at');
    const expiresAt = JSON.parse(expiration);

    return moment(expiresAt);
  }

  isLoggedIn() {
    return moment().isBefore(this.getExpiration());
  }

  isLoggedOut() {
    return !this.isLoggedIn();
  }
}

@Injectable()
export class AuthInterceptor implements HttpInterceptor {

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = localStorage.getItem('token');

    if (token) {
      const cloned = req.clone({
        headers: req.headers.set('Authorization', 'JWT '.concat(token))
      });

      return next.handle(cloned);
    } else {
      return next.handle(req);
    }
  }
}

@Injectable()
export class AuthGuard implements CanActivate {

  constructor(private authService: AuthService, private router: Router) { }

  canActivate() {
    if (this.authService.isLoggedIn()) {
      this.authService.refreshToken();

      return true;
    } else {
      this.authService.logout();
      this.router.navigate(['login']);

      return false;
    }
  }
}

interface JWTPayload {
  user_id: number;
  username: string;
  email: string;
  exp: number;
}
{{< / highlight >}}

O **service** criado possuí 3 classes e 1 interface das quais suas funções são as seguintes:

A classe `AuthService` é a responsável por no autenticar de fato no sistema. Ela implemanta as funções de `login`, `logout` e `refreshToken` para fazer a manutenção da sessão no sistema, auxiliada das funções `setSession` que salva a sessão em **local storage**, `getExpiration` que realiza o cálculo de expiração para a função `refreshToken` decidir se é hora de atualizar o token ou não, os pares `isLoggedIn` e `isLoggedOut` que são utilizados para verificar se o usuário está logado e o **getter** `token` que retorna o **JWT** para ser utilizado nas requisições autenticadas.

A classe `AuthInterceptor` implementa os interceptadores do Angular, que neste caso intercepta todas as requisições **http** realizadas e, caso o usuário esteja logado, injeta o cabeçalho `Authorization JWT <token>` na requisição para realizar chamadas autenticadas na **API**.

A classe `AuthGuard` serve como um escudo que impede o acesso de usuário não logado nas rotas em que ela for vinculada, se um usuário não logado tenta acessar determinada rota protegida por ela, o mesmo será redirecionado para a tela de login.

E por fim a interface `JWTPayload` que serve somente para definirmos o formato do **payload** retornado no **JWT** dentro do typescript.

Após a criação do **service** vamos adicioná-lo na aplicação, definir as rotas protegidas e finalmente realizar a chamada de autenticação no componente de **login**:

{{< highlight typescript >}}
// app.module.ts

import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { AuthService, AuthInterceptor, AuthGuard } from './auth.service';

@NgModule({
  ...
  providers: [
    ...
    AuthService,
    AuthGuard,
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true,
    },
  ],
})
export class AppModule { }

// app-routing.module.ts

import { AuthGuard } from './auth.service';
...

const routes: Routes = [
  ...
  { path: 'list', component: ListComponent, canActivate: [AuthGuard] },
];

// login.component.ts

import { Router } from '@angular/router';
import { AuthService } from './auth.service';

...
export class LoginComponent implements OnInit {

  error: any;

  constructor(
    private authService: AuthService,
    private router: Router,
  ) { }

  ngOnInit() {
  }

  login(username: string, password: string) {
    this.authService.login(username, password).subscribe(
      success => this.router.navigate(['list']),
      error => this.error = error
    );
  }
}
{{< / highlight >}}

Com a aplicação rodando agora podemos visualizar processo de login funcionando \o/:

{{< videogif "/img/angular-django/auth.webm" >}}

![it works](/img/memes/amazed-cat.jpg)

## Processo de Cadastro

Uma outra operação bastante comum ao lidar com autenticação é o processo de cadastro de novas contas no sistema. A biblioteca `djangorestframework-jwt` não nos fornece esta funcionalidade pronta, para isso será preciso criar a própria view de cadastro. Porém existem duas bibliotecas que usadas em conjunto nos fornecem não somente esta funcionalidade como também suporte a validação de conta por email e cadastro e login com outros métodos como cadastro com contas de redes sociais (Github, Google entre outros).

Estas bibliotecas são [django-rest-auth](https://django-rest-auth.readthedocs.io/en/latest/) responsável por disponibilizar as de views de autênticação e cadastro. E [django-allauth](https://django-allauth.readthedocs.io/en/latest/) responsável pela estrutura de cadastro, validação de conta e autênticação social.

Para incluir o processo de cadastro em nossa aplicação vamos instalar as bibliotecas no backend:

{{< highlight console >}}
$ pip install django-rest-auth django-allauth
{{< / highlight >}}

Em seguida adicionamos as seguintes configurações no `settings.py`:

{{< highlight python >}}
INSTALLED_APPS = [
    ...,
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'rest_auth',
    'rest_auth.registration',
]

# allauth

SITE_ID = 1
ACCOUNT_EMAIL_VERIFICATION = 'none'

# JWT settings

REST_USE_JWT = True
{{< / highlight >}}

Desabilitamos o `ACCOUNT_EMAIL_VERIFICATION` nesta postagem pois estamos focados nos fluxos que envolvem a manipulação do JWT Token no processo de autenticação, em aplicações no "mundo real" processos de verificação de conta são importantes para mitigar abusos em seu sistema.

A variável `REST_USE_JWT` informa ao `rest_auth` que utilizaremos o JWT ao invés do Token padrão.

Adicionadas as configurações é hora de configurar as rotas do backend em `urls.py`:

{{< highlight python >}}
urlpatterns = [
    ...,
    path('auth/login/', obtain_jwt_token), #  remova esta linha
    path('auth/', include('rest_auth.urls')),
    path('auth/signup/', include('rest_auth.registration.urls')),
    path('auth/refresh-token/', refresh_jwt_token),
]
{{< / highlight >}}

Nesta configuração removemos a rota `auth/login/` pois ela já existe nas rotas do `rest_auth`.

Um outro ponto interesante é que tanto o login quanto o cadastro no `rest_auth` retornam junto com o JWT Token uma instância do usuário logado, o que pode lhe poupar uma requisição a sua api para recuperar os dados de usuário após essas operações.

Agora que temos a funcionalidade de cadastro pronta no backend, vamos às alterações de frontend:

{{< highlight typescript >}}
// auth.service.ts

...
  signup(username: string, email: string, password1: string, password2: string) {
    return this.http.post(
      this.apiRoot.concat('signup/'),
      { username, email, password1, password2 }
    ).pipe(
      tap(response => this.setSession(response)),
      shareReplay(),
    );
  }
...

// signup.component.ts

import { Router } from '@angular/router';
import { AuthService } from './auth.service';

...
export class SignupComponent implements OnInit {

  error: any;

  constructor(
    private authService: AuthService,
    private router: Router,
  ) { }

  ngOnInit() {
  }

  signup(username: string, email: string, password1: string, password2: string) {
    this.authService.signup(username, email, password1, password2).subscribe(
      success => this.router.navigate(['list']),
      error => this.error = error
    );
  }
}
{{< / highlight >}}

Com essa pequena alteração agora é possível realizar cadastros no sistema acessando o `/signup` do seu frontend.

## Conclusão

Já existem bibliotecas no Angular que implementam a autenticação usando **JWT** automaticamente, porém, compreender o funcionamento deste mecanismo que estará presente durante toda a vida útil da sua aplicação e ter a autonomia para fazer os ajustes necessários de acordo com suas necessidades faz com que uma implementação um pouco mais manual valha a pena o esforço.
