Routing
============

*The Tres routing system is bundled with Tres Framework. If you like it, you
can also [use it without the framework][router-link].*

## Introduction
A router generally forwards you to something. In this case, we take the URI,
match it with a list and do something based on that. What you want to do with
the routes is up to you.

This technique adds a degree of separation between the files used to generate
a webpage and the URL that is presented to the world. An addition to that is
not only that it is search engine friendly, but also that it's prettier.

## Basic routing
Now you know what a router is, let's take a look at how you can make use of it.

### Registering routes
Before making use of routes, you should have the slightest understanding of
HTTP request methods. The most common ones are the GET and POST methods.
```php
<?php

// Homepage
Route::get('/', [
    'alias' => 'home',
    'controller' => 'HomeController',
    'method' => 'renderPage'
]);

// Contact page
Route::get('/contact', [
    'alias' => 'contact',
    'controller' => 'ContactController',
    'method' => 'renderPage'
]);

Route::post('/contact', [
    'controller' => 'ContactController',
    'method' => 'sendMail'
]);

// Multi request
Route::register(['GET', 'POST'], '/multi-request-example', [
    'controller' => 'ExampleController',
    'method' => 'beUseful'
]);
```
Routes may start and end with a slash. It does not make a difference.

What you might notice is the use of controllers. Its intent is to respond to
user actions. While it's not required to make use of a controller, it is
recommended to do so for consistency and separation of concerns.
```php
// Using lambda function.
Route::get('/no-controller-example', function(){
    echo '<h1>Lambda function</h1>';
    echo '<p>Not recommended in most cases.</p>';
});

Route::get('/', [
    'alias' => 'home',
    function(){
        echo '<h1>Home</h1>';
        echo '<p>Aliasing also supported for lambda functions.</p>';
    }
]);
```

### Error pages
You can change the Error 404 Not Found route by using the `notFound` method.
```php
Route::notFound([
    'controller' => 'ErrorController',
    'method' => 'notFound'
]);
```

### Accessing routes
So you registered a few routes. Great! But how can you efficiently access them?

It would be useful to be able to change the URL without having to change all
the links in your pages. That is where aliasing comes of use.

Let's say you have the following route:
```php
Route::get('/about-me', [
    'alias' => 'about',
    'controller' => 'AboutController',
    'method' => 'renderPage'
]);
```
with the following code in your view:
```php
<a href="{{ URL::route('about'); }}">Click here to know more!</a>
```
This tells the router to find the first view with the "about" alias. At the
moment, it points to `/about-me`. By changing the route URI, it will simply
change anchor's href value along.
```php
Route::get('/about-us', [
    'alias' => 'about',
    'controller' => 'AboutController',
    'method' => 'renderPage'
]);
```

## Dynamic routes
Let's say you are making a blog. It would be silly to make a new route for
every single post. You can make a dynamic route which can be used for all your
blog posts.
```php
Route::get('/blog/:id/:title', [
    'controller' => 'BlogController',
    'method' => 'getPost'
]);

Route::get('/number-echo/:number', function($number){
    echo '<h1>Your number:</h1>';
    echo $number;
});
```
If you want to make a placeholder (like `:title`) optional, you have to
duplicate the route and remove the placeholder (`:title` in this case).
```php
Route::get('/blog/:id/', [
    'controller' => 'BlogController',
    'method' => 'getPost'
]);

Route::get('/blog/:id/:title', [
    'controller' => 'BlogController',
    'method' => 'getPost'
]);
```

## Importing routes
If you are writing an application or just a large website, you might be
interested in importing routes, so you have multiple route files.
```php
Route::import(VENDOR_DIR.'/ExampleApp/routes.php');
```

[router-link]: https://github.com/tres-framework/Tres-router
