Routing
============

## Introduction

The Tres routing system is always bundled with Tres Framework. It is however a 
stand-alone package, which means that it can be used without the framework.

A router generally forwards you to something. In this case, we take the URI, match it with a 
list and do something based on that. What you want to do with the routes is up to you.

This technique adds a degree of separation between the files used to generate a webpage and the 
URL that is presented to the world. An addition to that is not only that it is search engine friendly,
but also that it's prettier for humans like you and me.

## Basic routing
So now you know what a router is, let's take a look at how you can make use of it.

### Registering routes
There are several things you should know. First of all, you should have a basic understanding of 
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
Route::register(['GET', 'POST'], '/multi-request-example', function(){
    //
});
```
What you notice, is that we make use of controllers. Its intent is to respond to user actions.

While it's not required to make use of a controller, we recommend doing so for consistency.
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
You can customize the Error 404 Not Found page by using the notFound() method.
```php
Route::notFound([
    'controller' => 'ErrorController',
    'method' => 'notFound'
]);
```

### Accessing routes
So you registered a few routes. Great! But how can you efficiently access them?

The Tres router gives you the option to make use of aliasing. Aliasing is useful, because you might 
change the page URL, but not change the page itself. It would be useful to be able to change the URL
without having to change all the links in your pages. That is where aliasing comes of use.

So let's say you have the following route:
```php
Route::get('/about-me', [
    'alias' => 'about',
    'controller' => 'AboutController',
    'method' => 'renderPage'
]);
```
With the following code in your view:
```php
<a href="<?= URL::route('about'); ?>">Click here to know more!</a>
```
This tells the router to find the first view with the "about" alias. At the moment, it points to
/about-me. By changing the route URI, it will simply change along.
```php
Route::get('/about-us', [
    'alias' => 'about',
    'controller' => 'AboutController',
    'method' => 'renderPage'
]);
```

## Dynamic routes
Let's say you are making a blog. You of course wouldn't make a new route for every single post. 
You only make one dynamic route which can be used for all your blog posts.
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
At the moment of writing, the router does not support optional routes. If you want to make 
the title optional, you have to duplicate the route and remove the :title.
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
