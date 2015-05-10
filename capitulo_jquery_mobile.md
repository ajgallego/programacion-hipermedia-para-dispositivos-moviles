



<!-- ********************************************************************* -->
#### Precarga y caché de páginas

A continuación vamos a ver algunas técnicas presentes en jQuery Mobile para mejorar la experiencia del usuario a la hora de navegar por nuestra aplicación.


<!-- ********************************************************************* -->
#### Precarga de páginas

Si en tu aplicación utilizas un documento html con una sola página web, pero sin embargo, prefieres cargar el contenido de determinadas páginas sin mostrar la típica imagen de _"Cargando..."_, podemos hacer una precarga de estas páginas simplemente añadiendo el atributo `data-prefetch` a cualquier enlace que queremos precargar. jQuery Mobile se encargará de cargar el contenido de esta página enlazada sin que el usuario vea nada. Este podría ser un ejemplo:

```html
<a href="enlaceprecargado.html" data-prefetch>Enlace precargado</a>
```

Así es como funciona internamente jQuery Mobile en estas precargas. Una vez la página principal se ha cargado por completo, jQuery Mobile buscará entre todos los enlaces aquellos que tengan el atributo `data-prefetch` para automáticamente cargar el contenido de esos enlaces. De esta forma, cuando el usuario haga clic en estos enlaces, la carga del contenido se hará mucho más rápida que se haría si no hubiéramos hecho esta precarga. Además, la imagen de _"Cargando..."_ ya no volverá a aparecer salvo que por cualquier motivo, el contenido de la página enlazada todavía no se haya podido cargar en nuestra aplicación.

Es importante saber que estas precargas realizarán una serie de peticiones que en ocasiones nunca se utilizarán, con lo que debemos sólo utilizar esta precarga en determinadas situaciones y cuando sepamos con un alto grado de certeza que el usuario hará clic en ese enlace.


<!-- ********************************************************************* -->
### Caché de páginas

Cuando se realizan las transiciones entre las diferentes páginas de nuestra aplicación, debemos tener en cuenta que ambas páginas deben estar cargadas en el DOM y que conforme vamos navegando por las mismas, más páginas se irán añadiendo a este DOM, lo que en versiones anteriores de jQuery Mobile provocaba en ocasiones que el rendimiento de la aplicación bajara o que incluso el navegador se cerrara inesperadamente.

Para solucionar este problema, jQuery Mobile se encarga de gestionar las páginas almacenadas en el DOM y lo hace añadiendo un _flag_ a estas páginas una vez ya hemos accedido a otra página del DOM. En caso de que volvamos a una página que ya haya sido eliminada del DOM previamente, el navegador intentará cargar la página de su propia caché o será de nuevo solicitada al servidor. Esto sucede únicamente con aquellas páginas que se cargan vía Ajax y no con los documentos html multi-páginas.

Sin embargo, jQuery Mobile también especifica una forma de gestionar nosotros mismos la caché de determinadas páginas de nuestra aplicación que consideremos interesantes. Esta gestión de la caché del DOM, supone que nosotros somos quienes deberemos encargarnos de controlar que el tamaño del mismo no exceda unos determinados límites.

Podemos hacerlo de dos formas. Por un lado aplicando esta gestión de la caché a toda nuestra aplicación.

```java
$.mobile.page.prototype.options.domCache = true;
```

O bien especificarlo únicamente en determinadas páginas mediante el atributo `data-dom-cache="true"`.


```html
<a href="foo/bar/baz" data-dom-cache="true">link text</a>
```



<!-- ********************************************************************* -->
## Estilo de los componentes jQuery Mobile

jQuery Mobile dispone de un sistema muy robusto y sencillo para estilizar nuestra aplicación de varias formas. Cada componente de jQuery Mobile tiene la posibilidad de ser estilizado de una forma diferente. Para aplicar estos estilos, estos componentes pueden añadir el atributo `data-theme` y puede tomar los valores, _a, b, c, d o e_ para elegir cualquiera de los cinco temas de los que dispone actualmente jQuery Mobile. Cada uno de estos temas, utiliza una serie de combinaciones de colores y formas diferentes. Por defecto, jQuery Mobile utiliza una combinación de estos temas tal y como vemos en las siguientes imágenes.

**Tema por defecto**

![Tema por defecto](images/web_jqm/temapordefecto.png "Tema por defecto")

**Tema A**

![Tema A](images/web_jqm/temaa.png "Tema A")

