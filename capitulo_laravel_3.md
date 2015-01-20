# Laravel 3 -


<!-- ************************************************************************-->
# Base de Datos

Configuration

Laravel makes connecting with databases and running queries extremely simple. The database configuration file is app/config/database.php. In this file you may define all of your database connections, as well as specify which connection should be used by default. Examples for all of the supported database systems are provided in this file.

Currently Laravel supports four database systems: MySQL, Postgres, SQLite, and SQL Server.


## Configuración de la Base de Datos


Editamos app/config/database.php

Y cambiamos, en la sección de mysql, los siguientes campos:


```php
'mysql' => array(
    'driver'    => 'mysql',
    'host'      => 'localhost',
    'database'  => 'laravel',
    'username'  => 'root',
    'password'  => '',
    'charset'   => 'utf8',
    'collation' => 'utf8_unicode_ci',
    'prefix'    => '',
),
```

Para crear la base de datos que vamos a utilizar en MySQL podemos utilizar la herramienta PHPMyAdmin que se ha instalado con el paquete XAMPP. Para esto accedemos a la ruta:


```bash
http://localhost/phpmyadmin
```

La cual nos mostrará un panel para la gestión de las bases de datos de MySQL, que nos permite, además de realizar cualquier tipo de consulta, crear nuevas bases de datos o tablas, e insertar, modificar o eliminar los datos. En nuestro caso apretamos en la pestaña "Bases de datos" y creamos una nueva base de datos llamada "laravel" (igual que hemos indicado antes en el fichero de configuración de Laravel).

A continuación vamos a crear la tabla de migraciones en la nueva base de datos ejecutando el siguiente comando de Artisan:


```bash
php artisan migrate:install
```

Si ahora vamos al navegador y actualizamos nuestra base de datos en PHPMyAdmin podremos comprobar que se nos tendrá que haber creado la tabla `migrations`.



## Migraciones

Migrations are a type of version control for your database. They allow a team to modify the database schema and stay up to date on the current schema state. Migrations are typically paired with the Schema Builder to easily manage your application's schema.

Creating Migrations

To create a migration, you may use the migrate:make command on the Artisan CLI:

```bash
php artisan migrate:make create_users_table
```

The migration will be placed in your app/database/migrations folder, and will contain a timestamp which allows the framework to determine the order of the migrations.

Running Migrations

Running All Outstanding Migrations

```bash
php artisan migrate
```


Rolling Back Migrations

Rollback The Last Migration Operation

```php
php artisan migrate:rollback
```

Rollback all migrations

```php
php artisan migrate:reset
```

Rollback all migrations and run them all again

```php
php artisan migrate:refresh
```



## Schema Builder

The Laravel Schema class provides a database agnostic way of manipulating tables. It works well with all of the databases supported by Laravel, and has a unified API across all of these systems.

Creating & Dropping Tables

To create a new database table, the Schema::create method is used:

```php
Schema::create('users', function($table)
{
    $table->increments('id');
});
```

The first argument passed to the create method is the name of the table, and the second is a Closure which will receive a Blueprint object which may be used to define the new table.

To drop a table, you may use the Schema::drop method:

```php
Schema::drop('users');

Schema::dropIfExists('users');
```


The table builder contains a variety of column types that you may use when building your tables:

| Command | Description |
| -- | -- |
| $table->boolean('confirmed'); 	               | BOOLEAN equivalent to the table |
| $table->enum('choices', array('foo', 'bar')); | 	ENUM equivalent to the table |
| $table->float('amount');                      | 	FLOAT equivalent to the table |
| $table->increments('id');                     | 	Incrementing ID to the table (primary key). |
| $table->integer('votes');                     | INTEGER equivalent to the table |
| $table->mediumInteger('numbers');             | MEDIUMINT equivalent to the table |
| $table->smallInteger('votes'); 	           | SMALLINT equivalent to the table |
| $table->tinyInteger('numbers'); 	           | TINYINT equivalent to the table |
| $table->string('email'); 	                    | VARCHAR equivalent column |
| $table->string('name', 100); 	                | VARCHAR equivalent with a length |
| $table->text('description'); 	                | TEXT equivalent to the table |
| $table->timestamp('added_on'); 	          | TIMESTAMP equivalent to the table |
| $table->timestamps(); 	                       | Adds created_at and updated_at columns |
| ->nullable() 	                                | Designate that the column allows NULL values |
| ->default($value) 	                           | Declare a default value for a column |
| ->unsigned() 	                                | Set INTEGER to UNSIGNED | 

