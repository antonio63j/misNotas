# SEO

Un desarrollo web con angular no es propicio para SEO, puesto que la mayor parte del código se renderiza en el front y por tanto no da lugar a que los motores de búsqueda puedan tener en cuenta la página web para ponerla en las primeras posiciones de la búsqueda de usuario. 

Para resolver esta situación obligaremos a la renderización en el servidor con angular universal.

### Angular universal 

Otras mejoras de la renderización en el servidor son:

- Mejora la carga de la primera página
- Mejor rendimiento en móviles y dispositivos lentos o con un velocidad baja en internet. 

##### Instalación de angular universal en un proyecto angular

npm i @nguniversal/express-engine

que cambiará la estructura y modificará el contenido de algunos ficheros existentes del proyecto:

```
CREATE src/main.server.ts (907 bytes)
CREATE src/app/app.server.module.ts (318 bytes)
CREATE tsconfig.server.json (396 bytes)
CREATE server.ts (2046 bytes)
UPDATE angular.json (5671 bytes)
UPDATE src/main.ts (547 bytes)
UPDATE src/app/app.module.ts (4147 bytes)
UPDATE package.json (2662 bytes)

```

Y en package.json vemos que se han incorporado:

En la parte de script:

```
  "dev:ssr": "ng run publi-restaurante:serve-ssr",

  "serve:ssr": "node dist/publi-restaurante/server/main.js",

  "build:ssr": "ng build && ng run publi-restaurante:server",

  "prerender": "ng run publi-restaurante:prerender"
```



y la parte de paquetes:

```
"@angular/platform-server": "^13.0.1",

 "@nguniversal/express-engine": "^13.0.1",

"express": "^4.15.2",
```

Ahora para ejecutar la aplicación en localhost, deberemos hacer:

npm run --ng dev:ssr

Dado que la renderización se hace en el lado del servidor y no en la del navegador, y dado que el servidor no tiene APIs por ejemplo para manejar el DOM (document, window,...), tendremos que de alguna forma, salvar este inconveniente.

##### Para deploy en producción:

En server.js (creado al instalar universal), asignamos el puerto en el que estará escuchando dist/metarestaurante-front/server/main.js

generamos producción con **npm run --ng build:ssr** 

Tendremos en el servidor un proceso a la escucha de peticiones, este es justamente el **main.js**, este es el proceso que deberemos indicar

al crear el servicio:



Configuar nginx:



