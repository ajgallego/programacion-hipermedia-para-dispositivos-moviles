





<!-- ********************************************************************* -->
## Formularios

En esta sección vamos a ver como podemos cargar, guardar y validar los datos de un formulario.


<!-- ********************************************************************* -->
### Cargar datos en un formulario

Para insertar datos de un modelo en un formulario utilizamos el método "`setRecord( data )`" de la clase "`Ext.form.Panel`". En el siguiente ejemplo se crea un formulario con dos campos llamados "_title_" y "_text_" y a continuación se cargan los datos del registro "_note_".

```javascript
var noteEditor = Ext.create('Ext.form.Panel', {
  id: 'noteEditor',
  fullscreen: true,
  items: [ {
      xtype: 'textfield',
      name: 'title',
      label: 'Title',
      required: true
    }, {
      xtype: 'textareafield',
      name: 'narrative',
      label: 'Narrative'
    } ]
});

noteEditor.setRecord( note );
```

El método "`setRecord( data )`" recibe como parámetro una instancia de un modelo de datos (ver sección _Data Model_), del cual cargará solamente los campos cuyos nombre coincidan con los establecidos en el formulario. En este formulario tenemos dos campos: "`name: 'title'`" y "`name: 'text'`", si cargamos una instancia de un modelo de datos como el descrito a continuación, solamente se cargarían los dos campos que coinciden.


```javascript
Ext.define('Note', {
    extend: 'Ext.data.Model',
    config: {
        fields: [
            { name: 'id', type: 'int' },
            { name: 'date', type: 'date', dateFormat: 'c' },
            { name: 'title', type: 'string' },
            { name: 'text', type: 'string' }
        ]
    }
});
```

En la sección de "_Data Model_" ya hemos visto que podemos usar la función "`Ext.create('<nombre-modelo>', {lista-valores})`" para crear instancias de un modelo de datos. En el siguiente ejemplo se crea una instancia del modelo anterior y se añade al formulario "noteEditor" que habíamos definido anteriormente. En este caso, al asignar los datos al formulario, solo se cargarían los campos "_title_" y "_text_".


```javascript
var note = Ext.create('Note', {
    id: 1,
	date: new Date(),
	title: 'titulo',
	text: 'texto'
});

noteEditor.setRecord( note );
```


A continuación se incluye un ejemplo un poco más avanzado: Creamos una barra de herramientas a la que añadimos un botón con el texto "Cargar datos". Para este botón definimos su función "`handler`", de forma que al pulsar el botón se crea una instancia del modelo 'Note' y posteriormente se carga en el formulario 'noteEditor'.

```javascript
var myToolbar = Ext.create('Ext.Toolbar', {
    docked: 'top',
    title: 'Mi barra',
    items: [
    	{
    	    xtype: 'button',
    	    ui: 'action',
    	    text: 'Cargar datos',
    	    handler: function() {
    	        var now = new Date();
                var noteId = now.getTime();
                var note = Ext.create('Note', {
                    id: noteId,
                	date: now,
                	title: 'titulo',
                	text: 'texto'
                });
                noteEditor.setRecord( note );
    	    }
    	}
    ]
});
```




<!-- ********************************************************************* -->
### Guardar los datos de un formulario

Para guardar los datos de un formulario en general tendremos que seguir cuatro pasos:

* En primer lugar llamamos al método `getRecord()` del formulario para obtener su modelo de datos asociado, el cual devolverá un objeto del tipo `Ext.data.Model` con la definición de los campos utilizados en el formulario, pero no sus valores.

* A continuación llamamos a la función `updateRecord(objeto)` del mismo formulario para transferir los valores introducidos a la instancia del modelo que hemos obtenido antes.

* En tercer lugar tenemos que realizar el proceso de validación de los datos (explicado en el siguiente apartado).

* Y por último guardar los datos en el _store_ correspondiente. Si tenemos una instancia del almacén de datos creada (ver sección _Data Store_) podemos añadir los datos llamando a su función `add`, de la forma:<br/>

```javascript
notesStore.add(currentNote);
```

En el siguiente código de ejemplo se resumen los cuatro pasos que habría que seguir para cargar los datos del formulario 'noteEditor' y guardarlos en el almacén 'notesStore'.


```javascript
// Cargar el modelo de datos
var currentNote = noteEditor.getRecord();

// Actualizar el modelo con los datos del formulario
noteEditor.updateRecord(currentNote);

// Realizar validaciones
// (ver siguiente apartado)

// Guardar los datos
notesStore.add(currentNote);
```


Más comúnmente nuestro almacén estará asociado con algún elemento que nos permita visualizar los datos (como un listado o un _Data View_, ver secciones correspondientes). Si por ejemplo fuera un listado deberíamos de obtener la instancia del almacén de datos llamando a su método `getStore()` y posteriormente añadir los datos que habíamos obtenido del formulario de la forma:

```javascript
var notesStore = notesList.getStore();
notesStore.add( currentNote );
```

Opcionalmente podemos comprobar si los datos a añadir están repetidos. Para esto tenemos que utilizar el método `findRecord()` del _store_ (ver sección _Data Store_).


```javascript
var notesStore = notesList.getStore();

if( notesStore.findRecord('id', currentNote.data.id) === null)
{
   notesStore.add( currentNote );
}
```


Para terminar con el ejemplo del listado, una vez añadidos los datos tendremos que sincronizar su _store_, ordenarlo (si fuese necesario) y por último actualizar o refrescar la vista del listado:


```javascript
notesStore.sync();
notesStore.sort([{ property: 'date', direction: 'DESC'}]);

notesList.refresh();
```



<!-- ********************************************************************* -->
### Comprobar validaciones

Para comprobar las validaciones de un formulario lo tenemos que hacer de forma manual llamando a la función `validate()` del modelo de datos asociado. Para esto tienen que estar definidas estas validaciones en el modelo. Continuando con el ejemplo del modelo de datos "Note", vamos a añadir que los campos `id`, `title` y `text` sean requeridos:

```javascript
Ext.define('User', {
    extend: 'Ext.data.Model',
    fields: [
        { name: 'id', type: 'int' },
        { name: 'date', type: 'date', dateFormat: 'c' },
        { name: 'title', type: 'string' },
        { name: 'text', type: 'string' }
    ],
    validations: [
        { type: 'presence', field: 'id' },
        { type: 'presence', field: 'title',
          message: 'Introduzca un título para esta nota' },
        { type: 'presence', field: 'narrative',
          message: 'Introduzca un texto para esta nota' }
    ]
});
```

Los pasos a seguir para realizar la validación de un formulario son los siguientes:

* Obtener el modelo de datos asociado a un formulario (`getRecord()`) y rellenarlo con los datos introducidos por el usuario (`updateRecord()`).

* Llamar a la función `validate()` del modelo de datos. Esta función comprueba que se cumplan todas las validaciones que estén definidas en dicho modelo, devolviendo un objeto del tipo `Errors`.

* A continuación usaremos la función `isValid()` del objeto `Errors` para comprobar si ha habido algún error. Esta función devuelve un valor _booleano_ indicando si existen errores o no.

* En caso de que existan errores tendremos que mostrar un aviso con los errores y no realizar ninguna acción más.

* Dado que pueden haber varios errores (guardados en el array `items` del objeto `Errors`), tenemos que iterar por los elementos de este array usando la función `Ext.each(array, funcion)`. Esta función recibe dos parámetros: el primero es el array sobre el que va a iterar y el segundo la función que se llamará en cada iteración. Esta función recibe a su vez dos parámetros: el item de la iteración actual y el índice de ese item en el array.

* Para mostrar el aviso con los errores podemos usar un `MessageBox` (ver sección correspondiente).


A continuación se incluye un ejemplo completo de la validación de un formulario:


```javascript
// Cargar el modelo de datos
var currentNote = noteEditor.getRecord();

// Actualizar el modelo con los datos del formulario
noteEditor.updateRecord(currentNote);

// Realizar validaciones
var errors = currentNote.validate();

if(!errors.isValid())
{
    var message="";

    Ext.each( errors.items, function(item, index) {
        message += item.getMessage() + "<br/>";
    });

    Ext.Msg.alert("Errores", message, Ext.emptyFn);

    return;   // Terminamos si hay errores
}

// Almacenar datos
notesStore.add(currentNote);
```

También podríamos haber creado un bucle para iterar entre los elementos del array de errores, o haber llamado a la función `errors.getByField('title')[0].getMessage()` para obtener directamente el mensaje de error de un campo en concreto.



<!-- *********************************************************************** -->
## Más información

Podemos consultar principalmente tres fuentes de información cuando tengamos alguna duda:

* Los **tutoriales y la sección de FAQ** en la página Web de Sencha Touch:
<br/>
http://www.sencha.com/

* La **documentación API** Online:

  * http://docs.sencha.com/touch/2.4/2.4.1-apidocs/

  * http://www.sencha.com/learn/touch/

  * También disponible de forma local accediendo en la dirección "/docs" del SDK descargado.

* Los **foros** en la página web de Sencha Touch.


Además en la carpeta "touch-sdk/examples" podemos encontrar aplicaciones de ejemplo.






<!-- ********************************************************************* -->
<!-- ********************************************************************* -->
<!-- ********************************************************************* -->
<!-- ********************************************************************* -->