Para consultar todos los tipos de datos que podemos utilizar podéis consultar la documentación de Laravel en: 

http://laravel.com/docs/4.2/schema#adding-columns


### Adding Indexes

The schema builder supports several types of indexes. There are two ways to add them. First, you may fluently define them on a column definition, or you may add them separately:

```php
$table->string('email')->unique();
```

Or, you may choose to add the indexes on separate lines. Below is a list of all available index types:

| Command | Description| 
| -- | -- |
| $table->primary('id');                     | 	Adding a primary key |
| $table->primary(array('first', 'last')); 	| Adding composite keys |
| $table->unique('email'); 	                | Adding a unique index |
| $table->index('state'); 	                | Adding a basic index |


### Claves ajenas

Laravel also provides support for adding foreign key constraints to your tables:

```php
$table->integer('user_id')->unsigned();
$table->foreign('user_id')->references('id')->on('users');
```

In this example, we are stating that the user_id column references the id column on the users table. Make sure to create the foreign key column first!

You may also specify options for the "on delete" and "on update" actions of the constraint:

```php
$table->foreign('user_id')
      ->references('id')->on('users')
      ->onDelete('cascade');
```

To drop a foreign key, you may use the dropForeign method. A similar naming convention is used for foreign keys as is used for other indexes:

```php
$table->dropForeign('posts_user_id_foreign');
```

Note: When creating a foreign key that references an incrementing integer, remember to always make the foreign key column unsigned.






## Database Seeding

Laravel also includes a simple way to seed your database with test data using seed classes. All seed classes are stored in app/database/seeds. Seed classes may have any name you wish, but probably should follow some sensible convention, such as UserTableSeeder, etc. By default, a DatabaseSeeder class is defined for you. From this class, you may use the call method to run other seed classes, allowing you to control the seeding order.

Example Database Seed Class

```php
class DatabaseSeeder extends Seeder {

    public function run()
    {
        $this->call('UserTableSeeder');

        $this->command->info('User table seeded!');
    }

}

class UserTableSeeder extends Seeder {

    public function run()
    {
        DB::table('users')->delete();

        User::create(array('email' => 'foo@bar.com'));
    }

}
```

To seed your database, you may use the db:seed command on the Artisan CLI:

```bash
php artisan db:seed
```




## Query Builder

The database query builder provides a convenient, fluent interface to creating and running database queries. It can be used to perform most database operations in your application, and works on all supported database systems.

Note: The Laravel query builder uses PDO parameter binding throughout to protect your application against SQL injection attacks. There is no need to clean strings being passed as bindings.

Selects

Retrieving All Rows From A Table

```php
$users = DB::table('users')->get();

foreach ($users as $user)
{
    var_dump($user->name);
}
```

Retrieving A Single Row From A Table

```php
$user = DB::table('users')->where('name', 'John')->first();

var_dump($user->name);
```

Using Where Operators

```php
$users = DB::table('users')->where('votes', '>', 100)->get();
```

Or Statements

```php
$users = DB::table('users')
                    ->where('votes', '>', 100)
                    ->orWhere('name', 'John')
                    ->get();
```
                    
Order By, Group By, And Having

```php
$users = DB::table('users')
                    ->orderBy('name', 'desc')
                    ->groupBy('count')
                    ->having('count', '>', 100)
                    ->get();
```

Offset & Limit

```php
$users = DB::table('users')->skip(10)->take(5)->get();
```


Para más información sobre la construcción de Querys (join, insert, update, delete, agregados, etc.) podéis consultar la documentación de Laravel en su sitio web: 

http://laravel.com/docs/4.2/queries





<!-- ************************************************************************-->
# Modelo de datos


