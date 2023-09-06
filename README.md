# Laravel / Inertia con Svelte

Este repositorio tiene El andamio con Larvel, Inertiajs y Svelte para que lo puedas usar, sin embargo, si deseas conocer y realizar la instalación manual, aqui te dejo toda la guía.

Recuerda que debes tener instalado previamente **"composer"**. Hay  muchos tutoriales disponibles para ello.
Crea una carpeta en cualquier lugar de windows.

## Pasos de instalacion Laravel / inertia / svelte

## Laravel

1. abrir una Terminal (cmd, gitbash o cualquiera) en la carpeta elegida para el proyecto.
2. instalamos Laravel con el comando:

~~~shell
composer create-project laravel/laravel [nombre-app]
~~~

Esto deberia instalar Laravel sin novedades, eso toma unos segundos o minutos dependiendo de las caracateristicas de tu máquina.
Despues de la instalación, Ingrese a la carpeta del proyecto y luego suba el servidor de la siguiente manera:

~~~shell
cd nombre-app
php artisan serve
~~~

ahora abre el link en un browser.  Suele ser: [http://127.0.0.1:8000](http://127.0.0.1:8000)
y listo, ya tenemos Laravel instalado que es nuestra instalación base.

## Inertia js - Server Side

Ahora pasamos a instalar **Inertia Js** que hará las veces de "interface" entre ***laravel*** y ***Svelte***.

- Debe asegurarse de estar dentro de la carpeta del proyecto y luego desde allí instalar el lado del servidor *(Server-Side Setup)* con la siguiente instrucción en la Terminal:

~~~php
composer require inertiajs/inertia-laravel
~~~

esto llevará un tiempo dependiendo de tu máquina, "ten paciencia".

- si todo va bien, es hora de abrir nuestro proyecto en un editor de codigo, en mi caso usaré vscode, asi que ahi en la terminal desde la carpeta del proyecto digitas:

~~~shell
code .
~~~

y se abrirá vscode con la estructura del proyecto.
*(Si deseas puedes cerrar la terminal y seguir desde vscode abriendo una terminal desde el editor)*

el primer archivo que editaremos será **"web.php"** que se encuentra en la carpeta **"routes"**.  (*routes/web.php*)
alli reemplazaremos todo el codigo por la siguiente plantilla:

~~~php
<?php

use Illuminate\Support\Facades\Route;
use Inertia\Inertia;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "web" middleware group. Make something great!
|
*/

Route::get('/', function () {

    return inertia::render('Home');

});
~~~

- Ahora vas a la siguiente ruta del proyecto: **"resources/views"**, allí puedes borrar el archivo existente: **"welcome.blade.php"** y creas uno nuevo con el nombre **"app.blade.php"** que será el archivo que inertia buscara como entrada.

- dentro de ese archivo copias la siguiente plantilla:

~~~php
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    @vite('resources/js/app.js')
    @inertiaHead
  </head>
  <body>
    @inertia
  </body>
</html>
~~~

Esta plantilla incluye los activos, así como las directivas **@inertia** y **@inertiaHead** que se utilizarán para cargar los recursos de su sitio (*CSS y JavaScript*). También contendrá una raíz en la que se iniciará su aplicación JavaScript por lo que de forma predeterminada, el adaptador Laravel de Inertia asumirá que su plantilla raíz se llama **app.blade.php**

### Inertia Middleware

Ahora abrimos una terminal desde vscode e instalaremos el Middleware de Inertia con la siguiente instrucción:

~~~shell
php artisan inertia:middleware
~~~

ésta es muy rápida ! 🏃‍♂️✈️

- Una vez que se haya publicado el middleware, registre el middleware HandleInertiaRequests en la ruta **App\Http\Kernel** como el último elemento de su grupo de middleware web:

~~~php
'web' => [
  // ... 
  // otros
  // ...
  \App\Http\Middleware\HandleInertiaRequests::class,
],
~~~

Es importante que lo agregue de úlimo en el "**middlewareGroups.web**".
Ya tenemos listo el Side-Server.

## Inertia js Client Side

Ahora instalemeos el lado del cliente (Cliente-Side Setup).

- para ello ingresamos la siguiente instrucción en la consola:
  
~~~shell
npm install @inertiajs/svelte
~~~

- A continuación, actualice su archivo JavaScript principal para iniciar su aplicación Inertia. Para lograr esto, inicializaremos el marco del lado del cliente con el componente base Inertia. El archivo lo encuentra en la ruta: **"resources/js/app.js"** y pegue allí la siguiente plantilla:

~~~php
import './bootstrap';

import { createInertiaApp } from '@inertiajs/svelte'

createInertiaApp({
  resolve: name => {
    const pages = import.meta.glob('./Pages/**/*.svelte', { eager: true })
    return pages[`./Pages/${name}.svelte`]
  },
  setup({ el, App, props }) {
    new App({ target: el, props })
  },
})
~~~

esto tiene lo necesario para inicializar el marco del lado del cliente, incluido el componente raíz de la aplicación Inertia.

Ya casi terminamos; ahora nos faltaría instalar el plugin **@sveltejs/vite-plugin-svelte**.

- para ello ingresamos en la terminal la siguiente instrucción:

~~~shell
npm install --save-dev @sveltejs/vite-plugin-svelte
~~~

- luego importelo y agreguelo al archivo de configuración de **vite**, éste archivo debe verlo en la raiz del proyecto con el nombre **"vite.config.js"** y debe verse así:

~~~js
//vite.config.js

import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import { svelte } from '@sveltejs/vite-plugin-svelte';

export default defineConfig({
  plugins: [
    laravel({
      input: ['resources/css/app.css', 'resources/js/app.js'],
      refresh: true,
    }),
    svelte({
      /* plugin options */
    })
  ],
});
~~~

- Por ultimo vamos a crear nuestro primer componente Svelte para testear la instalación. Para ello vamos a la carpeta **"resources/js"** y dentro de ella creamos una carpeta **"pages"** y dentro pages creamos el componente: **"Home.svelte"**.
- Hecho lo anterior, el componente debe verse en la ruta: ***resources/js/pages/Home.svelte***

- ahora dentro del archivo svelte usemos la siguiente plantilla:

~~~js
//Home.svelte

<script>
  let saludo = 'Hola desde Inertia con Laravel y Svelte'
</script>

<h1>{saludo}</h1>
~~~

- vamos a probar nuestra aplicación y para ello arrancamos nuestro servidor de cliente ingresando en la terminal:

~~~shell
npm run dev
~~~

- abrimos otra terminal más y alli ingresamos:

~~~shell
php artisan serve
~~~

___
***y Voilà !***
___

Deberías ver nuestro Saludo de prueba:
**'Hola desde Inertia con Laravel y Svelte'**

- y asi tenemos a Laravel 10 disponible para backend,
- y Svelte para el frontend juntos como un proyecto monolitico.

Adicionalmente, si deseas usar Tailwind, puedes seguir el [manual de instalación de la pagina oficial de Tailwind para Laravel](https://tailwindcss.com/docs/guides/laravel), funciona a la perfección.

Saludos y Espero te sea de utilidad.

## *Orlando Camacho*
