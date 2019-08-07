---
title: JWT Authentication with Angular and Django
description: Implement the JWT authentication model using Angular 6 for the frontend and Django 2 for the backend
publishDate: 2018-04-15
images:
  - /img/generics/jwt.jpg
tags: ["python", "django", "typescript", "angular", "api", "jwt", "auth"]
draft: false

---

Almost every system that runs on the internet and stores user data has an authentication layer. With the API architecture becoming popular nowadays, the complexity of the authentication layer also grew.

This post was made to explain the authentication process between a frontend written in **Angular 6** and a backend written in **Django 2** using the architecture proposed on my previous post ["Separating Frontend from Backend with Angular and Django"](/en/blog/separating-frontend-from-backend-with-angular-and-django/).

The repositories with this post code are:

- **Backend**: [https://github.com/humrochagf/post-jwt-backend](https://github.com/humrochagf/post-jwt-backend)
- **Frontend**: [https://github.com/humrochagf/post-jwt-frontend](https://github.com/humrochagf/post-jwt-frontend)


{{% tip class="info" %}}
**UPDATE (September 18, 2018)** Post updated and tested on Angular 6
{{% /tip %}}

{{% tip class="info" %}}
**UPDATE (July 5, 2019)** Post updated with the sign up process
{{% /tip %}}

## Authentication

There are many ways to implement an authentication system. The most famous and used across the internet is the user and password model, where you present a unique identifier (id, username, email or other) and a secret shared between you and the service that you want to access. After validating this pair, you get a **token** that works as a credential to give you access to your resources on that service.

A big part of backend frameworks comes with the authentication mechanic implemented if you use it on a **monolithic** system (where the same stack does the template rendering). On these cases, you only need to decide things like the token expiration time or the name of the variable for that holds the **token**.

However, when we separate the stacks, the authentication becomes less automatic, and you have to implement this connection.

There are many ways to do it, and it depends on your authentication method. In our case, the authentication method selected was the **JWT**.

## JWT

**JWT** stands for **JSON Web Token**, and it is an encoded **JSON** object defined by the [RFC 7519](https://tools.ietf.org/html/rfc7519) to perform access information exchange between two ends. It is encoded and signed with the following format:

{{< highlight text >}}
header.payload.signature
{{< / highlight >}}

At the **header** it is stored the metadata about the **token**, the type of algorithm used for the signature:

{{< highlight json >}}
{
  "alg": "HS256",
  "typ": "JWT"
}
{{< / highlight >}}

On the **payload**, there are the user information and metadata about the access grant like the **token expiration**:

{{< highlight json >}}
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
{{< / highlight >}}

Finally, the **signature** is the concatenation of the **header**, and the **payload**, both in **base 64** and united by a '.' (dot) hashed with the algorithm defined in the **header**, and a secret key defined by the server:

{{< highlight text >}}
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
{{< / highlight >}}

With the signature, it is possible to check if the **token** doesn't change during the exchange. It ensures its integrity and proves the authenticity of the source.

These three blocks joined by '.' (dot), each one encoded in **base 64** composes the **JWT Token**:

{{< highlight text >}}
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.PcmVIPbcZl9j7qFzXRAeSyhtuBnHQNMuLHsaG5l804A
{{< / highlight >}}

After confirming the user data and receiving the **JWT**, it should be stored, usually at **local storage** to use at the authenticated requests with the header scheme `JWT`:

{{< highlight text >}}
Authorization: JWT <token>
{{< / highlight >}}

This authentication mechanism is **stateless** and does not require session registration at the server database.

Now, let's go to the coding!

{{< videogif "/img/memes/coding-cat.webm" "/img/memes/coding-cat.gif" >}}

So for that, we use as starting point the application created at the previous post ["Separating Frontend from Backend with Angular and Django"](/en/blog/separating-frontend-from-backend-with-angular-and-django/).

## Backend

The first thing to do is to install `djangorestframework-jwt` package:

{{< highlight console >}}
$ pip install djangorestframework-jwt
{{< / highlight >}}

Following, add its configuration at the `settings.py` file:

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

After that add the login routes at the `urls.py`:

{{< highlight python >}}
from rest_framework_jwt.views import obtain_jwt_token, refresh_jwt_token

urlpatterns = [
    ...

    path('auth/login/', obtain_jwt_token),
    path('auth/refresh-token/', refresh_jwt_token),
]
{{< / highlight >}}

The default permission to the resources changed at the `settings.py` to `IsAuthenticated`, so if you try to access the shopping list you get the following result:

![denied](/img/angular-django/denied.png)

Now that the resource access is allowed to logged users only let's implement the authentication at the **frontend**.

## Frontend

Our first task on the **frontend** is to move the content of `app.component.ts` to a separate component named `list.component.ts` and to add its routes, so we can create a second component to place the login:

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
      Shopping list
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

  <button (click)="logout()">logout</button>
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
      Signup
    </h1>
  </div>

  <input #username type='text' placeholder='username'>
  <input #email type='text' placeholder='email'>
  <input #password1 type='password' placeholder='password1'>
  <input #password2 type='password' placeholder='password2'>
  <button (click)="signup(username.value, email.value, password1.value, password2.value)">signup</button>
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

To help us with the frontend implementation, we'll use two libraries:

{{< highlight console >}}
$ npm install -s moment
$ npm install -s jwt-decode
$ npm install -s @types/jwt-decode
{{< / highlight >}}

The [moment](https://momentjs.com/) library helps us with the task of handling time since we'll need to control the **token** expiration and renewal while the [jwt-decode](https://github.com/auth0/jwt-decode) library handles the **token** manipulation.

Now that we have them installed, let's start with the authentication **service** code:

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

The created service has three classes and one interface with the following responsibilities:

The `AuthService` class is the core of the authentication process. It implements the `login`, `logout`, `signup`, `refreshToken`, and other helper functions which helps to maintain the session at the system. The function `setSession` saves it on **local storage** while `getExpiration` makes the expiration math to be used by the `refreshToken` function to decide if its time to refresh the token. The pair `isLoggedIn` and `isLoggedOut` is used to check if the user is logged, and the **getter** function `token` returns the **JWT**, so we can use it to make the authenticated requests.

The class `AuthInterceptor` implements the Angular interceptor pattern. It intercepts every **HTTP** request to check if the user is logged and injects the `Authorization JWT <token>` header in the request to make the authenticated calls to the **API**.

The class `AuthGuart` work as a "shield" that stops unauthenticated access to the routes. If a not logged user tries to access the guarded route, it redirects to the login page.

Also, the `JWTPayload` interface is a TypeScript structure to define the **payload** format returned by the **JWT**.

After the service creation, let's add it to the app and define the guarded routes so we can finally call the authentication at the login:

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


With the app running, we can see the login process working \o/

![auth](/img/angular-django/auth.gif)

![it works](/img/memes/amazed-cat.jpg)

## Signup process

When working with authentication, another standard operation is the signup. The `djangorestframework-jwt` library doesn't have it out of the box, but there are two libraries that we can use to get this functionality and other things like social authentication (Github, Google, and others).

The libraries are [django-rest-auth](https://django-rest-auth.readthedocs.io/en/latest/) that provide the auth views, and [django-allauth](https://django-allauth.readthedocs.io/en/latest/) who is responsible for the signup, account validation, and social auth.

To include it at out app let's install it to the backend:

{{< highlight console >}}
$ pip install django-rest-auth django-allauth
{{< / highlight >}}

Then we add the following to the `settings.py`:

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

We disabled the `ACCOUNT_EMAIL_VERIFICATION` at this post so we can focus on the  JWT process, but on "real world" solutions the account verification step is essential to reduce cases of abuse on the system.

The variable `REST_USE_JWT` tells `rest_auth` to use JWT instead of a standard token.

Added the configuration its time to set the routes to the `urls.py`:

{{< highlight python >}}
urlpatterns = [
    ...,
    path('auth/login/', obtain_jwt_token), #  remove this line
    path('auth/', include('rest_auth.urls')),
    path('auth/signup/', include('rest_auth.registration.urls')),
    path('auth/refresh-token/', refresh_jwt_token),
]
{{< / highlight >}}

We remove the `auth/login/` because it already exists at the `rest_auth`.

Another point that worth mention is that the login and the signup views at `rest_auth` both return the JWT Token and the logged user instance, so it saves you from one request to retrieve user data after authenticating to the system.

Now that we have the signup ready at the backend its time to change the frontend:

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

With this little change, we can signup on the system. You can access `/sinup` at the frontend to check it.

## Conclusion

Some libraries implement authentication using *JWT* on Angular automatically but having it implemented worth the effort because it gives you the autonomy to adjust the authentication to your system needs during its lifetime.