/* COPIADO DE INTERNET, MEJORAR:

Modelo

Para crear nuestro modelo de usuario debemos crear un archivo llamado usuario.php en la carpeta /app/models con el siguiente código.


<?php
class Usuario extends Eloquent { //Todos los modelos deben extender la clase Eloquent
    protected $table = 'usuarios';
}
?>

En Laravel los modelos utilizan el Eloquent ORM, que proporciona una manera elegante y fácil de interactuar con la base de datos. Para esto cada tabla en la base datos debe tener su correspondiente modelo.


Los modelos utilizan convenciones para saber que modelo utiliza que tabla de la base datos, pero como esas convenciones están hechas para el idioma ingles es mejor decirle directamente que tabla debe usar en que modelo. Esto lo hacemos con la variables $table, en ella especificamos que el modelo Usuario utiliza la tabla de usuarios.

Laravel asume que todas las tablas tienen tres campos básicos ‘id’ (clave primaria), ‘created_at’, ‘updated_at’. Los últimos dos son llenados automáticamente por el framework.
Vista

La primera vista que vamos a crear será lista.blade.php en /app/views/usuarios/. Para esto primero vamos a crear la carpeta de usuario dentro de /app/views, luego dentro de usuario creamos el archivo lista.blade.php con el siguiente código.

*/





<!-- ************************************************************************-->
# Eloquent ORM

Introduction

The Eloquent ORM included with Laravel provides a beautiful, simple ActiveRecord implementation for working with your database. Each database table has a corresponding "Model" which is used to interact with that table.

Before getting started, be sure to configure a database connection in app/config/database.php.

Basic Usage

To get started, create an Eloquent model. Models typically live in the app/models directory, but you are free to place them anywhere that can be auto-loaded according to your composer.json file.
Defining An Eloquent Model

```php
class User extends Eloquent {}
```

Note that we did not tell Eloquent which table to use for our User model. The lower-case, plural name of the class will be used as the table name unless another name is explicitly specified. So, in this case, Eloquent will assume the User model stores records in the users table. You may specify a custom table by defining a table property on your model:

```php
class User extends Eloquent {

    protected $table = 'my_users';

}
```

Note: Eloquent will also assume that each table has a primary key column named id. You may define a primaryKey property to override this convention. Likewise, you may define a connection property to override the name of the database connection that should be used when utilizing the model.

Once a model is defined, you are ready to start retrieving and creating records in your table. Note that you will need to place updated_at and created_at columns on your table by default. If you do not wish to have these columns automatically maintained, set the $timestamps property on your model to false.
Retrieving All Models

```php
$users = User::all();
```

Retrieving A Record By Primary Key

```php
$user = User::find(1);

var_dump($user->name);
```

Note: All methods available on the query builder are also available when querying Eloquent models.

Retrieving A Model By Primary Key Or Throw An Exception

Sometimes you may wish to throw an exception if a model is not found, allowing you to catch the exceptions using an App::error handler and display a 404 page.


```php
$model = User::findOrFail(1);

$model = User::where('votes', '>', 100)->firstOrFail();
```


Querying Using Eloquent Models

```php
$users = User::where('votes', '>', 100)->take(10)->get();

foreach ($users as $user)
{
    var_dump($user->name);
}
```

Eloquent Aggregates

Of course, you may also use the query builder aggregate functions.

```php
$count = User::where('votes', '>', 100)->count();
```

Insert, Update, Delete

To create a new record in the database from a model, simply create a new model instance and call the save method.

Saving A New Model

```php
$user = new User;
$user->name = 'John';
$user->save();
```

After saving or creating a new model that uses auto-incrementing IDs, you may retrieve the ID by accessing the object's id attribute:

```php
$insertedId = $user->id;
```


Updating A Retrieved Model

To update a model, you may retrieve it, change an attribute, and use the save method:

```php
$user = User::find(1);
$user->email = 'john@foo.com';
$user->save();
```


Deleting An Existing Model

To delete a model, simply call the delete method on the instance:

```php
$user = User::find(1);
$user->delete();
```


Of course, you may also run a delete query on a set of models:

```php
$affectedRows = User::where('votes', '>', 100)->delete();
```

Timestamps

By default, Eloquent will maintain the created_at and updated_at columns on your database table automatically. Simply add these timestamp columns to your table and Eloquent will take care of the rest. If you do not wish for Eloquent to maintain these columns, add the following property to your model:

