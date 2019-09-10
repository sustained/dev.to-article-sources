> Something missing or incorrect? This [article's source is on Github](https://github.com/sustained/dev.to-article-sources/).
> 
> Please feel free to [open an issue](https://github.com/sustained/dev.to-article-sources/issues/new) or [send a PR](https://github.com/sustained/dev.to-article-sources/compare).

# Table of Contents

- [Introduction](#introduction)
- [Reasons to use Vue and Laravel together](#reasons-to-use-vue-and-laravel-together)
    - [Scaffolded by default](#scaffolded-by-default)
    - [Laravel Mix](#laravel-mix)
    - [Widespread community adoption](#widespread-community-adoption)
- [Common gotchas when using Vue and Laravel together](#common-gotchas-when-using-vue-and-laravel-together)
    - [Blade and Vue interpolation syntax](#blade-and-vue-interpolation-syntax)
    - [Passing PHP variables as Vue component props](#passing-php-variables-as-vue-component-props)
    - [Laravel router and vue-router](#laravel-router-and-vuerouter)
        - [Setting it all up](#setting-it-all-up)
            - [Install vue-router](#install-vuerouter)
            - [Create a router instance and some routes](#create-a-router-instance-and-some-routes)
            - [Create a simple App component](#create-a-simple-app-component)
            - [Create a few page components](#create-a-few-page-components)
            - [Configure the root Vue instance](#configure-the-root-vue-instance)
            - [Configure the Laravel router](#configure-the-laravel-router)
            - [Create the controller and action](#create-the-controller-and-action)
            - [Create the view](#create-the-view)
    - [Lack of Webpack aliases when using Laravel Mix](#lack-of-webpack-aliases-when-using-laravel-mix)
- [Thanks and good bye](#thanks-and-good-bye)

# Introduction

Recently the excellent Vue Community guide was released. A guide on all things Vue - written for the Vue community, by the Vue community.

- [Github](https://github.com/dobromir-hristov/vuecommunity)
- [Website](https://vue-community.org/)

This article's stuture will mirror exactly the layout of [the Laravel section of the Vue Community guide](https://vue-community.org/guide/learning/how-to-learn-vue.html#laravel), heading for heading.

The intent is to provide a living resource (i.e. it will be updated over time if/when necessary) that goes into far greater detail (including code samples/demos) than the guide (which is intended to be more concise and easier to digest).

[Back to top](#table-of-contents)

# Reasons to use Vue and Laravel together

## Scaffolded by default

A default install of Laravel [has everything you need](https://laravel.com/docs/frontend#writing-vue-components) to begin enhancing your server-rendered (Blade) templates with Vue components. No setup necessary.

After having created a Laravel project, you'll be provided with:

- `bootstrap.js`:
  - loads some libraries such as lodash, axios and Popper
  - configures axios for CSRF
- `app.js`:
  - makes Vue globally available
  - registers the `ExampleComponent.vue`
  - provides example code on how to automatically register Vue components
  - initialises Vue
- `ExampleComponent.vue`

You are of course free to customise these files to your needs such as by removing unneeded/unwanted libraries.

[Back to top](#table-of-contents)

## Laravel Mix

[Laravel Mix](https://laravel.com/docs/mix)--a fluid API on top of Webpack, also included in the box--takes much of the pain out of asset compiling for you, so you can focus on authoring your Vue components.

You simply need to run `npm run watch` and then you can get straight to work on writing your Vue components!

It's not quite [@vue/cli](https://cli.vuejs.org/)-levels of awesome but it's actually a really nice library and I do recommend it. It's also not at all tied to Laravel in any way- you can use it in any project.

<!-- TODO: Expand section. -->

[Back to top](#table-of-contents)

## Widespread community adoption

Several prominent members of the Laravel community are proponents of Vue (and its ecosystem).

This includes but is not limited to:

- [Taylor Otwell](https://twitter.com/taylorotwell)
    - Founder (and Benevolent Dictator for Life) of Laravel itself
    - Known user of Laravel and Vue
- [Adam Wathan](https://twitter.com/adamwathan)
    - Author of [TailwindCSS](https://tailwindcss.com/)
    - Creator of the [Advanced Vue Component Design course](https://adamwathan.me/advanced-vue-component-design/)
    - Known user of Laravel and Vue
- [Jeffrey Way](https://twitter.com/jeffrey_way)
    - Author of [Laravel Testing Decoded](https://leanpub.com/u/jeffreyway)
    - Reoccuring speaker at [Laracon](https://laracon.eu/)
    - Creator of [several Laravel/Vue courses](https://laracasts.com)
    - Known user of Laravel and Vue

This advocacy and support of Vue, as far as I can tell, extends to the Laravel community as a whole, meaning that a majority of Laravel developers will likely be more comfortable and familiar with Vue than say React or Angular.

Naturally, then, it follows that there will be:

- more resources to help you learn (guides, tutorials, articles)
- more people who have experience with Vue+Laravel (help, support, guidance)
- in general a healthy ecosystem (plugins, boilerplates, etc.)

[Back to top](#table-of-contents)

# Common gotchas when using Vue and Laravel together

## Blade and Vue interpolation syntax

As you may be aware both [Blade templates](https://laravel.com/docs/blade#displaying-data) and [Vue templates](https://vuejs.org/v2/guide/#Declarative-Rendering) use moustache syntax (i.e. `{{ message }}`) for variable interpolation, which presents a problem.

Fortunately the solution is simple - just prepend an `@` to the moustache statement. This will [instruct the Blade template rendering engine](https://laravel.com/docs/blade#blade-and-javascript-frameworks) to ignore this statement, leaving it to be later processed by Vue.

```html
<p>You have @{{ messageCount }} new message(s).</p>
```

If you need to escape several moustache statements, you may instead use the `@verbatim` directive.

```html
@verbatim
    <div class="container">
        <p>Welcome {{ user.name }} ({{ user.id }})!</p>
        <p>Your last visit was: {{ user.lastVisit }}.</p>
    </div>
@endverbatim
```

[Back to top](#table-of-contents)

## Passing PHP variables as Vue component props

If you ever need to pass a Blade variable as a [prop](https://vuejs.org/v2/guide/components-props.html) into a [Vue component](https://vuejs.org/v2/guide/components.html) from within a [Blade template](https://laravel.com/docs/blade) then you may be tempted to reach for `json_encode` however you should instead use the `@json` directive:

```html
<user-profile :user='@json($user)' />
```

[Back to top](#table-of-contents)

## Laravel router and vue-router

You can absolutely use [Laravel router](https://laravel.com/docs/routing) and [vue-router](https://router.vuejs.org/) together without too much effort.

Perhaps you want vue-router to handle **all** routing; or for it to handle only some and for Laravel to handle the others; or to serve multiple SPAs using one Laravel app. All of this and more is possible.

### Setting it all up

> **NOTE:** This section presumes a relatively basic understanding of Vue, vue-router, Laravel and the command line.

The boilerplate provided by Laravel does not include vue-router but it won't be much trouble to set up.

#### Install vue-router 

```bash
npm install vue-router --save
```

#### Create a router instance and some routes

Create `resources/js/router.js`:

```js
import Vue from "vue";
import VueRouter from "vue-router";

import PageHome from "./pages/Home";
import PageAbout from "./pages/About";

Vue.use(VueRouter);

const router = new VueRouter({
    mode: "history",
    routes: [
        {
            path: "/",
            component: PageHome
        },
        {
            path: "/about",
            component: PageAbout
        }
    ]
});

export default router;
```

#### Create a simple App component

Create `resources/js/components/App.vue`:

```html
<template>
    <div>
        <nav>
            <ul>
                <li>
                    <router-link to="/">Home</router-link>
                </li>

                <li>
                    <router-link to="/about">About</router-link>
                </li>
            </ul>
        </nav>

        <main>
            <router-view></router-view>
        </main>
    </div>
</template>
```

#### Create a few page components

Create `resources/js/pages/Home.vue`:

```html
<template>
    <div>Home</div>
</template>
```

Create `resources/js/pages/About.vue`:

```html
<template>
    <div>About</div>
</template>
```

#### Configure the root Vue instance

Modify `resources/js/app.js`:

```js
import router from "./router";
import App from "./components/App";

const app = new Vue({
    el: '#app',
    router,               // <-- register router with Vue
    render: (h) => h(App) // <-- render App component
});
```

#### Configure the Laravel router

This is the important part - we need to instruct Laravel to route all requests to the `index` action on the `SPAController`.

Replace `routes/web.php`:

```php
<?php
Route::get('/{vue}', 'SPAController@index')->where('vue', '.*');
```

> **NOTE:** Any routes registered before this catch-all will still function.
>
> This is how we are able to handle some routes with Laravel and others with vue-router.

#### Create the controller and action

Create `app/Http/Controllers/SPAController.php`:

```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class SPAController extends Controller
{
    public function index()
    {
        return view("spa");
    }
}
```

#### Create the view

Create `resources/views/spa.blade.php`:

```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <meta name="csrf-token" content="{{ csrf_token() }}" />

        <title>Laravel + Vue = ❤️</title>

        <link href="{{ asset('css/app.css') }}" rel="stylesheet" />
    </head>

    <body>
        <div id="app"></div>

        <script src="{{ asset('js/app.js') }}"></script>
    </body>
</html>
```

That's it! You are now serving a Vue SPA with Laravel and vue-router is in charge of all routing (except for any routes which were defined before the catch-all, which will be handled by Laravel!).

<!-- TODO: Add serving multiple SPAs section? -->

[Back to top](#table-of-contents)

## Lack of Webpack aliases when using Laravel Mix

If you've used [vue-cli](https://cli.vuejs.org/) then you'll probably be familiar with (and used to using) aliases [such as `@` and `~`](https://cli.vuejs.org/guide/html-and-static-assets.html#url-transform-rules).

Unfortunately this is not setup by default. Fortunately for us - [there's a plugin](https://laravel-mix.com/extensions/alias).

[Back to top](#table-of-contents)

----

# Thanks and good bye

Thanks for reading and enjoy developing with Laravel and Vue.

Also, be sure to check out the [new Vue Community](https://vue-community.org/).

> Something missing or incorrect? This [article's source is on Github](https://github.com/sustained/dev.to-article-sources/).
> 
> Please feel free to [open an issue](https://github.com/sustained/dev.to-article-sources/issues/new) or [send a PR](https://github.com/sustained/dev.to-article-sources/compare).

[Back to top](#table-of-contents)
