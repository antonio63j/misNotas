
<!-- TOC -->

- [angular-cli](#angular-cli)
- [mvn](#mvn)
- [npm](#npm)
- [npx](#npx)
- [instalación de angular-cli](#instalaci%C3%B3n-de-angular-cli)
    - [Actualizaciones de angular-cli](#actualizaciones-de-angular-cli)
- [Creación de un proyecto angular](#creaci%C3%B3n-de-un-proyecto-angular)
- [Directivas introducidas en la versión 16/17 de angular](#directivas-introducidas-en-la-versi%C3%B3n-1617-de-angular)
    - [La directiva @if](#la-directiva-if)
    - [Directivar @for](#directivar-for)
- [Propiedad binding](#propiedad-binding)
- [Eventos](#eventos)
- [Comunicación de los componenetes con @Input](#comunicaci%C3%B3n-de-los-componenetes-con-input)
- [Comunicación entre componentes con @Output](#comunicaci%C3%B3n-entre-componentes-con-output)
- [Vistas aplazadas Deferrable Views](#vistas-aplazadas-deferrable-views)
- [Formularios template-driven](#formularios-template-driven)
- [Formularios reactivos Reactive forms](#formularios-reactivos-reactive-forms)
- [Inyección de dependencias función inyect](#inyecci%C3%B3n-de-dependencias-funci%C3%B3n-inyect)
- [Construir un pipe](#construir-un-pipe)
- [Actualización de la aplicación restaurante a angular 17](#actualizaci%C3%B3n-de-la-aplicaci%C3%B3n-restaurante-a-angular-17)
    - [de 11 a 12](#de-11-a-12)
    - [de 12 a 13](#de-12-a-13)
    - [de 13 a 14](#de-13-a-14)
    - [de 14 a 15](#de-14-a-15)
    - [De 15 a 16](#de-15-a-16)
    - [de 16 a 17](#de-16-a-17)

<!-- /TOC -->
## angular-cli

Framework que facilita el desarrollo de proyectos angular.
Angluar-cli es una herramienta en línea de comandos bastante útil para trabajar con proyectos angular.

Angular-cli nos creará un proyecto básico o esqueleto, incluye el compilador typescript y más.

`Si no trabajasemos con angular-cli, podemos instalar el compilador con`

```consola
npm install –g typescript
npm install typescript
```

`Y luego los typings, que son librerías generales que se utilizan desde typescript`

```consola
npm install –g typings
```

## mvn

Con Node Version Manager (nvm) podemos seleccionar entre las diferentes versiones de nodejs que tengamos instaladas.

Para mostrar las versiones que tenemos instaladas

``` consola
Nvm list [available]
```

Para trabajar con una versión determinada

```consola
nvm use
```
## npm

Npm es parte de Nodejs, y tiene la función de instalar paquetes nodejs. Se instala con nodejs y tiene su propio repositorio de paquetes.
Cuando los ejecutables se instalan a través de paquetes npm, npm crea enlaces a ellos:

- las instalaciones locales tienen enlaces creados en el directorio ./node_modules/.bin/

- las instalaciones globales (-g) tienen enlaces creados desde el directorio global bin/ (por ejemplo: /usr/local/bin en Linux o %AppData%/npm en Windows)

Para ejecutar un paquete con npm tienes que escribir la ruta local, así:

```consola
./node_modules/.bin/tu-paquete
```

o puedes ejecutar un paquete instalado localmente agregándolo a tu archivo package.json en la sección de scripts, así:

```javascript
{
  "name": "tu-aplicacion",
  "version": "1.0.0",
  "scripts": {
    "tu-paquete": "tu-paquete"
  }
}

```

Luego puedes ejecutar el script usando npm run:

``` consola
npm run tu-paquete
```

## npx

npx viene con npm y con él podemos lanzar ejecutables node.js sin que estén instalados con npm.

Puedes ejecutar el siguiente comando para ver si ya está instalado para tu versión actual de npm:

```consola
 which npx

 /mnt/c/Program Files/nodejs/npx
```

Si no lo está, puede instalarlo así

```consola
 npm install -g npx
```

## instalación de angular-cli

Para instalar angular/cli global (debemos tener permisos de administrador):

```consola
npm install –g @angular/cli@latest
```

Local install (default): puts stuff in./node_modules of the current package root.
Global install (with-g): puts stuff in /usr/local or wherever node is installed.

para ver la versión global instalada

```console
ng  -version
```

Para ver la versión local instalada

```npm run – ng  –version (con angular-cli local)
npx ng –version (con angular cli local)
```

### Actualizaciones de angular-cli

En global

```consola
npm uninstall –g @angular-cli
npm cache clean [–force]
npm install –g @angular/cli@latest
```

En local

```consola
rm -rf node_modules
npm uninstall --save-dev angular-cli
npm install --save-dev @angular/cli@latest
npm install
```

## Creación de un proyecto angular

Crear proyecto con angular-cli global

```consola
ng new your-project-name --style=scss
```

Para crear un proyecto sin angular-cli global, podemos utilizar

``` consola
npx -p @angular/cli ng new my-app

npx -p @angular/cli[version] ng new my-app

```

npx instalará previamente angular-cli en local.

npx también se puede utilizar para ejecutar los binarios locales (en node_modules)

## Directivas introducidas en la versión 16/17 de angular

### La directiva @if

@if {

}
@else {

}

### Directivar @for

```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    @for (os of operatingSystems; track os.id) {
      {{ os.name }}
    }`,
  standalone: true,
})
export class AppComponent {
  operatingSystems = [{id: 'win', name: 'Windows'}, {id: 'osx',        name: 'MacOS'}, {id: 'linux', name: 'Linux'}];
}
```

## Propiedad binding

```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app-root',
  styleUrls: ['app.component.css'],
  template: `
    <div [contentEditable]="isEditable"></div>
  `,
  standalone: true,
})
export class AppComponent {
  isEditable = false;
}
```

## Eventos

```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <button (click)="greet()">Primary</button>
    <section (mouseover)="onMouseOver()">
      There's a secret message for you, hover to reveal 👀
      {{ message }}
    </section>
  `,
  standalone: true,
})

--------------------------------------

export class AppComponent {
  message = '';
  greet() {
        console.log('Hello, there 👋');
        this.message = 'pepe';
    }
  onMouseOver() {
    this.message = 'holas';
  }
}

```

## Comunicación de los componenetes con @Input

Comunicación hacia componenetes hijos.

```typescript
import {Component, Input} from '@angular/core';

@Component({
  selector: 'app-user',
  template: `<p>The user's name is</p> {{ occupation}}`,
  standalone: true,
})
export class UserComponent {
  @Input() occupation = '';
}

---------------------

import {Component} from '@angular/core';
import {UserComponent} from './user.component';

@Component({
  selector: 'app-root',
  template: `<app-user occupation="Angular Developer"><app-user/>`,
  standalone: true,
  imports: [UserComponent],
})
export class AppComponent {}

```

## Comunicación entre componentes con @Output

Comunicación de componentes hijos hacia los padres.

```typescript
import {Component, Output, EventEmitter} from '@angular/core';

@Component({
  selector: 'app-child',
  styles: `.btn { padding: 5px; }`,
  template: `
    <button class="btn" (click)="addItem()">Add Item</button>
  `,
  standalone: true,
})
export class ChildComponent {
  @Output() addItemEvent = new EventEmitter<string>();
  addItem() {
    this.addItemEvent.emit('it');
  }
}

-----------------------------

import {Component} from '@angular/core';
import {ChildComponent} from './child.component';

@Component({
  selector: 'app-root',
  template: `
    <app-child (addItemEvent)="addItem($event)" />
    <p>🐢 all the way down {{ items.length }}</p>
  `,
  standalone: true,
  imports: [ChildComponent],
})
export class AppComponent {
  items = new Array();
  addItem(item: string) {
    this.items.push(item);
  }
}

```

## Vistas aplazadas (Deferrable Views)

```typescript
import {Component} from '@angular/core';
import {CommentsComponent} from './comments.component';

@Component({
  selector: 'app-root',
  template: `
    <div>
      <h1>How I feel about Angular</h1>
  <article>
  <p>Angular is my favorite framework, and this is why. Angular has the coolest deferrable view feature that makes defer loading content the easiest and most ergonomic it could possibly be. The Angular community is also filled with amazing contributors and experts that create excellent content. The community is welcoming and friendly, and it really is the best community out there.</p>
  <p>I can't express enough how much I enjoy working with Angular. It offers the best developer experience I've ever had. I love that the Angular team puts their developers first and takes care to make us very happy. They genuinely want Angular to be the best framework it can be, and they're doing such an amazing job at it, too. This statement comes from my heart and is not at all copied and pasted. In act, I think I'll say these exact same things again a few times.....</p>
  <p>I can't express enough how much I enjoy working with Angular. It offers the best developer experience I've ever had. I love that the Angular team puts their developers first and takes care to make us very happy. They genuinely want Angular to be the best framework it can be, and they're doing such an amazing job at it, too. This statement comes from my heart and is not at all copied and pasted. In fact, I think I'll say these exact same things again a few times...</p>
  <p>Angular is my favorite framework, and this is why. Angular has the coolest deferrable view feature that makes defer loading content the easiest and most ergonomic it could possibly be. The Angular community is also filled with amazing contributors and experts that create excellent content. The community is welcoming and friendly, and it really is the best community out there.</p>
  <p>I can't express enough how much I enjoy working with Angular. It offers the best developer experience I've ever had. I love that the Angular team puts their developers first and takes care to make us very happy. They genuinely want Angular to be the best framework it can be, and they're doing such an amazing job at it, too. This statement comes from my heart and is not at all copied and pasted.</p>
</article>
      @defer (on viewport) {
         <comments>
         </comments>
      } @placeholder {
          <p>se ve antes que la carga diferida comience </p>
      } @loading (minimum 2s) {
        <p>Se ve mientras la carga diferida está en proceso (no ha finalizado todavía) </p>
      }

    </div>
  `,
  standalone: true,
  imports: [CommentsComponent],
})
export class AppComponent {}
## Actualización de restaurante-front desde la version 11 a la 17 de angular

-------------------------

import {Component} from '@angular/core';

@Component({
  selector: 'comments',
  template: `
    <ul>
      <li>Building for the web is fantastic!</li>
      <li>The new template syntax is great</li>
      <li>I agree with the other comments!</li>
    </ul>
  `,
  standalone: true,
})
export class CommentsComponent {}

```

## Formularios template-driven

```typescript

import {Component} from '@angular/core';
import {FormsModule} from '@angular/forms';

@Component({
  selector: 'app-user',
  template: `
    <label for="framework">
      Favorite Framework name:
      <input id="framework.nombre" type="text" [(ngModel)]="favoriteFramework.nombre" />
      Favorite Framework dni:
      <input id="framework.dni" type="text" [(ngModel)]="favoriteFramework.dni" />
    </label>
  `,
  standalone: true,
  imports: [FormsModule],
})
export class UserComponent {
  favoriteFramework = {nombre: 'afl', dni: 123} ;
}
```
## Formularios reactivos (Reactive forms)

```typescript 

import {Component} from '@angular/core';
import {FormGroup, FormControl} from '@angular/forms';
import {ReactiveFormsModule} from '@angular/forms';

@Component({
  selector: 'app-root',
  template: `
    <form [formGroup]="profileForm" (ngSubmit)="handleSubmit()">
      <input type="text" formControlName="name" />
      <input type="email" formControlName="email" />
      <button type="submit">Submit</button>
    </form>
  `,
  standalone: true,
  imports: [ReactiveFormsModule],
})
export class AppComponent {
  profileForm = new FormGroup({
    name: new FormControl(''),
    email: new FormControl(''),
  });

  handleSubmit() {
    alert(this.profileForm.value.name + ' | ' + this.profileForm.value.email);
  }
}
```

## Inyección de dependencias (función inyect())

```typescript

import {Injectable} from '@angular/core';

@Injectable({
  providedIn: 'root',
})
export class CarService {
  cars = ['Sunflower GT', 'Flexus Sport', 'Sprout Mach One'];

  getCars(): string[] {
    return this.cars;
  }

  getCar(id: number) {
    return this.cars[id];
  }
}

--------------------------------

import {Component, inject} from '@angular/core';
import {CarService} from './car.service';

@Component({
  selector: 'app-root',
  template: `
    <p>Car Listing: {{ display }}</p>
  `,
  standalone: true,
})
export class AppComponent {
  display = '';
  carService = inject(CarService);

  constructor() {
    this.display = this.carService.getCars().join(' ⭐️ ');
  }
}

```

## Construir un pipe

```typescript
import {Pipe, PipeTransform} from '@angular/core';

@Pipe({
  name: 'reverse',
  standalone: true,
})
export class ReversePipe implements PipeTransform {
  transform(value: string): string {
    let reverse = '';
    for (let i = value.length - 1; i >= 0; i--) {
      reverse += value[i];
    }
    return reverse;
  }
}
-----------------------------

import {Component} from '@angular/core';
import {ReversePipe} from './reverse.pipe';

@Component({
  selector: 'app-root',
  template: `Reverse Machine: {{ word | reverse }}`,
  standalone: true,
  imports: [ReversePipe],
})
export class AppComponent {
  word = 'You are a champion';
}

```

## Actualización de la aplicación restaurante a angular 17



### de 11 a 12

- Una vez hecho el clone desde el repositorio github.com, se ha tenido que utilizar node 12.22.12, para poder hacer el "npm install".

- Seguidos los pasos según la documentación de angular.io. Se ha tenido que forzar los updates con la opcción --force.
- A la hora de lanzar el front, daba el error

```console

- Generating server application bundles (phase: setup)...
- Generating browser application bundles (phase: setup)...
node:internal/crypto/hash:68
  this[kHandle] = new _Hash(algorithm, xofLen);
                  ^

Error: error:0308010C:digital envelope routines::unsupported

```

que se ha corregido con la variable de ambiente, el problema encotrado es que en ocasiones el valor que se asigna en la variable de entorno, se elimina, volviendo el error en el arranque de desarrollo.

```consola
 NODE_OPTIONS=--openssl-legacy-provider
```

- Errores detectados en ejecución

1. Error en formulario de envio de correo de contacto

### de 12 a 13

- Seguir las instrucciones de angular.io

```console
- ng update @nguniversal/express-engine@12
- ng update @nguniversal/express-engine@13
```

- Eliminar esta linea porque falla compilacion en carrito.service.ts

```consola
import { NullTemplateVisitor } from '@angular/compiler';
```

### de 13 a 14

- Seguimos las instrucciones de angular.io

```consola
ng update @angular/core@14 @angular/cli@14 --force

ng update @nguniversal/builders@14 --force
ng update @nguniversal/express-engine@14 --force
ng update @kolkov/angular-editor@2 --force
ng update @ng-bootstrap/ng-bootstrap@13 --force

@popperjs/core @ ^2.10.2 (pendiente solución)

npm uninstal @angular/flex-layout --force
npm install @angular/flex-layout@14.0.0-beta.39

ng update bootstrap (que actualiza a v5.3.2)

```

En 4 o 5 ficheros .scss se cambia

```consola
@import '~bootstrap/scss/bootstrap';
por
@import 'bootstrap/scss/bootstrap';
```

### de 14 a 15

- Referencia para la actualización web angular.io

Actualizacion a la versión 4.8 de typescript

```
ng update typescript@4.8.3
ng update @angular/core@15 @angular/cli@15 --force
ng update @angular/material@15 --force
ng update @ng-bootstrap/ng-bootstrap@14 --force
ng update @nguniversal/express-engine@15 --force
ng update @angular/flex-layout@15.0.0-beta.42

ng update bootstrap@5.2.3

ng generate @angular/material:mdc-migration

```

Con la migracion mdc-migration, se han modificaco los siguientes ficheros. En ficheros html, la modificación ha consistido en incluir la etiqueta:

```html
 <mat-card appearance="outlined" style="margin-bottom: 30px;">
```

```
UPDATE src/app/shared/componentes/filtro/dynamic-form/dynamic-form.component.ts (3537 bytes)
UPDATE src/app/pages-store/carta/carta.component.html (2163 bytes)
UPDATE src/app/pages-store/menu/menu.component.html (1223 bytes)
UPDATE src/app/pages-admin/admin-menu/admin-menu.component.html (2204 bytes)
UPDATE src/app/pages-admin/admin-pedido/admin-pedido-form/admin-pedido-form.component.html (6263 bytes)
UPDATE src/app/pages-admin/admin-sliders/admin-sliders.component.html (2614 bytes)
UPDATE src/app/pages-admin/admin-tipoplato/admin-tipoplato.component.html (2009 bytes)
UPDATE src/app/shared/componentes/filtro/input/input.component.ts (770 bytes)
UPDATE src/app/shared/componentes/filtro/button/button.component.ts (607 bytes)
UPDATE src/app/shared/componentes/filtro/select/select.component.ts (656 bytes)
UPDATE src/app/shared/componentes/filtro/date/date.component.ts (879 bytes)
UPDATE src/app/shared/componentes/filtro/radiobutton/radiobutton.component.ts (675 bytes)
UPDATE src/app/shared/componentes/filtro/checkbox/checkbox.component.ts (522 bytes)
UPDATE src/app/shared/componentes/filtro/date-range/date-range.component.ts (1190 bytes)
UPDATE src/app/shared/componentes/filtro/time/time.component.ts (1407 bytes)
UPDATE src/app/pages-store/carrito/carrito.component.html (7271 bytes)
UPDATE src/app/pages-store/carta/carta.component.scss (616 bytes)
UPDATE src/app/pages-admin/admin-pedido/admin-pedido.component.scss (616 bytes)
UPDATE src/app/shared/componentes/paginator/paginator.component.css (325 bytes)
UPDATE src/app/pages-store/carrito/carrito.component.scss (1196 bytes)
UPDATE src/app/pages-store/carrito/tramitar-carrito/tramitar-carrito.component.scss (817 bytes)
UPDATE src/styles.scss (2996 bytes)
UPDATE src/app/pages-admin/pages-admin.module.ts (4938 bytes)
UPDATE src/app/shared/modules/all-material-module.ts (4939 bytes)
UPDATE src/app/shared/componentes/filtro/filtro.module.ts (2899 bytes)
UPDATE src/app/pages-store/pages-store.module.ts (3833 bytes)
UPDATE src/app/app.module.ts (4852 bytes)
UPDATE src/app/usuarios/signup/signup.module.ts (1144 bytes)
UPDATE src/app/pages-store/contacto/contacto.module.ts (874 bytes)
UPDATE src/app/usuarios/perfil/perfil.module.ts (974 bytes)
UPDATE src/app/usuarios/login/login.module.ts (1342 bytes)
UPDATE src/app/shared/componentes/paginator/paginator.module.ts (573 bytes)
UPDATE src/app/dashboard/dashboard.module.ts (775 bytes)
```

### De 15 a 16

- Se siguen los pasos de angular.io para esta actualización.

```console
ng update typescript@4.9.3
ng update @angular/core@16 @angular/cli@16 --force
ng update @angular/material@16
ng update @ng-bootstrap/ng-bootstrap@15
ng update @nguniversal/express-engine@16
ng update bootstrap@5.3.2

ng update ngx-cookieconsent

ng update @ngx-translate/core @ngx-translate/http-loader
```

```typescript
 this.subscribe$.next();
```

da error y hay que cambiar la declaración 

```typescript
subscribe = new Subject();
```

a

```typescript
subscribe = new Subject<void>();
```

### de 16 a 17

- Se siguen los pasos de angular.io para esta actualización

```consola
npm uninstall typescript --force
npm install typescript@5
ng update zone.js --force

ng update @angular/core@17 @angular/cli@17 --force

ng update @ng-bootstrap/ng-bootstrap@16
ng update bootstrap@5.3.2
ng update @types/node

```

Se ha añadido en main.server.ts

```typescript
export { AppServerModule as default } from '../src/app/app.server.module'; 
```