**Tema B**

![Tema B](images/web_jqm/temab.png "Tema B")

**Tema C**

![Tema C](images/web_jqm/temac.png "Tema C")

**Tema D**

![Tema D](images/web_jqm/temad.png "Tema D")

**Tema E**

![Tema E](images/web_jqm/temae.png "Tema E")


**Nota:** A partir de la versión 1.4 de JQuery Mobile solo se incluyen los temas A y B en la hoja de estilo por defecto. Para incluir el resto de temas tenemos que cargar la siguiente hoja de estilo:

```css
<link rel="stylesheet" href="http://demos.jquerymobile.com/1.4.5/theme-classic/theme-classic.css" />
```

Otra opción es generar nuestros propios temas mediante la utilidad _Theme Roller_ de JQuery Mobile: http://themeroller.jquerymobile.com/ Esta utilidad nos permite crear temas basándonos en alguno de los ya predefinidos y después personalizarlos o directamente generar nuestro propio tema. Por último nos da la opción de descargar la hoja de estilo.



<!-- ********************************************************************* -->
### Barras de herramientas

Las barras de herramientas son utilizadas habitualmente en las cabeceras y los pies de nuestras aplicaciones en cualquier aplicación web móvil. Por este motivo, jQuery Mobile nos ofrece una serie de componentes ya preparados para ser utilizados en nuestras aplicaciones.



<!-- ********************************************************************* -->
### Tipos de barras de herramientas

En una aplicación jQuery Mobile estándar, veremos dos tipos de barras de herramientas: las cabeceras (_headers_) y los pies de página (_footers_):

* La **barra de herramientas de la cabecera** se utiliza para indicar el título de la página actual, casi siempre es lo primero que se muestra en la aplicación y es aconsejable que como mucho tenga dos botones, uno a la izquierda del título y otro a la derecha.

* La **barra de herramientas del pie de página** es habitualmente el último elemento en cada página y los desarrolladores la pueden utilizar de diversas formas, aunque lo normal es que haya una combinación entre texto y botones.

También es muy habitual que las aplicaciones realizadas con jQuery Mobile utilicen una navegación horizontal que suele estar incluida en la cabecera y/o en el pie de página. Para ello, jQuery Mobile introduce una barra de navegación predeterminada que convierte una lista desordenada de enlaces en una barra horizontal del siguiente estilo:

![Barra de navegación](images/web_jqm/navbar.png "Barra de navegación")

Para facilitarnos aún más el trabajo con estas barras de herramientas, jQuery Mobile facilita incluso el posicionamiento de éstas en una aplicación. Por defecto, estas barras se colocan una detrás de la otra del mismo modo en el que se definen en nuestro documento html en lo que se conoce como posición _inline_.

Sin embargo, en ocasiones deseamos que una determinada barra de herramientas esté siempre visible en nuestra aplicación para facilitar su uso por parte del usuario en lo que se conoce como posición _fixed_. La barra de herramientas aparecerá en la misma posición como si hubiera sido definida de tipo _inline_, pero en cuanto el usuario haga _scroll_ en la aplicación, la barra de herramientas se desplazará para ocupar una posición visible en la aplicación.

Incluso estas barras de herramientas desaparecerán y aparecerán de nuestra aplicación con cada contacto que el usuario haga en el dispositivo móvil. Para indicar que una barra de herramientas debe tener este tipo de posición podemos poner el atributo `data-position="fixed"`.

Por otro lado, jQuery Mobile también dispone del modo a _pantalla completa_ para las barras de herramientas. Básicamente funcionan de la misma forma que la posición _fixed_, salvo que ésta ya no se muestra al inicio o al final de la página y sólo se muestra cuando el usuario hace clic sobre la página. Este tipo de posicionamiento es muy útil en aplicaciones donde se muestren fotos o vídeos, en los cuales queremos cargar el contenido a pantalla completa y esta barra de herramientas únicamente se mostrará cuando el usuario toque la pantalla.

Para habilitar esta característica en nuestras aplicaciones debemos especificar el atributo `data-fullscreen="true"` al elemento `div` que contiene el atributo `data-role="page"` y además, debemos indicar también el atributo `data-position="fixed"` en la cabecera y en el pie de la página.



<!-- ********************************************************************* -->
#### Cabeceras

