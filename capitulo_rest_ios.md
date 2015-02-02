# Parsing y servicios REST en iOS

## Parsing en iOS

En la primera parte de esta sesión veremos cómo parsear la información recibida de un servidor, tanto en JSON como en XML.

### Parsing de JSON

El parsing de JSON no se incorporó al SDK de iOS hasta la versión 5.0. Anteriormente contábamos con diferentes librerías que podíamos incluir para realizar esta tarea, como *JSONKit* (<a href="https://github.com/johnezang/JSONKit">`https://github.com/johnezang/JSONKit`</a>) o *json-framework* (<a href="https://github.com/stig/json-framework/">`https://github.com/stig/json-framework/`</a>). Sin embargo, ahora podemos trabajar con JSON directamente con las clases de Cocoa Touch, sin necesidad de incluir ninguna librería adicional. Vamos a centrarnos en esta forma de trabajar.

Simplemente necesitaremos la clase `NSJSONSerialization`. A partir de ella obtendremos el contenido del JSON en una jerarquía de objetos `NSArray` y `NSDictionary`

```objectivec
NSError *error = nil;
NSData *data = ... // Contenido JSON obtenido de la red
id mensajes = [NSJSONSerialization JSONObjectWithData: data options:0 error:&error];

if(error==nil) {
    for(NSDictionary *mensaje in mensajes)
    {
        NSString *texto = [mensaje valueForKey:@"texto"];
        NSString *usuario = [mensaje valueForKey:@"usuario"];

        ...
    }
}
```

A veces la información JSON está en forma de diccionario, y otras organizada como un array.  El método `JSONObjectWithData:options:error:` de la clase `NSJSONSerialization` nos devolverá un `NSDictionary` o `NSArray` según si el elemento principal del JSON es un objeto o una lista, respectivamente.

Al igual que en el caso de Android, el objeto `NSJSONSerialization` también nos permite realizar la transformación en el sentido inverso, permitiendo transformar una jerarquía de objetos `NSArray` y `NSDictionary` en una representación JSON. Para eso contaremos con el método `dataWithJSONObject`:

```objectivec
NSDictionary *dict = [NSDictionary dictionaryWithObjectsAndKeys:
                          @"Hola!", @"texto",
                          @"Pepe",  @"usuario", nil];

NSData *json = [NSJSONSerialization dataWithJSONObject:dict options:0 error:&error];
```

### Parsing de XML

Para análisis de XML en iOS contamos en el SDK con `NSXMLParser`. Con esta librería el análisis se realiza de forma parecida a los parsers SAX de Java. Este es el parser principal incluido en el SDK y está escrito en Objective-C, aunque también contamos dentro del SDK con libxml2, escrito en C, que incluye tanto un parser SAX como DOM. También encontramos otras librerías que podemos incluir en nuestro proyecto como parsers DOM XML:

<table>
		<tr><th>Parser</th><th>URL</th><th></th><th></th></tr>
		<tr><td>TBXML</td><td colspan="3"><a href="http://www.tbxml.co.uk/">http://www.tbxml.co.uk/</a></td></tr>
		<tr><td>TouchXML</td><td colspan="3"><a href="https://github.com/TouchCode/TouchXML">https://github.com/TouchCode/TouchXML</a></td></tr>
		<tr><td>KissXML</td><td colspan="3"><a href="http://code.google.com/p/kissxml">http://code.google.com/p/kissxml</a></td></tr>
		<tr><td>TinyXML</td><td colspan="3"><a href="http://www.grinninglizard.com/tinyxml/">http://www.grinninglizard.com/tinyxml/</a></td></tr>
		<tr><td>GDataXML</td><td colspan="3"><a href="http://code.google.com/p/gdata-objectivec-client">http://code.google.com/p/gdata-objectivec-client</a></td></tr>
</table>

Nos vamos a centrar en el estudio de `NSXMLParser`, por ser el parser principal incluido en la API de Cocoa Touch.

Para implementar un parser con esta librería deberemos crear una clase que adopte el protocolo `NSXMLParserDelegate`, el cual define, entre otros, los siguientes métodos:

```objectivec
- (void) parser:(NSXMLParser *)parser
didStartElement:(NSString *)elementName
   namespaceURI:(NSString *)namespaceURI
  qualifiedName:(NSString *)qualifiedName
     attributes:(NSDictionary *)attributeDict;

- (void) parser:(NSXMLParser *)parser
  didEndElement:(NSString *)elementName
   namespaceURI:(NSString *)namespaceURI
  qualifiedName:(NSString *)qName;

- (void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)string;
```

Podemos observar que nos informa de tres tipos de eventos: `didStartElement`, `foundCharacters`, y `didEndElement`. El análisis del XML será secuencial, el parser irá leyendo el documento y nos irá notificando los elementos que encuentre. Cuando se abra una etiqueta, llamará al método `didStartElement` de nuestro parser, cuando encuentre texto llamará a `foundCharacters`, y cuando se cierra la etiqueta llamará a `didEndElement`. Será responsabilidad nuestra implementar de forma correcta estos tres eventos, y guardar la información de estado que necesitemos durante el análisis.

Por ejemplo, imaginemos un documento XML sencillo como el siguiente:

```xml
<![CDATA[<mensajes>
    <mensaje usuario="pepe">Hola, ¿qué tal?</mensaje>
    <mensaje usuario="ana">Fetén</mensaje>
</mensajes>]]>
```

Podemos analizarlo mediante un parser `NSXMLParser` como el siguiente:

```objectivec

- (void) parser:(NSXMLParser *)parser
    didStartElement:(NSString *)elementName
       namespaceURI:(NSString *)namespaceURI
      qualifiedName:(NSString *)qualifiedName
         attributes:(NSDictionary *)attributeDict
{
  if ([[elementName lowercaseString] isEqualToString:@"mensajes"]) {
    self.listaMensajes = [NSMutableArray arrayWithCapacity: 100];
  }
  else if([[elementName lowercaseString] isEqualToString:@"mensaje"]) {
    self.currentMensaje = [UAMensaje mensaje];
    self.currentMensaje.usuario = [attributeDict objectForKey:@"usuario"];
  }
  else {
    // No puede haber etiquetas distintas a mensajes o mensaje
    [parser abortParsing];
  }
}

-(void) parser:(NSXMLParser *)parser
    didEndElement:(NSString *)elementName
    namespaceURI:(NSString *)namespaceURI
    qualifiedName:(NSString *)qName
{
  if([[elementName lowercaseString] isEqualToString:@"mensaje"]) {
    [self.listaMensajes addObject: self.currentMensaje];
    self.currentMensaje = nil;
  }
}

- (void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)string
{
	NSString* value = [string stringByTrimmingCharactersInSet:
	             [NSCharacterSet whitespaceAndNewlineCharacterSet]];

	if([value length]!=0 && self.currentMensaje!=nil) {
        self.currentMensaje.texto = value;
	}
}
```

Podemos observar que cada vez que encuentra una etiqueta de apertura, podemos obtener tanto la etiqueta como sus atributos. Cada vez que se abre un nuevo mensaje, se crea un objeto de tipo `UAMensaje` en una variable de instancia, y se van introduciendo en él los datos que se encuentran en el XML, hasta encontrar la etiqueta de cierre (en nuestro caso el texto, aunque podríamos tener etiquetas anidadas).

Para que se ejecute el _parser_ que hemos implementado mediante el delegado, deberemos crear un objeto `NSXMLParser` y proporcionarle dicho delegado (en el siguiente ejemplo suponemos que nuestro objeto `self` hace de delegado). El parser se debe inicializar proporcionando el contenido XML a analizar (encapsulado en un objeto `NSData`):


```objectivec
NSXMLParser *parser = [[NSXMLParser alloc] initWithData: self.content];
parser.delegate = self;
BOOL result = [parser parse];
```

Tras inicializar el _parser_, lo ejecutamos llamando el método `parse`, que realizará el análisis de forma síncrona, y nos devolverá `YES` si todo ha ido bien, o `NO` si ha habido algún error al procesar la información (se producirá un error si en el delegado durante el _parsing_ llamamos a `[parser abortParsing]`).


