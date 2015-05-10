


<!-- ********************************************************************* -->
### Pies de página

Ahora que ya conocemos como podemos modificar el comportamiento de las cabeceras con jQuery Mobile, pasemos a ver en profundidad los pies de página, que básicamente tienen la misma estructura que las cabeceras con la salvedad, claro está, del atributo `data-role="footer"`.

```html
<div  data-role="footer">
	<h4>Pie de página</h4>
</div>
```

Estructuralmente, los pies de páginas son muy parecidos a las cabeceras, con la salvedad que en los pies de página, jQuery Mobile no añade automáticamente esos botones que veíamos anteriormente a ambos lados del título, con lo que si queremos mostrar botones, los vamos a tener que pintar nosotros mismos.

Cualquier enlace válido añadido en el pie de página podemos convertirlo automáticamente en un botón en nuestra aplicación. Para ello debemos utilizar el atributo `data-role="button"`. Por defecto, jQuery Mobile no añade ningún tipo de espaciado entre los botones y los laterales del navegador, con lo que si queremos que no aparezcan demasiado pegados a esos laterales, podemos utilizar el atributo `class="ui-bar"`.

```html
<div data-role="footer" class="ui-bar">
	<a href="index.html" data-role="button" data-icon="delete">Remove</a>
	<a href="index.html" data-role="button" data-icon="plus">Add</a>
	<a href="index.html" data-role="button" data-icon="arrow-u">Up</a>
	<a href="index.html" data-role="button" data-icon="arrow-d">Down</a>
</div>
```

![Pie de página](images/web_jqm/footbar1.png "Pie de página")


Incluso podemos agrupar los botones con los atributos `data-role="controlgroup"` y `data-type="horizontal"`.


```html
<div data-role="footer" class="ui-bar">
	<div data-role="controlgroup" data-type="horizontal">
		<a href="index.html" data-role="button" data-icon="delete">Remove</a>
		<a href="index.html" data-role="button" data-icon="plus">Add</a>
		<a href="index.html" data-role="button" data-icon="arrow-u">Up</a>
		<a href="index.html" data-role="button" data-icon="arrow-d">Down</a>
	</div>
</div>
```

![Pie de página](images/web_jqm/footbar2.png "Pie de página")




<!-- ********************************************************************* -->
## Barras de navegación

jQuery Mobile tiene también una característica que permite añadir barras de navegación en nuestras aplicaciones. Estas barras permiten hasta cinco botones con la posibilidad incluso de añadir iconos y normalmente se sitúan dentro de la cabecera o del pie de página.

Una barra de navegación no es más que una lista desordenada de enlaces que se encuentra dentro de elemento con el atributo `data-role="navbar"`. Si queremos marcar una determinada opción de esta barra de navegación como activa podemos especificar el atributo `class="ui-btn-active"` en el enlace.

```html
<div data-role="footer">
	<div data-role="navbar">
		<ul>
			<li><a href="a.html" class="ui-btn-active">One</a></li>
			<li><a href="b.html">Two</a></li>
		</ul>
	</div><!-- /navbar -->
</div><!-- /footer -->
```

![Barra de navegación](images/web_jqm/navbar2.png "Barra de navegación")

A medida que vayamos aumentando el número de botones en la barra de navegación, jQuery Mobile se encargará automáticamente de posicionarlos correctamente en el navegador, tal y como vemos en las siguientes imágenes.

![Con 3 botones](images/web_jqm/navbar3.png "Con 3 botones")

![Con 4 botones](images/web_jqm/navbar4.png "Con 4 botones")

![Con 5 botones](images/web_jqm/navbar5.png "Con 5 botones")

![Con 6 botones](images/web_jqm/navbar6.png "Con 6 botones")

Comentar también que los botones de nuestras barras de navegación pueden ir acompañados de iconos que mejoren la experiencia del usuario final. Estos iconos se pueden añadir a los enlaces mediante el atributo `data-icon` especificándole un valor de entre los siguientes:

* arrow-l
* arrow-r
* arrow-u
* arrow-d
* delete
* plus
* minus
* check
* gear
* refresh
* forward
* back
* grid
* star
* alert
* info
* home
* search

Por último, comentar también que estos iconos aparecen por defecto a la izquierda del texto, pero si queremos colocarlos en cualquier otro lugar podemos utilizar el atributo `data-iconpos` con cualquiera de estos valores:

* left
* right
* top
* bottom


<!-- ********************************************************************* -->
### Formateo de contenido

El contenido de las páginas de una aplicación desarrollada con jQuery Mobile es totalmente abierto, aunque como siempre, siguiendo una serie de convenciones, el framework nos ayudará muchísimo en nuestra tarea.	En esta sección veremos algunos aspectos interesantes como son los paneles desplegables y los diseños para múltiples columnas que nos facilitarán el formateo de contenido para aplicaciones móviles.