Como comentábamos anteriormente, la cabecera se sitúa al inicio de las páginas y habitualmente contiene un título y opcionalmente puede tener hasta dos botones a los lados del título. Para el título de la cabecera habitualmente se utiliza el encabezado `<h1>`, aunque también posible utilizar cualquiera de los otros encabezados (_h1-h6_). Por ejemplo, un documento html multi-página puede tener un título de tipo _h1_ en la primera página y un título _h2_ en la segunda, sin embargo, por defecto, jQuery Mobile los mostrará con el mismo estilo por motivos de consistencia visual. Comentar también que por defecto, las cabeceras utilizan el estilo _a_ (fondo negro y letras en blanco).



<!-- ********************************************************************* -->
### Botones

En la configuración estándar de las cabeceras se ha dejado un espacio para añadir hasta dos botones a ambos lados del título y habitualmente se utilizan enlaces como botones. jQuery Mobile localiza el primero de estos enlaces y lo coloca automáticamente a la izquierda del título y el segundo enlace (en caso de que lo haya), a la derecha del título.

```html
<div data-role="header" data-position="inline">
	<a href="index.html" data-icon="delete">Cancel</a>
	<h1>Edit Contact</h1>
	<a href="index.html" data-icon="check">Save</a>
</div>
```

![Cabecera con botones](images/web_jqm/editcontact1.png "Cabecera con botones")

Los botones automáticamente adoptan el mismo estilo que la barra que los contiene, pero tal y como hemos visto antes, podemos modificar este diseño para mostrar otro diseño a los usuarios.

```html
<div data-role="header" data-position="inline">
	<a href="index.html" data-icon="delete">Cancel</a>
	<h1>Edit Contact</h1>
	<a href="index.html" data-icon="check" data-theme="b">Save</a>
</div>
```

![Cabecera con botones](images/web_jqm/editcontact2.png "Cabecera con botones")


Pero, ¿qué pasa si queremos controlar la posición donde se pinta el botón? Pues que la gente de jQuery Mobile ya ha pensado en esto y simplemente añadiendo el atributo `class` con los valores `ui-btn-left` o `ui-btn-right` al enlace en cuestión podremos indicar donde queremos que aparezca el botón.


```html
<div data-role="header" data-position="inline">
	<h1>Page Title</h1>
	<a href="index.html" data-icon="gear" class="ui-btn-right">Options</a>
</div>
```


![Controlando la posición de los botones](images/web_jqm/buttonatright.png "Controlando la posición de los botones")


Además de estos botones, jQuery Mobile también pone a disposición de los desarrolladores la posibilidad de que automáticamente se cree un botón para volver atrás en las páginas de nuestras aplicaciones. Sin embargo, por defecto esta opción está desactivada. Para activarla únicamente debemos especificar en aquellas páginas donde queramos insertar automáticamente este botón el atributo `data-add-back-btn="true"`

Esto colocará un botón a la izquierda del título que permitirá al usuario volver atrás. Sin embargo, este texto aparecerá en inglés, cosa que no siempre será lo deseado. Vamos a poder modificar el texto que aparece indicando el atributo `data-back-btn-text="Atrás"` en la página en cuestión. De igual forma también podremos modificar el tema por defecto de este botón Atrás con el atributo `data-back-btn-theme="e"`.

Pero además de esta forma, también podemos crear nosotros mismos nuestros botones para volver atrás simplemente especificando el atributo `data-rel="back"` a un enlace en cuestión. Además, recuerda que también tenemos la posibilidad de mostrar una transición inversa con el atributo `data-direction="reverse"`.



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



<!-- *********************************************************************** -->
<!-- *********************************************************************** -->
<!-- *********************************************************************** -->
<!-- *********************************************************************** -->



<!-- *********************************************************************** -->
# Ejercicios - Introducción a jQuery Mobile

En esta sección de ejercicios vamos a desarrollar una primera parte de una aplicación destinada a la gestión de una biblioteca de un centro educativo. Esta biblioteca básicamente contará por supuesto con _libros_ y con una serie de tipos de _usuario_ que podrán realizar una serie de _operaciones_.


## Ejercicio 1 - Inicio de la aplicación (1 punto)

En este primer ejercicio vamos a empezar por preparar la interfaz de usuario de lo que será nuestra aplicación y a definir el comportamiento de determinadas pantallas de la misma. Empecemos creando la página que será la portada de nuestra aplicación. Esta página debe contener una cabecera indicando el nombre de nuestra aplicación que llamaremos "iBiblioteca".

