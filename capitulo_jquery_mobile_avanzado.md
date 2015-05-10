



<!-- *********************************************************************** -->
#### Elementos de tipo checkbox

Este tipo de elementos se utilizan para proporcionar al usuario una serie de opciones de las cuales puede seleccionar más de una. Al igual que los elementos de tipo radio, los elementos de tipo checkbox utilizan la misma sintaxis que en HTML normal y es jQuery Mobile el encargado de realizar las transformaciones oportunas para adaptarlas a un entorno móvil.

Para añadir uno de estos elementos debemos utilizar la etiqueta `input` con el atributo `type="checkbox"` y su correspondiente etiqueta `label` con el atributo `for` correctamente asociado al identificador del `checkbox`.

Por último y al igual que sucedia con los elementos de tipo `radio`, tenemos que agrupar los campos en un `fieldset` con el atributo `data-role="controlgroup"`, y también podemos usar la etiqueta `legend` para indicar el título:


```html
<div data-role="fieldcontain">
 	<fieldset data-role="controlgroup">
		<legend>De acuerdo con los términos del contrato:</legend>
		<input type="checkbox" name="checkbox-1" id="checkbox-1" class="custom" />
		<label for="checkbox-1">Sí, estoy de acuerdo</label>
    </fieldset>
</div>
```

![Checkbox horizontal](images/web_jqm2/checkboxhorizontal.png "Checkbox horizontal")


Por defecto, los elementos de tipo checkbox aparecerán agrupados de forma vertical, tal y como se muestra en el siguiente ejemplo.


```html
<div data-role="fieldcontain">
 	<fieldset data-role="controlgroup">
		<legend>Indíquenos sus hobbies</legend>
		<input type="checkbox" name="musica" id="musica"/>
		<label for="musica">Música</label>
		<input type="checkbox" name="deporte" id="deporte"/>
		<label for="deporte">Deportes</label>
		<input type="checkbox" name="television" id="television"/>
		<label for="television">Televisión</label>
		<input type="checkbox" name="cine" id="cine"/>
		<label for="cine">Cine</label>
    </fieldset>
</div>
```


![Checkbox vertical](images/web_jqm2/verticalcheckbox.png "Checkbox vertical")


Por último, si en lugar de mostrarlos de forma vertical queremos hacerlo de forma horizontal, podemos añadir el atributo `data-type="horizontal"`.


```html
<div data-role="fieldcontain">
    <fieldset data-role="controlgroup" data-type="horizontal">
    	<legend>Estilo:</legend>
    	<input type="checkbox" name="bold" id="bold"/>
		<label for="bold">b</label>
		<input type="checkbox" name="cursive" id="cursive"/>
		<label for="cursive"><em>i</em></label>
		<input type="checkbox" name="underline" id="underline"/>
		<label for="underline">u</label>
    </fieldset>
</div>
```

![Checkbox horizontal](images/web_jqm2/horizontalcheckbox.png "Checkbox horizontal")



<!-- *********************************************************************** -->
#### Elementos de tipo select

Para terminar con los diferentes tipos de elementos de formulario, vamos a ver el elemento de tipo `select`. Este tipo nos permitirá seleccionar un solo elemento de una lista.

Como los elemementos de tipo radio y checkbox, éstos también tienen la sintaxis típica de HTML y será nuevamente jQuery Mobile quien se encargue de realizar las transformaciones oportunas para mejorar la experiencia del usuario de dispositivos móviles.

Para añadir un elemento de este tipo debemos utilizar la etiqueta `select` con una serie de elementos de tipo `option`. Debemos también relacionar este elemento con una etiqueta de tipo `label`. Además, tenemos que agrupar este elemento dentro de un elemento tipo `div` con el atributo `data-role="fieldcontain"`. Veamos un ejemplo:

```html
<div data-role="fieldcontain">
   <label for="tiposuscripcion">Tipo de suscripción:</label>
   <select name="tiposuscripcion" id="tiposuscripcion">
      <option value="diaria">Diaría</option>
      <option value="semanal">Semanal</option>
      <option value="mensual">Mensual</option>
      <option value="anual">Anual</option>
   </select>
</div>
```

![Select](images/web_jqm2/select.png "Select")


Como podemos comprobar en la imagen, este tipo de elementos se mostrarán de forma nativa en los diferentes dispositivos donde carguemos nuestra aplicación. Si queremos modificar este comportamiento y mostrar las opciones siempre de la misma forma y con algo más de estilo propio, podemos utilizar el atributo `data-native-menu="false"` en el elemento `select` obteniendo lo siguiente:


![Select no nativo](images/web_jqm2/selectnonativo.png "Select no nativo")


Si necesitas además en tu aplicación que tus usuarios puedan utilizar la sección múltiple, jQuery Mobile también nos va a facilitar esta labor. Únicamente debemos añadir a la etiqueta `select` el atributo `multiple="multiple"`.

Además, también debemos añadir un primer elemento **sin valor** que se mostrará como una cabecera del `select` o cuando no haya nada seleccionado.


```html
<div data-role="fieldcontain">
   <label for="tiposuscripcion">Tipo de suscripción:</label>
   <select name="tiposuscripcion" id="tiposuscripcion" multiple="multiple" data-native-menu="false">
	  <option>Selecciona opciones</option>
      <option value="diaria">Diaría</option>
      <option value="semanal">Semanal</option>
      <option value="mensual">Mensual</option>
      <option value="anual">Anual</option>
   </select>
</div>
```

![Select múltiple](images/web_jqm2/selectmultiple.png "Select múltiple")


Como vemos en la imagen, jQuery Mobile se encarga de poner un encabezado con la primera opción del `select` que además podremos cerrar con un botón también añadido automáticamente.



<!-- *********************************************************************** -->
### Botones

Una vez vistos todos los elementos de formulario, pasemos a ver los botones en jQuery Mobile. Los botones con jQuery Mobile se especifican como si fuera HTML normal, pero como siempre éstos se presentarán de una forma más atractiva para los clientes móviles.

También existe la posibilidad de pintar botones en nuestras aplicaciones a partir de un enlace simplemente añadiendoles el atributo `data-role="button"`.

```html
<a href="index.html" data-role="button">Botón con enlace</a>
```

![Botón con enlace](images/web_jqm2/buttonlink.png "Botón con enlace")


Por otro lado, si utilizamos la sintaxis típica para los botones en HTML, tenemos las siguientes representaciones:


```html
<button>Elemento button</button>
<input type="button" value="Input type=button" />
<input type="submit" value="Input type=submit" />
<input type="reset" value="Input type=reset" />
<input type="image" data-role="none" src="image-filename.png" value="Input type=image" />
```

![Botón con inputs](images/web_jqm2/buttoninputs.png "Botón con inputs")


Por defecto, los botones se muestra a nivel de bloque, esto es que ocuparán todo el ancho posible de la página. Si queremos pintar más de un botón en una misma línea, debemos utilizar el atributo `data-inline="true"` a cada botón.


```html
<a href="index.html" data-role="button" data-inline="true">Cancelar</a>
<a href="index.html" data-role="button" data-inline="true" data-theme="b">Guardar</a>
```

![Botón en línea](images/web_jqm2/buttonsinline.png "Botón en línea")


Pero además, jQuery Mobile nos permite agrupar botones tanto de forma vertical.


```html
<div data-role="controlgroup">
	<a href="index.html" data-role="button">Sí</a>
	<a href="index.html" data-role="button">No</a>
	<a href="index.html" data-role="button">Quizás</a>
</div>
```

![Botones agrupados](images/web_jqm2/buttonsgrouped.png "Botones agrupados")


Y en el caso que queramos mostrar los botones de forma horizontal, deberemos añadir el atributo `data-type="horizontal"` al contenedor con el atributo `data-role="controlgroup"`.


```html
<div data-role="controlgroup" data-type="horizontal">
	<a href="index.html" data-role="button">Sí</a>
	<a href="index.html" data-role="button">No</a>
	<a href="index.html" data-role="button">Quizás</a>
</div>
```

![Botones agrupados horizontalmente](images/web_jqm2/buttonsgroupedhorizontal.png "Botones agrupados horizontalmente")




<!-- *********************************************************************** -->
### Envío de formularios

Y por último, ahora que ya conocemos ampliamente como podemos preparar todo tipo de formularios con jQuery Mobile, vamos a pasar a ver como se envía la información cumplimentada en estos formularios a un servidor que la procese.

jQuery Mobile por defecto procesa el envío de los formularios mediante llamadas Ajax, creando incluso una transición entre el formulario y la página resultante. Para asegurarnos de que nuestro formulario se procesa correctamente, debemos especificar los atributos `action` y `method`. En caso de que no especifiquemos estos valores, el método pasado será `GET` y el atributo `action` será la misma página que contiene el formulario.

Los formularios incluso aceptan otros parámetros como `data-transition="pop"` y `data-direction="reverse"`. Incluso, si no queremos que el formulario sea procesado vía Ajax, podemos desactivar este comportamiento especificando el atributo `data-ajax="false"`. Además, también podemos especificar el atributo `target="_blank"`.