Disabling Auto Timestamps

```php
class User extends Eloquent {

    protected $table = 'users';

    public $timestamps = false;
}
```

### Más información

Para más información sobre como crear relaciones entre modelos, el _eager loading_ 





## Transacciones

To run a set of operations within a database transaction, you may use the transaction method:

```php
DB::transaction(function()
{
    DB::table('users')->update(array('votes' => 1));

    DB::table('posts')->delete();
});
```

Note: Any exception thrown within the transaction closure will cause the transaction to be rolled back automatically.






<!-- ************************************************************************-->
# Special Responses

Creating A JSON Response

```php
return Response::json(array('name' => 'Steve', 'state' => 'CA'));
```


# Converting To Arrays / JSON
Converting A Model To An Array

When building JSON APIs, you may often need to convert your models and relationships to arrays or JSON. So, Eloquent includes methods for doing so. To convert a model and its loaded relationship to an array, you may use the toArray method:

```php
$user = User::with('roles')->first();

return $user->toArray();
```

Note that entire collections of models may also be converted to arrays:

```php
return User::all()->toArray();
```

Converting A Model To JSON

To convert a model to JSON, you may use the toJson method:

```php
return User::find(1)->toJson();
```

Hiding Attributes From Array Or JSON Conversion

Sometimes you may wish to limit the attributes that are included in your model's array or JSON form, such as passwords. To do so, add a hidden property definition to your model:

```php
class User extends Eloquent {

    protected $hidden = array('password');

}
```






<!-- ************************************************************************-->
# RESTful Resource Controllers

Resource controllers make it easier to build RESTful controllers around resources. For example, you may wish to create a controller that manages "photos" stored by your application. Using the controller:make command via the Artisan CLI and the Route::resource method, we can quickly create such a controller.

To create the controller via the command line, execute the following command:

```bash
php artisan controller:make PhotoController
```

Now we can register a resourceful route to the controller:

```php
Route::resource('photo', 'PhotoController');
```

This single route declaration creates multiple routes to handle a variety of RESTful actions on the photo resource. Likewise, the generated controller will already have stubbed methods for each of these actions with notes informing you which URIs and verbs they handle.

## Actions Handled By Resource Controller


| Verb      | Path                      | Action  | Route Name      |
| --------- | ------------------------- | ------- | --------------- |
| GET       | /resource                 | index   | resource.index  |
| GET    	| /resource/create 	        | create  | resource.create |
| POST 	    | /resource 	                | store   | resource.store  |
| GET 	    | /resource/{resource} 	    | show 	  | resource.show   |
| GET 	    | /resource/{resource}/edit | edit 	  | resource.edit   |
| PUT/PATCH | /resource/{resource} 	    | update  | resource.update |
| DELETE 	| /resource/{resource} 	    | destroy | resource.destroy|


Sometimes you may only need to handle a subset of the resource actions:

```php
php artisan controller:make PhotoController --only=index,show

php artisan controller:make PhotoController --except=index
```

And, you may also specify a subset of actions to handle on the route:

```php
Route::resource('photo', 'PhotoController',
                array('only' => array('index', 'show')));

Route::resource('photo', 'PhotoController',
                array('except' => array('create', 'store', 'update', 'destroy')));
```

By default, all resource controller actions have a route name; however, you can override these names by passing a names array with your options:

```php
Route::resource('photo', 'PhotoController',
                array('names' => array('create' => 'photo.build')));
```

## Handling Nested Resource Controllers

To "nest" resource controllers, use "dot" notation in your route declaration:

```php
Route::resource('photos.comments', 'PhotoCommentController');
```

This route will register a "nested" resource that may be accessed with URLs like the following: photos/{photoResource}/comments/{commentResource}.

```php
class PhotoCommentController extends BaseController
{
    public function show($photoId, $commentId)
    {
        //
    }
}
```

## Adding Additional Routes To Resource Controllers

If it becomes necessary for you to add additional routes to a resource controller beyond the default resource routes, you should define those routes before your call to Route::resource:

```php
Route::get('photos/popular');
Route::resource('photos', 'PhotoController');
```











<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->



<!-- ************************************************************************-->
# Ejercicios