A continuación, vamos a definir lo que será el contenido de esa página de inicio. Aquí vamos a redactar un pequeño texto formateado explicando brevemente el funcionamiento de la aplicación. Que no se te olvide indicar también quien ha desarrollado esta aplicación.

> Las páginas de inicio en el mundo de las aplicaciones web para móvil no son nada deseables ya que lo más recomendable es que el usuario pueda directamente interactuar con la aplicación sin necesidad de aterrizar en una página de inicio que siempre muestra la misma información.

Por último, crear también un pie de página con una barra de navegación que nos servirá para tener siempre disponible una serie de opciones en cada página de nuestra aplicación. En esta primera página de la aplicación las opciones que vamos a añadir serán las siguientes:

* Botón para configurar una serie de ajustes en nuestra aplicación.
* Botón para mostrar una pequeña ayuda de lo que nuestra aplicación puede hacer.

No olvides poner unos iconos de acuerdo a la funcionalidad de cada botón y asegúrate también que esta barra de herramientas está siempre visible por el usuario. De momento estos botones no tienen que funcionar.

A continuación se incluye una captura de esta primera pantalla:

![Ejercicio 1](images/web_jqm/ejercicio_1.png "Ejercicio 1")

El archivo html creado en este ejercicio se debe llamar _index.html_ y debe estar en la raíz de un directorio llamado ibiblioteca.


## Ejercicio 2 - Página del autor (1 punto)

En el ejercicio anterior hemos indicado en la página de bienvenida a la aplicación quien es el autor de la misma, sin embargo, no hemos añadido más información sobre el mismo. En este ejercicio vamos a crear un nuevo documento html con toda la información del autor y en él practicaremos con la estructura de navegación para que el usuario pueda moverse por la aplicación sin problemas.

Este nuevo documento html relativo al autor tendrá más de una página (documento multipágina) y en cada una de ellas añadiremos unos datos diferentes. En la primera página se indicará el nombre completo, dirección de email y los teléfonos de contacto. Además habrá dos botones que os llevarán a una fotografía del autor y a su currículum. A continuación se incluye una captura de ejemplo:

![Captura](images/web_jqm/ejercicio_2.png "Captura")

Al pulsar sobre el botón de fotografía se abrirá un diálogo que únicamente tendrá el título "Fotografía" y una imagen. Y al pulsar sobre el botón de currículum nos llevará a una segunda página con información relativa a nuestro puesto actual (estudiante, programador, analista, arquitecto, etc) y un pequeño listado de los estudios o puestos ocupados recientemente, cada uno de ellos con un enlace que nos llevarán a su vez a una tercera página donde se explicará con más detalle el puesto ocupado o los estudios realizados. Se han de incluir como mínimo 3 enlaces de este tipo.

Ten en cuenta que siempre debemos volver atrás en todas las pantallas de nuestra aplicación. Recuerda también especificar una transición idónea para estos enlaces y añadir el enlace que nos llevé desde la página de "_index.html_" de bienvenida a esta nueva página.

El archivo html creado en este ejercicio se debe llamar _author.html_ y debe estar en la raíz de un directorio llamado ibiblioteca. Para la fotografía del autor, crea un directorio llamado _images_ y ponla en la raíz de este directorio.


## Ejercicio 3 - Listado de libros (1 punto)

En este ejercicio vamos a preparar una página en la que mostrar todos los libros de nuestra biblioteca y para ello utilizaremos los _acordeones_. En estos acordeones presentaremos en primer lugar el título del libro y una vez hagamos clic en ellos, mostraremos otras opciones de los libros como son el autor, el isbn y la editorial.

En la plantilla de estos ejercicios tenéis disponible un xml con libros de ejemplo y sus correspondientes imágenes de las portadas de los libros.

En cada libro, además mostraremos una barra de navegación (tipo _navbar_) con tres opciones: ver la portada del libro (la cual se tendrá que abrir en un diálogo), reservar el libro o marcarlo como favorito (estos dos botones últimos botones no funcionarán). Utiliza un tema diferente en estos enlaces (de tipo "e") para resaltar las opciones. A continuación se incluye una captura de como tiene que quedar esta sección:

![Captura](images/web_jqm/ejercicio_3.png "Captura")

El archivo html creado en este ejercicio se debe llamar _books.html_ y debe estar en la raíz de un directorio llamado ibiblioteca. Actualiza también la pantalla principal de la aplicación para que al pulsar sobre el enlace del listado de libros nos lleve a esta nueva pantalla.





