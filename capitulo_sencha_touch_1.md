





<!-- ********************************* -->
### _Pack_ y _Align_

Las características _pack_ y _align_ del _layout_ sirven para controlar la alineación de los elementos hijos dentro de un contenedor:

* _Pack_: es la alineación en el mismo eje de alineación del _layout_ utilizado. Por ejemplo, en un _layout_ tipo _hbox_ sería el horizontal y en uno tipo _vbox_ el vertical. Se pueden asignar tres posibles valores: _start_, _center_ y _end_.
* _Align_: es la alineación en el eje perpendicular al de la alineación del _layout_ utilizado. Por ejemplo, en un _layout_ del tipo _hbox_ será la vertical y en uno del tipo _vbox_ la horizontal. Puede tener cuatro posibles valores: _start_, _center_, _end_ y _stretch_.

A continuación se incluye un ejemplo de su utilización:

```javascript
Ext.create('Ext.Panel', {
    fullscreen: true,
    layout: {
        type: 'hbox',
        align: 'center',
        pack: 'center'
    },
    items: {
    	xtype: 'panel',
    	html: 'Contenido centrado.'
    }
});
```




<!-- ********************************************************************* -->
## Toolbars

Hasta ahora hemos visto como utilizar paneles y contenedores (`Ext.Panel` y `Ext.Container`), en esta sección vamos a ver como añadir barras de herramientas dentro de estos elementos.

Para añadir barras de herramientas a un panel tenemos dos opciones, igual que para otro tipo de componentes, estas son:

* Crear la barra de herramientas de forma separada usando el constructor "`var toolbar = Ext.create('Ext.Toolbar', { ... });`" y posteriormente añadirla al panel en su atributo "`items`" usando su nombre de variable.
* Crear la barra de herramientas directamente en el atributo "`items`" del panel. Para esto definiremos el tipo de componente usando el `xtype: 'toolbar'`.


En ambos casos, dentro del constructor podemos usar los siguientes atributos:

* **id: 'identificador'**: Atributo opcional para indicar el identificador de la barra.
* **docked: 'top'** o **docked: 'bottom'**: indica que la barra se coloque en la parte superior o en la parte inferior del panel respectivamente.
* **title: 'texto'**: indica el texto que se colocará en el centro de la barra.
* **ui: valor**: atributo opcional que cambia la apariencia de la barra. Por defecto toma el valor "_dark_", pero también podemos usar "_light_" que aplicará unos colores más claros.


En el siguiente ejemplo se crean dos barras de herramientas y se añaden a un panel. La primera se crea de forma separada y después se añaden al panel usando su nombre de variable. La segunda barra se crea en línea usando su _xtype_:

```javascript
var topToolbar = Ext.create('Ext.Toolbar', {
  docked: 'top',
  title: 'Top Toolbar'
});

var panel = Ext.create('Ext.Panel', {
  fullscreen: true,
  layout: 'fit',
  html: 'Contenido central',
  items: [
    topToolbar,
    {
        xtype: 'toolbar',
        dock: 'bottom',
        title: 'Bottom Toolbar'
    }
  ]
});
```

Con lo que obtendríamos un resultado similar a:

![](images/web_sencha/sencha_toolbars.jpg)





<!-- ********************************************************************* -->
## Botones

Los botones se añaden a las barras de herramientas (Toolbar) u otro tipo de componentes mediante su propiedad `items`. La forma de construir un botón, igual que para otros componentes, son dos:

* Mediante su constructor creamos el botón y lo asignamos a una variable:  `var button = Ext.create('Ext.Button', {...});`. Posteriormente podemos asignar dicha variable al atributo `items` de algún contenedor.
* Definir el botón directamente dentro del atributo `items` de algún contenedor mediante el `xtype: 'button'`.

Además tenemos una serie de propiedades que podemos configurar:
* _text_: texto del botón.
* _ui_: tipo o apariencia del botón.
* _iconCls_: icono del botón.

En el siguiente ejemplo se crea una barra de herramientas con dos botones, uno creado de forma _inline_ y otro de forma separada:


```javascript
var button1 = Ext.create('Ext.Button', {
  text: 'Mi botón 1'
});

var topToolbar = Ext.create('Ext.Toolbar', {
  dock: 'top',
  title: 'mi barra',
  items: [
        button1,
        {
            xtype: 'button',
            ui: 'action',
            text: 'Mi botón 2'
        }
  ]
});
```

Podemos usar siete tipos **ui** predefinidos de botones, estos son:

* ui: 'normal'
* ui: 'back'
* ui: 'forward'
* ui: 'round'
* ui: 'action'
* ui: 'confirm'
* ui: 'decline'

![](images/web_sencha/sencha_buttons.jpg)

Además podemos usar los modificadores **"-small"** y **"-round"** sobre los tipos de botón "action", "confirm" y "decline" para obtener botones más pequeños o redondeados:

![](images/web_sencha/sencha_buttons_modificadores.jpg)

Si no indicamos un tipo (ui) por defecto nos aparecerá el botón tipo "normal".

Si queremos variar el **ancho** de un botón podemos utilizar la propiedad "width: '200px'" en píxeles o "width: '95%'" indicando porcentajes.



**Iconos**

