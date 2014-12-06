Trestle
===========

## Introduction

<b>Tres</b>tle is an objected oriented PHP 5.4+ PDO database wrapper that is designed to handle multiple database connections with different database types.

This is an independent database package that is being worked on for the [Tres Framework](https://github.com/tres-framework). It can be used without the main framework.

## Getting started

Trestle needs a few things to get started:
1. An autloader
2. Config loader
3. Database initialization

Once those three things are done Trestle is ready to use.

### Autoload
While you can use you own autoload here is an example one to get you started. This assumes you are loading content in the `/tests/` directory and that your Trestle files are located in `/src/Trestle/`, visual illustration:
```
/
|- src/
   |- Trestle/
      |- trestle files
|- tests/
   |- autoload.php

```

Example autoload:
```php
// Autoload
spl_autoload_register(function($class){
    $dirs = [
        dirname(__DIR__).'/src/'
    ];
    
    foreach($dirs as $dir){
	    $file = str_replace('\\', '/', rtrim($dir, '/').'/'.$class.'.php');

    	if(is_readable($file)){
    		require_once($file);
    		break;
    	}
	}
});
```

### Create your config
```php
$dbInfo = [
    // Display PDO errors
    'display_errors' => true,

    // Default connection if one is not specified
    'default' => 'MySQL1',
    
    // List of connections
    'connections' => [
        // Connection 1
        'MySQL1' => [
            'driver'    => 'MySQL',
            'database'  => 'trestle_1',
            'host'      => '127.0.0.1',
            'port'      => '3306',
            'charset'   => 'utf8',
            'username'  => 'root',
            'password'  => ''
        ],
        // Connection 2
        'MySQL2' => [
            'driver'    => 'MySQL',
            'database'  => 'exampel_db_2',
            'host'      => '127.0.0.1',
            'username'  => 'root',
            'password'  => ''
        ]
    ],
    
    // Log settings are optional and will use most of the below by default
    // 'logs' => [
    //     'dir' => [
    //         'path'        => __DIR__ . '/logs',
    //         'permissions' => 0777,
    //     ],
    //     'file' => [
    //         'ext'         => 'log',
    //         'size'        => 2097152,
    //         'permissions' => 0775,
    //     ],
    // ],

];
```

### Load the config & database
Next you need to load the config & initialize the database.

Let's load the `$dbInfo` array into the `Config::set`.
```php
// DB config from above.
$dbInfo = [];
// Set config
Trestle\Config::set($dbInfo);
```

Next we need to create an instance of the database.
```php
$db = new Trestle\Database('MySQL1');
```

It is recommended to wrap the database in a try/catch as Trestle uses it for errors.
```php
try {
	// Set Config
	Trestle\Config::set($dbInfo);

	// Load Database 1
	$db = new Trestle\Database('MySQL1');
    // Load another database
	$db2 = new Trestle\Database('MySQL2');
	
	// Build queries...
    // Look below for examples
    
} catch(TrestleException $e) {
	echo $e->getMessage();
} catch(\Exception $e) {
	echo $e->getMessage();
}
```

## Queries
While Trestle allows for raw query usage using the `query()` method it is always recommend to use the the core methods, these are:
- get
- update
- create
- delete

Once done you can string on any number of appropriate methods to create your statement. Remember that each method chain should end in the `exec` method or the query will not be run. Below are some examples using `query()`, `get()`, `update()`, `create()` & `delete()`, the data is then return using the `first()`, `results()`, `count()` & `status()` methods.

After a query is called you can use the `debug()` method to check your query for bugs, syntax, general errors & execution time.

### Raw Query
```php
// Get a record with specific fields
// Return first result
// SELECT `username`, `firstname`, `email` FROM `users` WHERE `id` = ?
$users = $db->query('SELECT `username`, `firstname`, `email` FROM `users` WHERE `id` = ?', 1)
            ->exec();
echo '<pre>'; print_r($users->first()); echo '</pre>';
```

### Get (first or only row)
```php
// Get a record with specific fields
// Return all results
// SELECT username, firstname, email FROM `users` WHERE `id` = ?
$users = $db->get('users', ['username', 'firstname', 'email'])
            ->where('id', '=', 1)
            ->exec();
echo '<pre>'; print_r($users->first()); echo '</pre>';
```

### Get (all rows + count rows)
```php
// Get records with all existing fields
// Return all data
// SELECT * FROM `users` ORDER BY ? ASC LIMIT ?, ?
$users = $db->get('users')
            ->order('id', 'ASC')
            ->offset(0)
            ->limit(5)
            ->exec();
echo '<pre>'; print_r($users->results()); echo '</pre>';
echo '<pre>'; print_r($users->count()); echo '</pre>';
```

### Get (BETWEEN)
```php
// Get records with all existing fields
// Return all data
// SELECT * FROM `users` WHERE `id` BETWEEN ? AND ?
$users = $db->get('users')
			->where('id', 'BETWEEN', [1, 9])
			->exec();
echo '<pre>'; print_r($users->results()); echo '</pre>';
```

### Get (NOT BETWEEN)
```php
// Get records with all existing fields
// Return all data
// SELECT * FROM `users` WHERE `id` BETWEEN ? AND ?
$users = $db->get('users')
			->where('id', 'NOT BETWEEN', [1, 9])
			->exec();
echo '<pre>'; print_r($users->results()); echo '</pre>';
```

### Get (LIKE)
```php
// Get records with all existing fields
// Return all data
// SELECT * FROM `users` WHERE `id` BETWEEN ? AND ?
$posts = $db->get('posts')
            ->where('title', 'LIKE', 'foobar')
            ->exec();
echo '<pre>'; print_r($posts->results()); echo '</pre>';
```

### GET (The Kitchen Sink)
```php
// A ton of parameters
// Return data as object
// SELECT id, title FROM `posts` WHERE `date` > ? AND `id` BETWEEN ? AND ? AND `author` LIKE ? ORDER BY ? ASC LIMIT ?, ?
$posts = $db->get('posts', ['id', 'title'])
            ->where('date', '>', '2014-11-20')
            ->andWhere('id', 'BETWEEN', [1, 9])
            ->andWhere('author', 'LIKE', 1)
            ->order('date', 'ASC')
            ->limit(4)
            ->offset(1)
            ->exec();
echo '<pre>SELECT `id`, `title` FROM posts WHERE `date` > ? AND `id` BETWEEN ? AND ? AND `author` LIKE ? ORDER BY ? ASC  LIMIT ?, ?</pre>';
echo '<pre>'; print_r($posts->results()); echo '</pre>';
echo 'Debug:';
echo '<pre>'; print_r($posts->debug()); echo '</pre>';
```

### Update
```php
// Update row
// Return true/false of update
// UPDATE `users` SET `username` = ?, `email` = ?, `firstname` = ? WHERE `id` = ?
$update = $db->update('users', [
                'username'  => 'bar',
                'email'     => 'bar@foo.tld',
                'firstname' => 'bar',
                'lastname'  => 'foo'
            ])
            ->where('id', '=', 3)
            ->exec();
echo '<pre>'; print_r($update->status()); echo '</pre>';
```

### Create
```php
// Create a record
// Return true/false of create
// INSERT INTO `users` (`username`, `email`, `firstname`, `lastname`, `active`, `permissions`) VALUES (?, ?, ?, ?, ?, ?);
$register = $db->create('users', [
                    'username' => 'foobar',
                    'email' => 'foo@bar.tld',
                    'password' => 'cleartextwoot',
                    'firstname' => 'Foo',
                    'lastname' => 'Bar',
                    'active' => 0,
                    'permissions' => '{\'admin\': 0}'
                ])
                ->exec();
echo '<pre>'; print_r($register->status()); echo '</pre>';
```

### Delete
```php
// Delete
// Return true/false of delete
// DELETE FROM `users` WHERE `id` = ?
$delete = $db->delete('users')
             ->where('id', '=', 72)
             ->exec();
echo '<pre>'; print_r($delete->status()); echo '</pre>';
```

## Using the data
```php
$foobar = $db->query('...')
             ->exec();
// Get all
$foobar->results();
// Get first
$foobar->first();
// Get count
$foobar->count();
// Get status of query success (boolean)
$foobar->status();
```

## Debug
```php
$foobar = $db->query('...')
             ->exec();
// Full debug
$foobar->debug();
```