## Acceso a servicios REST desde iOS

En iOS podemos acceder a servicios REST utilizando las clases para conectar con URLs vistas en anteriores sesiones. Por ejemplo, para hacer una consulta al servidor de OpenWeatherMap podríamos utilizar un código como el siguiente para iniciar la conexión (recordemos que este método de conexión es asíncrono, y los datos los recibirá posteriormente el objeto delegado especificado):

```objectivec
NSURL *url = [NSURL URLWithString:@"http://api.openweathermap.org/data/2.5/weather?q=Alicante"];
NSURLRequest *theRequest = [NSURLRequest requestWithURL: url];
NSURLConnection *theConnection = [NSURLConnection connectionWithRequest: theRequest delegate: self];
```

En el caso de Objective-C, si queremos realizar peticiones con métodos distintos a GET, deberemos utilizar la clase `NSMutableURLRequest`, en lugar de `NSURLRequest`, ya que esta última no nos permite modificar los datos de la petición, incluyendo el método HTTP. Podremos de esta forma establecer todos los datos necesarios para la petición al servicio: método HTTP, mensaje a enviar (por ejemplo XML o JSON), y cabeceras (para indicar el tipo de contenido enviado, o los tipos de representaciones que aceptamos). Por ejemplo:

```objectivec

NSURL *url = [NSURL URLWithString:@"http://localhost/videoclub/api/v1/catalog"];
NSData *datosPelicula = ... // Componer mensaje JSON con datos de la peli a crear

NSMutableURLRequest *theRequest = [NSMutableURLRequest requestWithURL: url];
[theRequest setHTTPMethod: @"POST"];
[theRequest setHTTPBody: datosPelicula];
[theRequest setValue:@"application/json" forHTTPHeaderField:@"Accept"];
[theRequest setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

NSURLConnection *theConnection = [NSURLConnection connectionWithRequest: theRequest delegate: self];
```

Podemos ver que en la petición POST hemos establecido todos los datos necesarios. Por un lado su bloque de contenido, con los datos del recurso que queremos añadir en la representación que consideremos adecuada. En este caso suponemos que utilizamos XML como representación. En tal caso hay que avisar de que el contenido lo enviamos con este formato, mediante la cabecera `Content-Type`, y de que la respuesta también queremos obtenerla en XML, mediante la cabecera `Accept`.

## Seguridad HTTP básica

Vamos a ver en primer lugar cómo acceder a servicios protegidos con seguridad HTTP estándar. En estos casos, deberemos proporcionar en la llamada al servicio las cabeceras de autentificación al servidor, con las credenciales que nos den acceso a las operaciones solicitadas.

<!--
Por ejemplo, desde un cliente Android en el que utilicemos la API de red est·ndar de Java SE deberemos definir
un <code>Authenticator</code> que proporcione estos datos:</p>

<source lang="java">Authenticator.setDefault(new Authenticator() {
    protected PasswordAuthentication getPasswordAuthentication() {
        return new PasswordAuthentication (
              "usuario", "password".toCharArray());
    }
});</source>

<p>En caso de que utilicemos HttpClient de Apache, se especificar· de la siguiente forma:</p>

<source lang="java">DefaultHttpClient client = new DefaultHttpClient();
client.getCredentialsProvider().setCredentials(
    new AuthScope("jtech.ua.es", 80),
    new UsernamePasswordCredentials("usuario", "password")
);</source>

<p>AquÌ adem·s de las credenciales, hay que indicar el ·mbito al que se aplican (host y puerto).</p>
-->

Para quienes no estén muy familiarizados con la seguridad en HTTP, conviene mencionar el funcionamiento del protocolo a grandes rasgos. Cuando realizamos una petición HTTP a un recurso protegido con seguridad
básica, HTTP nos devuelve una respuesta indicándonos que necesitamos autentificarnos para acceder. Es entonces cuando el cliente solicita al usuario las credenciales (usuario y password), y entonces se
realiza una nueva petición con dichas credenciales incluidas en una cabecera _Authorization_. Si las credenciales son válidas, el servidor nos dará acceso al contenido solicitado.

