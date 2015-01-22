
Curl
Rest




<!-- ************************************************************************-->
# Unión entre formulario y modelo de datos

Laravel incluye una forma muy sencilla de rellenar de datos un formulario que está basado en un modelo, simplemente tendremos que abrir el formulario utilizando el método `Form::model` en lugar de `Form::open`, pasándole como primer parámetro una instancia del modelo. Por ejemplo: 

```php
{{ Form::model($user, array('action' => array('Controller@method', $user->id))) }}
```

El resto de parámetro de este método son exactamente iguales a los que utilizaríamos con `Form::open`. 

Al utilizar este método, todos los campos del formulario cuyo nombre coincida con los del modelo serán rellenados con los datos del campo correspondiente que se ha pasado por parámetro. Pero no solo eso, sino que si se produce un error y se envía al usuario otra vez al formulario (con la opción de `withInput`), entonces el formulario automáticamente cogerá los valores que escribió (o modificó) el usuario y los pondrá (dándoles precedencia sobre los valores que ya estaban en el modelo). 


> Nota: Para cerrar el formulario tenemos que seguir usando el mismo método que antes: `Form::close`





<!-- ************************************************************************-->
# Convertir a Arrays / JSON

Laravel incluye métodos para transformar fácilmente el resultado obtenido de la consulta a un modelo de datos a formato JSON o a formato array. Esto es especialmente útil cuando estamos diseñando una API y queremos enviar los datos en formato JSON. Además, al realizar la transformación se incluirán los datos de las relaciones que se hayan cargado al hacer la consulta. Para realizar esta transformación simplemente tenemos que usar los métodos `toJson()` o `toArray()` sobre el resultado de la consulta:

```php
$user = User::first();

$arrayUsuario = $user->toArray();

$jsonUsuario = $user->toJson();
```

También podemos transformar una colección entera de datos a este formato, por ejemplo: 

```php
$arrayUsuarios = User::all()->toArray();

$jsonUsuarios = User::all()->toJson();
```


En ocasiones nos interesará ocultar determinados atributos de nuestro modelo en la conversión a array o a JSON, como por ejemplo, el password o el identificador. Para hacer esto tenemos que definir el campo protegido `hidden` de nuestro modelo con el array de atributos a ocultar: 

```php
class User extends Eloquent {

    protected $hidden = array('password');
    
    // O también podemos indicar solamente aquellos que queramos mostrar: 
    //protected $visible = array('name', 'address');

}
```


<!-- ************************************************************************-->
## Respuestas especiales

Con lo que hemos visto en la sección anterior solo se realizaría la transformación a JSON, pero si queremos devolverlo como respuesta de una petición (por ejemplo, como respuesta a una petición de un método de una API), tendremos que utilizar el método `Response::json`, el cual además añadirá en la cabecera de la respuesta que los datos enviados están en formato JSON. Por ejemplo:


```php
$usuarios = User::all();

return Response::json( $usuarios );
```

Este método también puede recibir otro tipo de valores (como una variable, un array, etc.) y los transformará también a JSON para devolverlos como respuesta a la petición: 

```php
return Response::json( array('name' => 'Steve', 'state' => 'CA') );
```



<!-- ************************************************************************-->
# HTTP Basic Authentication

HTTP Basic Authentication provides a quick way to authenticate users of your application without setting up a dedicated "login" page. To get started, attach the auth.basic filter to your route:

Protecting A Route With HTTP Basic

```php
Route::get('profile', array('before' => 'auth.basic', function()
{
    // Only authenticated users may enter...
}));
```

By default, the basic filter will use the email column on the user record when authenticating. If you wish to use another column you may pass the column name as the first parameter to the basic method in your app/filters.php file:

```php
Route::filter('auth.basic', function()
{
    return Auth::basic('username');
});
```

Setting Up A Stateless HTTP Basic Filter

You may also use HTTP Basic Authentication without setting a user identifier cookie in the session, which is particularly useful for API authentication. To do so, define a filter that returns the onceBasic method:

```php
Route::filter('basic.once', function()
{
    return Auth::onceBasic();
});
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






<!-- ************************************ -->
# Ejercicio Opcional - Completar la plataforma

De forma opcional se pide terminar el proyecto del videoclub con los siguientes puntos:

* Funcionalidad del botón alquilar / devolver película. (fácil)

* Añadir el método eliminar película desde la vista detalle de una película. (medio)

* Mostar un aviso después de guardar o editar una película. (avanzado)

* Validar los datos enviados y mostrar los errores al usuario en caso de no superar la validación. (avanzado)




