

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