<!--
<p>Este es el funcionamiento habitual de la autentificación. En el caso del acceso mediante HttpClient que hemos visto anteriormente, el funcionamiento es el mismo, cuando el servidor nos pida autentificarnos
la librerÌa lanzar· una nueva peticiÛn con las credenciales especificadas en el proveedor de credenciales.</p>
-->

Sin embargo, si sabemos de antemano que un recurso va a necesitar autentificación, podemos tambiçen autentificarnos de forma preventiva. La autentificación preventiva consiste en mandar las credenciales en la primera petición, antes de que el servidor nos las solicite. Con esto ahorramos una petición, pero podríamos estar mandando las credenciales en casos en los que no resulta necesario.

<!--
Con HttpClient podemos activar o desactivar la autentificaciÛn preventiva con el siguiente mÈtodo:

<source lang="java">client.getParams().setAuthenticationPreemptive(true);</source>
-->

En iOS, la autentificación la deberá hacer el delegado de la conexión. Para ello deberemos implementar el método `connection:didReceiveAuthenticationChallenge:`. Este método será invocado cuando el servidor nos pida autentificarnos. En ese caso deberemos crear un objeto `NSURLCredential`
a partir de nuestras credenciales (usuario y password).

A continuación vemos un ejemplo típico de implementación:

```objectivec
- (void)connection:(NSURLConnection *)connection
    didReceiveAuthenticationChallenge:
        (NSURLAuthenticationChallenge *)challenge
{
    if(challenge.previousFailureCount == 0)
    {
        NSURLCredential *cred = [NSURLCredential credentialWithUser:@"mi_login"
                                                           password:@"mi_password"
                                    persistence:NSURLCredentialPersistenceNone];
        [[challenge sender] useCredential: cred forAuthenticationChallenge:challenge];
    }
    else
    {
        [[challenge sender] cancelAuthenticationChallenge: challenge];
    }
}
```

Podemos observar que comprobamos los fallos previos de autentificación que hemos tenido. Es decir, si con los credenciales que tenemos en el código ha fallado la autentificación, será mejor que cancelemos el acceso, ya que si volvemos a intentar acceder con los mismos credenciales vamos a tener el mismo error. En caso de que sea el primer intento, creamos los credenciales (podemos ver que se puede indicar que se guarden de forma persistente para los próximos accesos), y los utilizamos para responder al reto de autentificación (`NSURLAuthenticationChallenge`).

Este método nos permite autentificarnos siempre que realicemos una conexión asíncrona. Sin embargo, si queremos conectar de forma síncrona, o queremos autentificarnos de forma preventiva, esto resultará
bastante más complejo, ya que deberemos añadir las cabeceras de autentificación manualmente a la petición.

La cabecera que necesitamos es `Authorization`, a la que le proporcionaremos el tipo de autentificación y los credenciales (login y password). Por ejemplo, para utilizar seguridad de tipo BASIC
como en los casos anteriores, esta cabecera tendrá la siguiente forma:

```bash
Authorization: Basic login_base64:password_base64
```

La mayor dificultad radica en que el login y el password no se incluyen directamente, sino que deben estar codificados en base64. Necesitaremos por lo tanto una función que se encargue de realizar esta codificación, y no hay ninguna implementada en el SDK de iOS. Esto no es demasiado complejo de implementar, pero si no estamos familiarizados con este formato podemos encontrar diferentes implementaciones. Una de ellas es la implementación de Matt Gallagher: <a href="
http://cocoawithlove.com/2009/06/base64-encoding-options-on-mac-and.html">http://cocoawithlove.com/2009/06/base64-encoding-options-on-mac-and.html</a>

Esta implementación es bastante elegante, ya que se introduce como una categoría de `NSData`, lo cual añade a esta clase la capacidad de realizar la codificación y descodificación. Con ella podemos implementar la codificación de la siguiente forma:

```objectivec
NSString *cred = [NSString stringWithFormat:@"%@:%@", login, password];
NSString *credBase64 = [[cred dataUsingEncoding:NSUTF8StringEncoding]
                                                   base64EncodedString];
NSString *authHeader = [NSString stringWithFormat:@"Basic %@",
                                                  credBase64];

NSMutableURLRequest *theRequest = [NSMutableURLRequest requestWithURL: url];
[theRequest addValue:authHeader forHTTPHeaderField:@"Authorization"];
```

Donde `login` y `password` son variables de tipo `NSString` donde tenemos almacenados nuestros credenciales, y `url` es de tipo `NSURL` y contiene la URL a la que conectar.

En el código podemos ver que primero se genera la cadena de credenciales, concatenando `login:password`. A continuación esta cadena se codifica en base64, y con esto ya podemos añadir una cabecera `Authorization` cuyo valor sea la cadena `Basic <credenciales_base64>`

Si en lugar de acceder a estos servicios desde una aplicación nativa, lo hacemos desde Javascript, deberemos asegurarnos de que el usuario se ha autentificado
previamente en el sitio web que esté haciendo la llamada AJAX al servicio REST seguro.















# Ejercicios de servicios rest en iOS

## Weather app (2 puntos)

En este primer ejercicio vamos a practicar el parsing de XML y JSON. Para ello haremos una aplicación que nos permita visualizar el tiempo de una ciudad accediendo a la API de <a href="http://openweathermap.org/api">Openweathermap</a>.

Se proporciona una plantilla `Weather` que ya realiza la llamada asíncrona a la API. Según el usuario elija XML o JSON, la respuesta del servidor se recibirá en el formato correspondiente.

Se pide parsear la respuesta en ambos formatos para poder mostrar la información en pantalla. Sólo se solicitan unos pocos datos, que son la temperatura actual, la humedad, velocidad del viento, el país, y la descripción (que es un mensaje como por ejemplo _clear skies_).

Hay que completar los métodos `parseXML` y `parseJSON` para mostrar la información correspondiente en los outlets del interfaz.

De forma opcional, puedes implementar lo siguiente:

* Descargar la imagen del icono que se encuentra en el campo `icon` de la respuesta para mostrarlo en el `UIImageView` correspondiente. Ejemplo de una URL de acceso: http://openweathermap.org/img/w/10d.png

## Proyecto de iOS (7 puntos)

El proyecto consistirá en hacer una aplicación para iOS que gestione el videoclub que se ha implementado en sesiones anteriores. Partiremos de la aplicación `Pelis` implementada en un ejercicio anterior.

#### Listado

Vamos a cambiar en el `MasterViewController` la carga de películas, de manera que esta vez se haga una petición GET al servidor. Puedes usar localhost si ya lo tienes implementado, o gbrain si no es así.

Para empezar, descarga la clase `Connection` de los materiales. Esta clase realiza una conexión asíncrona como la que hemos visto anteriormente, evitando gestionarlo en las clases principales.

Prepara  `MasterViewController` para usar la clase `Connection`, añadiendo el protocolo `ConnectionDelegate` e implementando los métodos siguientes:

```objectivec
-(void)connectionSucceed:(Connection *)connection withData:(NSData *)data;
-(void)connectionFailed:(Connection *)connection withError:(NSError *)error;
```

Para establecer la conexión usando la nueva clase, añade lo siguiente:

```objectivec
    self.theConnection=[[Connection alloc] initWithURLRequest:theRequest];
    self.theConnection.delegate=self;
```

Si tras guardar los datos no se muestra nada en la tabla, probablemente debas añadir la siguiente instrucción:

```objectivec
[self.tableView reloadData];
```

Los datos JSON de las películas están almacenados en un array. Añade el campo `identif` de tipo _NSString_ a la clase `Peli` y guárdalo cuando leas del JSON el valor del id. Te hará falta para implementar el resto de opciones.

#### Alquilar / devolver

En la vista `detailViewController`, implementa la opción de alquiler mostrando un botón (en cualquier parte visible) que indique el estado actual con el texto _Alquilada_ o _Libre_, y que pueda pulsarse para cambiar su estado.