<!-- ********************************************************************* -->
### HTML básico

Lo más importante de jQuery Mobile es que para formatear contenido no tenemos más que aplicar los conocimientos del lenguaje HTML. Por su parte, el framework se encargará de modificar la apariencia de nuestras aplicaciones.

Por defecto, jQuery Mobile utiliza los estilos y tamaños estándar de HTML, tal y como se muestran en los siguientes ejemplos:

```html
<h1>Cabecera H1</h1>
<h2>Cabecera H2</h2>
<h3>Cabecera H3</h3>
<h4>Cabecera H4</h4>
<h5>Cabecera H5</h5>
<h6>Cabecera H6</h6>
```

![Cabeceras](images/web_jqm/headers.png "Cabeceras")


```html
<ol>
	<li>Lista desordenada item 1</li>
	<li>Lista desordenada item 2</li>
	<li>Lista desordenada item 3</li>
</ol>
```

![Listas desordenadas](images/web_jqm/unorderedlist.png "Listas desordenadas")


```html
<table>
	<thead>
		<tr>
			<th>ISBN</th>
			<th>Título</th>
			<th>Autor</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>843992688X</td>
			<td>La colmena</td>
			<td>Camilo José Cela Trulock</td>
		</tr>
		<tr>
			<td>0936388110</td>
			<td>La galatea</td>
			<td>Miguel de Cervantes Saavedra</td>
		</tr>
		<tr>
			<td>8437624045</td>
			<td>La dragontea</td>
			<td>Félix Lope de Vega y Carpio</td>
		</tr>
	</tbody>
</table>
```

![Tablas](images/web_jqm/tables.png "Tablas")


Como puedes observar, la apariencia de la mayoría de los componentes HTML no difieren prácticamente en nada de lo que ya conoces del desarrollo de aplicaciones web de _escritorio_.

Sin embargo, a continuación vamos a ver algunos componentes HTML que enmarcados dentro de une web desarrollada con jQuery Mobile facilitará la labor de estructuración de la información en una aplicación web para móviles.



<!-- ********************************************************************* -->
### Diseños por columnas

Aunque el diseño por columnas no es muy habitual verlo en aplicaciones web para móviles debido a la propia idiosincrasia de los dispositivos móviles y su amplitud, en algunas ocasiones este tipo de diseños se hace necesario para aprovechar al máximo esta amplitud.

jQuery Mobile tiene como convención para este tipo de diseños una clase llamada _ui-grid_ y que básicamente utiliza una serie de diseños CSS para generar un diseño por columnas, en el que se permiten hasta un máximo de 5 columnas.

En función del número de columnas que necesitemos en nuestro diseños, la capa contenedora debe tener el atributo `class` a uno de estos valores:

* 2 columnas: ui-grid-a
* 3 columnas: ui-grid-b
* 4 columnas: ui-grid-c
* 5 columnas: ui-grid-d

Veamos el siguiente código y analicémoslo:

```html
<div class="ui-grid-a">
	<div class="ui-block-a"><div class="ui-bar ui-bar-a">Block A</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-b">Block B</div></div>
</div>
<br/>
<div class="ui-grid-b">
	<div class="ui-block-a"><div class="ui-bar ui-bar-a">Block A</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-b">Block B</div></div>
	<div class="ui-block-c"><div class="ui-bar ui-bar-c">Block C</div></div>
</div>
<br/>

<div class="ui-grid-c">
	<div class="ui-block-a"><div class="ui-bar ui-bar-a">Block A</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-b">Block B</div></div>
	<div class="ui-block-c"><div class="ui-bar ui-bar-c">Block C</div></div>
	<div class="ui-block-d"><div class="ui-bar ui-bar-d">Block D</div></div>
</div>
<br/>

<div class="ui-grid-d">
	<div class="ui-block-a"><div class="ui-bar ui-bar-a">Block A</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-b">Block B</div></div>
	<div class="ui-block-c"><div class="ui-bar ui-bar-c">Block C</div></div>
	<div class="ui-block-d"><div class="ui-bar ui-bar-d">Block D</div></div>
	<div class="ui-block-e"><div class="ui-bar ui-bar-e">Block E</div></div>
</div>
```

Este código HTML produciría la siguiente salida:

![Diseño por columnas](images/web_jqm/gridlayout.png "Diseño por columnas")


