# Laravel 4 -


<!-- ************************************************************************-->
# Control de usuarios

http://laravel.com/docs/4.2/security






<!-- ************************************** -->
## Throwing 404 Errors

There are two ways to manually trigger a 404 error from a route. First, you may use the App::abort method:

```php
App::abort(404);
```

Second, you may throw an instance of Symfony\Component\HttpKernel\Exception\NotFoundHttpException.

More information on handling 404 exceptions and using custom responses for these errors may be found in the errors section of the documentation.




<!-- ************************************************************************-->
# Form Model Binding

Opening A Model Form

Often, you will want to populate a form based on the contents of a model. To do so, use the Form::model method:

```php
echo Form::model($user, array('route' => array('user.update', $user->id)))
```

Now, when you generate a form element, like a text input, the model's value matching the field's name will automatically be set as the field value. So, for example, for a text input named email, the user model's email attribute would be set as the value. However, there's more! If there is an item in the Session flash data matching the input name, that will take precedence over the model's value. So, the priority looks like this:

* Session Flash Data (Old Input)
* Explicitly Passed Value
* Model Attribute Data

This allows you to quickly build forms that not only bind to model values, but easily re-populate if there is a validation error on the server!

> Note: When using Form::model, be sure to close your form with Form::close!





<!-- ************************************************************************-->
# Basic Input

You may access all user input with a few simple methods. You do not need to worry about the HTTP verb used for the request, as input is accessed in the same way for all verbs.

Retrieving An Input Value

```php
$name = Input::get('name');
```

Retrieving A Default Value If The Input Value Is Absent

```php
$name = Input::get('name', 'Sally');
```

Determining If An Input Value Is Present

```php
if (Input::has('name'))
{
    //
}
```

Getting All Input For The Request

```php
$input = Input::all();
```

Getting Only Some Of The Request Input

```php
$input = Input::only('username', 'password');

$input = Input::except('credit_card');
```

When working on forms with "array" inputs, you may use dot notation to access the arrays:

```php
$input = Input::get('products.0.name');
```

Note: Some JavaScript libraries such as Backbone may send input to the application as JSON. You may access this data via Input::get like normal.



<!-- ************************************ -->
## Files

Retrieving An Uploaded File

```php
$file = Input::file('photo');
```

Determining If A File Was Uploaded

```php
if (Input::hasFile('photo'))
{
    //
}
```

The object returned by the file method is an instance of the Symfony\Component\HttpFoundation\File\UploadedFile class, which extends the PHP SplFileInfo class and provides a variety of methods for interacting with the file.

Determining If An Uploaded File Is Valid

```php
if (Input::file('photo')->isValid())
{
    //
}
```

Moving An Uploaded File

```php
Input::file('photo')->move($destinationPath);

Input::file('photo')->move($destinationPath, $fileName);
```

Retrieving The Path To An Uploaded File

```php
$path = Input::file('photo')->getRealPath();
```

Retrieving The Original Name Of An Uploaded File

```php
$name = Input::file('photo')->getClientOriginalName();
```

Retrieving The Extension Of An Uploaded File

```php
$extension = Input::file('photo')->getClientOriginalExtension();
```

Retrieving The Size Of An Uploaded File

```php
$size = Input::file('photo')->getSize();
```

Retrieving The MIME Type Of An Uploaded File

```php
$mime = Input::file('photo')->getMimeType();
```







<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->
<!-- ************************************************************************-->



<!-- ************************************************************************-->
# Ejercicios


Añadir el filtro CSRF a los formularios