Si la conexión ha sido válida y se ha alquilado o devuelto la película, se debe actualizar el campo `rented` y el título del botón en el interfaz.

Como esta petición requiere autentificación, también necesitarás añadir el siguiente método a la clase `Connection`:

```objectivec
- (void)connection:(NSURLConnection *)connection didReceiveAuthenticationChallenge: (NSURLAuthenticationChallenge *)challenge
```

Y especificar el usuario y contraseña cuando se crea la conexión.

#### Añadir

Vamos a poner la opción de añadir una película. Para esto, puedes descomentar el código de `addButton` en `viewDidLoad`.

En el método `insertNewObject` tendremos que mostrar una vista  para pedir los datos de la película a añadir. Para ello se proporciona como material la clase `PelisTableViewController`. El método quedaría así:

```objectivec
- (void)insertNewObject:(id)sender {
    if (!self.pelis) {
        self.pelis = [[NSMutableArray alloc] init];
    }

    PelisTableViewController *pelisAdd=[[PelisTableViewController alloc] init];
    pelisAdd.delegate=self;

    [self.navigationController pushViewController:pelisAdd animated:YES];
}
```

En `PelisTableViewController` deberás implementar el método `save` que se llama cuando el usuario pulsa el botón para guardar la película. También debes modificar `connectionSucceed` para actualizar el identificador de la película que se ha recibido, antes de devolverla al delegado mediante el método `saved`.

Hay que implementar dicho método `saved` en `MasterViewController` para actualizar el array local de películas sin tener que volver a hacer una llamada al servidor. Es conveniente crear un nuevo método `init` en la clase `Pelis` para inicializar los datos de una película vacía. Las nuevas películas estarán libres por defecto.

#### Eliminar

Descomenta el código de edición de las tablas en `MasterViewController`, tanto en el método `viewDidLoad` como `commitEditingStyle`, y devolviendo `YES` en `canEditRowAtIndexPath`.

Haz los cambios correspondientes para que cuando se elimine una película en la tabla también se haga en el servidor, y que si falla en el servidor no se llegue a borrar en la tabla.

Al tener dos tipos de conexiones en el mismo controlador, necesitarás identificar cuál de ellas es la que ha hecho la petición. Para ello, créate una nueva propiedad, por ejemplo:

```objectivec
@property (nonatomic,strong) Connection *theConnectionDelete;
```
Y puedes saber desde qué conexión se ha recibido la respuesta en el método

```objectivec
-(void)connectionSucceed:(NSURLConnection *)connection withData:(NSData *)data
{
    if (connection == self.theConnection) {
        // Hacer algo
    }
    else if (connection == self.theConnectionDelete) {
        // Hacer otra cosa
    }
}
```

#### Modificar

Añade un botón `Edit` en la barra de navegación de `DetailViewController` para editar una película. Cuando el usuario lo pulse, deberá poder modificar los datos de la película usando el mismo controlador que para añadirla (`PelisTableViewController`). Ojo, tendrás que hacer una copia de la película a modificar por si el usuario cancela los cambios volviendo atrás sin guardarla.

En esta opción en lugar de mostrarse en la barra de navegación _New movie_ deberá indicarse el nombre de la película, y cuando se guarde deberá actualizarse tanto en el listado local como en el servidor.

#### Apartados opcionales:

* Usar un formulario para pedir usuario y contraseña la primera vez que se necesite. Se puede usar un `UIAlertView`, con el estilo `UIAlertViewStyleLoginAndPasswordInput`, y guardar el usuario y la contraseña en `[NSUserDefaults standardUserDefaults]`. En realidad, la información sensible como las contraseñas debe guardarse en el KeyChain, pero esto se verá más adelante en la asignatura de persistencia.

* Añadir opción de búsqueda de películas con servicios REST. Para esto se debe implementar la búsqueda en el servidor mediante querystring. En la app se puede mostrar esta opción en la escena `MasterViewController`, reemplazando el botón _Edit_, ya que en realidad el borrado se puede hacer sin él.