Como vemos en la imagen, cada bloque está identificado por el atribudo `class="ui-grid"` diferente en función del número de columnas. Posteriormente, cada elemento debe estar a su vez contenido por una capa con el atributo `class="ui-block"` que indicará la posición del contenido en el diseño por columnas. Por último, tener en cuenta que el diseño del contenido mostrado, dependerá del atributo `class="ui-bar"` especificado, tal y como se muestra en la imagen.

Por último comentar también que la idea de los diseños por columnas es organizar en filas una serie de elementos. En ocasiones, nos puede interesar agrupar en un diseño de tres columnas a nueve elementos, con lo que jQuery Mobile metería tres elementos por cada fila tal y como vemos en el siguiente ejemplo.


```html
<div class="ui-grid-b">
	<div class="ui-block-a"><div class="ui-bar ui-bar-e">Block 1</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-e">Block 2</div></div>
	<div class="ui-block-c"><div class="ui-bar ui-bar-e">Block 3</div></div>
	<div class="ui-block-a"><div class="ui-bar ui-bar-e">Block 4</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-e">Block 5</div></div>
	<div class="ui-block-c"><div class="ui-bar ui-bar-e">Block 6</div></div>
	<div class="ui-block-a"><div class="ui-bar ui-bar-e">Block 7</div></div>
	<div class="ui-block-b"><div class="ui-bar ui-bar-e">Block 8</div></div>
	<div class="ui-block-c"><div class="ui-bar ui-bar-e">Block 9</div></div>
</div>
```

![Multiples columnas y filas](images/web_jqm/multiplecolumns.png "Multiples columnas y filas")



<!-- ********************************************************************* -->
### Contenido desplegable

jQuery Mobile permite incluso agrupar contenido que se mostrará en forma de desplegable cuando el usuario haga clic sobre el mismo. El formato de este tipo de contenidos es muy sencillo y simplemente debemos utilizar el atributo `data-role="collapsible"` seguido de un elemento de encabezado, como por ejemplo `<h3/>` y el contenido a mostrar. jQuery Mobile se encargará incluso de pintar un botón para que el usuario detecte que puede hacer clic sobre éste, tal y como vemos en el siguiente ejemplo.


```html
<div data-role="collapsible">
	<h3>Contenido desplegable</h3>
	<p>
		Este contenido puede ser mostrado y
		ocultado por el usuario simplemente haciendo
		clic en la cabecera
	</p>
</div>
```

![Contenido desplegable](images/web_jqm/collapsiblecontent.png "Contenido desplegable")

Como puedes observar, este contenido aparece desplegado al cargar la página. Si quieres que el mismo aparezca contraído debes añadir el atributo `data-collapsed="true"` de la siguiente forma:


```html
<div data-role="collapsible" data-collapsed="true"></div>
```

Comentar por último que el contenido de estos desplegables puede ir desde un simple párrafo hasta incluso un formulario y que incluso vamos a poder insertar un contenido desplegable dentro de otro, tal y como vemos en este ejemplo.


```html
<div data-role="collapsible">
	<h3>Contenido desplegable</h3>
	<p>Este contenido está dentro de un desplegable...</p>
	<div data-role="collapsible">
		<h4>más información</h4>
		<p>... que a su vez tiene más contenido desplegable</p>
	</div>
</div>
```

![Contenido desplegable anidado](images/web_jqm/nestedcollapsible.png "Contenido desplegable anidado")




<!-- *********************************************************************** -->
### Contenidos desplegables agrupados (acordeones)

Los acordeones no son más que un conjunto de contenidos desplegables de tal forma que al hacer clic sobre uno de ellos, el resto se ocultarán automáticamente, con lo que nunca podrá haber más de uno elemento desplegado al mismo tiempo.

La sintaxis de estos acordeones es prácticamente la misma que veíamos anteriormente salvo que ahora debemos añadir un elemento que agrupará a todos estos contenidos desplegables y al cual le debemos asignar el atributo `data-role="collapsible-set"`. Si queremos mostrar de inicio alguno de estos desplegables, debemos asignar el atributo `data-collapsed="false"`.

```html
<div data-role="collapsible-set">
	<div data-role="collapsible" data-collapsed="true">
		<h3>Sección A</h3>
		<p>Contenido desplega para A.</p>
	</div>
	<div data-role="collapsible" data-collapsed="true">
		<h3>Sección B</h3>
		<p>Contenido desplega para B.</p>
	</div>
	<div data-role="collapsible" data-collapsed="false">
		<h3>Sección C</h3>
		<p>Contenido desplega para C.</p>
	</div>
	<div data-role="collapsible" data-collapsed="true">
		<h3>Sección D</h3>
		<p>Contenido desplega para D.</p>
	</div>
</div>
```


![Grupo de elementos desplegables](images/web_jqm/collapsibleset.png "Grupo de elementos desplegables")




