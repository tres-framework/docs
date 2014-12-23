Routing
============

## Basic routing
Now you know what a router is, let's take a look at how you can make use of it.

### Registering routes
Before making use of routes, you should have the slightest understanding of
HTTP request methods. The most common ones are GET and POST.
```php
<?php

// Homepage
Route::get('/', [
    'alias' => 'home',
    'uses' => 'HomeController@renderPage'
]);

// Contact page
Route::get('/contact', [
    'alias' => 'contact',
    'uses' => 'ContactController@renderPage'
]);

Route::post('/contact', [
    'uses' => 'ContactController@sendMail'
]);

// Multi request
Route::register(['GET', 'POST'], '/multi-request-example', [
    'uses' => 'ExampleController@beUseful'
]);
```
Routes may start and end with a slash. It does not make a difference.

What you might notice is the use of controllers. Its intent is to respond to
user actions. While it is not required to make use of a controller, it is
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
    'uses' => 'ErrorController@notFound'
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
    'uses' => 'AboutController@renderPage'
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
    'uses' => 'AboutController@renderPage'
]);
```

## Dynamic routes
Let's say you are making a blog. It would be silly to make a new route for
every single post. You can make a dynamic route which can be used for all your
blog posts.
```php
Route::get('/blog/:id/:title', [
    'uses' => 'BlogController@getPost'
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
    'uses' => 'BlogController@getPost'
]);

Route::get('/blog/:id/:title', [
    'uses' => 'BlogController@getPost'
]);
```

## Importing and grouping routes
If you are writing an application or just a large website, you might be
interested in importing and grouping routes, so routes are more separated.

### Importing
```php
Route::import(VENDOR_DIR.'/ExampleApp/routes.php');
```

### Grouping
The `group` method accepts one and two arguments. If you provide one argument,
it must be callable.
```php
Route::group(function(){

    // ...

});
```

If you provide two arguments, the first argument can be either a
string or an array and the second must be callable. If a string has been
passed to the first argument, it will be accepted as the group prefix.
```php
Route::group('forum', function(){

    Route::get('/login', function(){
        // ...
    });

    // ...

});
```
In case of an array, you have to add `'prefix' => 'value'`.
```php
Route::group([
    'prefix' => 'forum',
    // ...
], function(){

    // ...

});
```
#### Prefixing
You can prefix groups by using the dot notation, but you could also nest them.

##### Dot notation
```php
Route::group('cms.admin.user-management', function(){

    // ...

});
```
##### Nesting
```php
Route::group('cms', function(){

    Route::group('admin', function(){

        Route::group('user-management', function(){

            // ...

        });

    });

});
```