También podemos usar algunos iconos predefinidos, indicando el nombre del icono mediante la propiedad **"iconCls: 'nombre'"**, de la forma:

```javascript
var button = Ext.create('Ext.Button', {
  iconCls: 'action'
});
```

Los iconos que podemos utilizar son:

<table>
	<tr>
		<td>
			![](images/web_sencha/sencha_button_action.jpg) action
		</td><td>
			![](images/web_sencha/sencha_buttons_add.jpg)   add
		</td><td>
  			![](images/web_sencha/sencha_buttons_arrow_down.jpg)   arrow_down
		</td></tr><tr><td>
	  		![](images/web_sencha/sencha_buttons_arrow_left.jpg)   arrow_left
		</td><td>
  			![](images/web_sencha/sencha_buttons_arrow_right.jpg)   arrow_right
		</td><td>
	  		![](images/web_sencha/sencha_buttons_arrow_up.jpg)   arrow_up
		</td></tr><tr><td>
  			![](images/web_sencha/sencha_buttons_compose.jpg)   compose
		</td><td>
	  		![](images/web_sencha/sencha_buttons_delete.jpg)   delete
  		</td><td>
  			![](images/web_sencha/sencha_buttons_organize.jpg)   organize
		</td></tr><tr><td>
  			![](images/web_sencha/sencha_buttons_refresh.jpg)   refresh
  		</td><td>
	  		![](images/web_sencha/sencha_buttons_reply.jpg)   reply
  		</td><td>
  			![](images/web_sencha/sencha_buttons_search.jpg)   search
  		</td></tr><tr><td>
  			![](images/web_sencha/sencha_buttons_settings.jpg)   settings
  		</td><td>
  			![](images/web_sencha/sencha_buttons_star.jpg)   star
		</td><td>
	  		![](images/web_sencha/sencha_buttons_trash.jpg)   trash
  		</td></tr><tr><td>
  			![](images/web_sencha/sencha_buttons_maps.jpg)   maps
  		</td><td>
  			![](images/web_sencha/sencha_buttons_locate.jpg)   locate
  		</td><td>
	  		![](images/web_sencha/sencha_buttons_home.jpg)   home
  		</td></tr>
</table>

Si además usamos la propiedad **"text: 'texto'"** al definir el botón, el texto aparecerá a la derecha del icono:

![](images/web_sencha/sencha_buttons_icon_text.jpg)


Opcionalmente podemos aplicar colores a estos iconos, aplicándole los tipos (ui) de 'action', 'decline' o 'confirm', obteniendo:

![](images/web_sencha/sencha_buttons_icon_colors.jpg)



**Imágenes externas**

Si queremos usar una imagen externa tenemos que aplicar al botón un estilo CSS. El botón lo definimos de forma normal, pero utilizamos su propiedad `cls` para indicar el nombre del estilo:


```javascript
items: [ { xtype: 'button', ui: 'normal', cls: 'btnAyuda' } ]
```

El estilo "btnAyuda" lo tendremos que definir indicando la imagen de fondo a usar junto con el ancho y el alto del botón. El tamaño de la imagen que usemos deberá de coincidir con el tamaño aplicado al botón para que no se vea recortado. El tamaño habitual de un botón es de 45x35 píxeles. Además es muy importante añadir en el CSS la propiedad `!important` al cargar la imagen de fondo. Esto es debido a que Sencha Touch sobrescribe algunos estilos y tenemos que aplicar esta propiedad para que prevalezca el nuestro:

```css
.btnAyuda {
	background: url(resources/imgs/ayuda.png) !important;
	width: 45px;
	height: 35px;
}
```


**Badges**

De forma sencilla podemos añadir una insignia distintiva a los botones para destacar alguna información. Para esto utilizamos la propiedad **"badgeText: '2'"**, que daría como resultado:

![](images/web_sencha/sencha_buttons_badge.jpg)



**Alineaciones**

Por defecto los botones salen alineados a la izquierda. Para crear otras alineaciones utilizaremos un espaciador **"{ xtype: 'spacer' }"**. En el siguiente código podemos ver diferentes ejemplos de alineaciones:

```javascript
// Alineación derecha
items: [
  { xtype: 'spacer' },
  { xtype: 'button', ui: 'normal', text: 'Botón' }
]
// Alineación centrada
items: [
  { xtype: 'spacer' },
  { xtype: 'button', ui: 'normal', text: 'Botón' },
  { xtype: 'spacer' }
]
```


**Acciones**

Para añadir acciones a los botones tenemos que definir su propiedad "**handler**", a la cual le asignaremos una función. Esta función la podemos definir en línea, de la forma `handler: function () { ... }`, o creando una función independiente para separar mejor el código, como en el ejemplo:

```javascript
function botonPresionado(btn, evt) {
	alert("Presionado " + btn.text);
}

var topToolbar = Ext.create('Ext.Toolbar', {
  	items: [
        {
            xtype: 'button',
            ui: 'normal',
            text: 'Botón 1',
            handler: botonPresionado
        },
        {
            xtype: 'button',
            ui: 'action',
            text: 'Botón 2',
            handler: function(btn, evt) {
				alert("Presionado " + btn.text);
            }
        }]
});
```










