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

En iOS podemos acceder a servicios REST utilizando las clases para conectar con URLs vistas en anteriores sesiones. Por ejemplo, para hacer una consulta al servidor de OpenWeatherMap podríamos utilizar un código como el siguiente para iniciar la conexión (recordemos que este método de conexión es asíncrono:

```objectivec
NSURL *url = [NSURL URLWithString:@"http://api.openweathermap.org/data/2.5/weather?q=Alicante"];
// A partir de aquí es una conexión normal como la que hemos visto en la sesión anterior
NSURLRequest *theRequest = [NSURLRequest requestWithURL: url];

NSURLSessionConfiguration *config=[NSURLSessionConfiguration defaultSessionConfiguration];
NSURLSession session = [NSURLSession sessionWithConfiguration:config];

[[session dataTaskWithRequest:request
 completionHandler:^(NSData *data,
                NSURLResponse *response,
                NSError *error)
                {
                    // La respuesta del servidor se recibe en este punto
                }] resume];
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
```

Podemos ver que en la petición POST hemos establecido todos los datos necesarios. Por un lado su bloque de contenido, con los datos del recurso que queremos añadir en la representación que consideremos adecuada. En este caso suponemos que utilizamos XML como representación. En tal caso hay que avisar de que el contenido lo enviamos con este formato, mediante la cabecera `Content-Type`, y de que la respuesta también queremos obtenerla en XML, mediante la cabecera `Accept`.

## Seguridad HTTP

Vamos a ver en primer lugar cómo acceder a servicios protegidos con seguridad HTTP estándar. En estos casos, deberemos proporcionar en la llamada al servicio las cabeceras de autentificación al servidor, con las credenciales que nos den acceso a las operaciones solicitadas.

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

### Autentificación no preventiva

En iOS, la autentificación la puede hacer el delegado de la conexión. Cuando enviamos una petición sin credenciales, el sevidor nos responderá que necesita autentificación invocando al método delegado `didReceiveChallenge` del protocolo `NSURLSessionDelegate`. En este método, que puede ser a nivel de sesión o de tarea, podremos especificar los datos de la acreditación.

* A nivel de sesión, se invoca ``URLSession:didReceiveChallenge:completionHandler`` cuando el servidor requiere autentificación. Este método debe usarse para servidores con SSL/TLS, o cuando todas las peticiones que hagamos para una sesión necesiten la misma acreditación.

* A nivel de petición, podemos usar el método ``URLSession:task:didReceiveChallenge:completionHandler:`` si el servidor requiere autentificación para una tarea en concreto. Esto es necesario si las peticiones de una misma sesión requieren acreditaciones distintas.

Para usar estos métodos con autentificación Basic debemos crear un objeto `NSURLCredential` a partir de nuestras credenciales (usuario y password). A continuación vemos un ejemplo típico de implementación de una autenticación a nivel de sesión:

```objectivec
- (void)URLSession:(NSURLSession *)session
didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge
  completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler
{
    if(challenge.previousFailureCount == 0)
    {
        NSURLCredential *credential = [NSURLCredential credentialWithUser:@"mi_login"
                                                           password:@"mi_password"
                                    persistence:NSURLCredentialPersistenceForSession];

        completionHandler(NSURLSessionAuthChallengeUseCredential, credential);
    }
    else
    {
        NSLog(@"%s: challenge.error = %@", __FUNCTION__, challenge.error);
        completionHandler(NSURLSessionAuthChallengeCancelAuthenticationChallenge, nil);
    }
}
```

Y a nivel de tarea sería exactamente igual, pero usando el siguiente prototipo, esta vez del protocolo `NSURLSessionTaskDelegate`:

```objectivec
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler
```

Si es necesaria la autentificación y no implementamos el método a nivel de sesión, entonces iOS llama automáticamente al método a nivel de tarea.

El tipo de persistencia puede ser:
* ``NSURLCredentialPersistenceNone``: Las credenciales no se guardan
* ``NSURLCredentialPersistenceForSession``: Las credenciales se guardan para esta sesión
* ``NSURLCredentialPersistencePermanent``: Las credenciales se guardan en el _keychain_
* ``NSURLCredentialPersistenceSynchronizable``: Las credenciales se guardan en el _keychain_, y además se comparten entre dispositivos iOS.

Podemos observar que comprobamos los fallos previos de autentificación que hemos tenido. Es decir, si con los credenciales que tenemos en el código ha fallado la autentificación, será mejor que cancelemos el acceso, ya que si volvemos a intentar acceder con los mismos credenciales vamos a tener el mismo error. En caso de que sea el primer intento, creamos los credenciales (podemos ver que se puede indicar que se guarden de forma persistente para los próximos accesos), y los utilizamos para responder al reto de autentificación (`NSURLAuthenticationChallenge`).

### Autentificación preventiva

Este tipo de autentificación se suele usar para servicios REST, y consiste en enviar las credenciales antes de que las pida el servidor. De este modo nos ahorramos un mensaje de petición y de respuesta, por lo que es más eficiente y ahorramos ancho de banda. Este esquema es recomendable cuando sabemos de antemano que nuestra petición va a necesitar autentificación.

Esto también lo podemos hacer a nivel de sesión o de tarea. En cualquier caso, deberemos codificar en Base64 la cadena para autentificación. En el caso de autentificación Basic, dados un usuario y una contraseña podemos generar la cadena del siguiente modo:

```objectivec
NSString *login=@"my_user";
NSString *password=@"my_password";

NSString *authStr = [NSString stringWithFormat:@"%@:%@", login, password];
NSData *authData = [authStr dataUsingEncoding:NSUTF8StringEncoding];
NSString *authValue = [NSString stringWithFormat: @"Basic %@",[authData base64EncodedStringWithOptions:0]];
```

En el código podemos ver que primero se genera la cadena de credenciales, concatenando `login:password`. A continuación esta cadena se codifica en base64, y con esto ya podemos añadir una cabecera `Authorization` cuyo valor es la cadena `Basic <credenciales_base64>`.

Una vez tengamos la cadnea de las credenciales, si queremos añadir la autentificación preventiva a la sesión podemos especificarla en la configuración:

```objectivec
NSURLSessionConfiguration *config=[NSURLSessionConfiguration defaultSessionConfiguration];

config.HTTPAdditionalHeaders = @{@"Accept": @"application/json",
                                 @"Authorization": authString};

 NSURLSession *session=[NSURLSession sessionWithConfiguration:config];
```

Si en lugar de hacerlo a nivel de sesión lo que queremos es añadir esta autentificación a una petición, tenemos que modificar su request:

```objectivec
NSMutableURLRequest *req=[request mutableCopy]; // Porque no podemos modificar un NSURLRequest
[req setValue:authValue forHTTPHeaderField:@"Authorization"];

// En req tenemos el request actualizado
```

# Ejercicios de servicios rest en iOS

## Weather app (2 puntos)

En este primer ejercicio vamos a practicar el parsing de XML y JSON. Para ello haremos una aplicación que nos permita visualizar el tiempo de una ciudad accediendo a la API de <a href="http://openweathermap.org/api">Openweathermap</a>.

Se proporciona una plantilla `Weather` que ya realiza la llamada asíncrona a la API. Según el usuario elija XML o JSON, la respuesta del servidor se recibirá en el formato correspondiente.

Se pide parsear la respuesta en ambos formatos para poder mostrar la información en pantalla. Sólo se solicitan unos pocos datos, que son la temperatura actual, la humedad, velocidad del viento, el país, y la descripción (que es un mensaje, como por ejemplo _clear skies_).

Hay que completar los métodos `parseXML` y `parseJSON` para mostrar la información correspondiente en los outlets del interfaz.

De forma opcional, puedes implementar lo siguiente:

* Descargar la imagen del icono que se encuentra en el campo `icon` de la respuesta para mostrarlo en el `UIImageView` correspondiente. Para esto puedes descargar la imagen con ``dataTaskWithURL``. Ejemplo de la URL correspondiente al icono `10d`: http://openweathermap.org/img/w/10d.png

## Proyecto de iOS (7 puntos)

El proyecto consistirá en hacer una aplicación para iOS que gestione el videoclub que se ha implementado en sesiones anteriores. Partiremos de la aplicación `Pelis` implementada en un ejercicio anterior.

#### Listado

Vamos a cambiar en el `MasterViewController` la carga de películas, de manera que esta vez se haga una petición GET al servidor. Puedes usar localhost si ya lo tienes implementado, o gbrain si no es así.

Para empezar, añade al proyecto la clase `Connection` que hemos implementado en el ejercicio anterior. Prepara `MasterViewController` para usar esta clase, añadiendo el protocolo `ConnectionDelegate` e implementando los métodos siguientes:

```objectivec
-(void)connectionSucceed:(Connection *)connection withData:(NSData *)data;
-(void)connectionFailed:(Connection *)connection withError:(NSString *)errorMessage;
```

Implementa el código para hacer la petición del listado de películas desde el método ``createPelis``. Si tras recibir los datos no se muestra nada en la tabla, probablemente debas añadir la siguiente instrucción:

```objectivec
[self.tableView reloadData];
```

Los datos JSON de las películas están almacenados en un array. Añade el campo `identif` de tipo _NSString_ a la clase `Peli` y guárdalo cuando leas del JSON el valor del _id_. Esto te hará falta para implementar el resto de opciones.

#### Alquilar / devolver

En la vista `detailViewController`, implementa la opción de alquiler mostrando un botón (en cualquier parte visible) que indique el estado actual con el texto _Alquilada_ o _Libre_, y que pueda pulsarse para cambiar su estado.

Si la conexión ha sido válida y se ha alquilado o devuelto la película, se debe actualizar el campo `rented` y el título del botón en el interfaz.

Esta petición requiere autentificación. Puedes hacerla de tipo preventivo y a nivel de tarea, añadiendo por ejemplo un método adicional a la clase `Connection` que acepte usuario y contraseña para hacer la petición.

#### Añadir

Vamos a implementar la opción para añadir una película. Para esto, puedes descomentar el código de `addButton` en `viewDidLoad`.

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

También que implementar dicho método `saved` en `MasterViewController` para actualizar el array local de películas sin tener que volver a hacer una llamada al servidor. Es conveniente crear un nuevo método `init` en la clase `Pelis` para inicializar los datos de una película vacía. Las nuevas películas estarán libres por defecto.

#### Eliminar

Descomenta el código de edición de las tablas en `MasterViewController`, tanto en el método `viewDidLoad` como `commitEditingStyle`, y devolviendo `YES` en `canEditRowAtIndexPath`.

Haz los cambios correspondientes para que cuando se elimine una película en la tabla también se haga en el servidor, de modo que si falla en el servidor no se llegue a borrar en la tabla.

Al tener dos tipos de conexiones en el mismo controlador, necesitarás identificar cuál de ellas es la que ha hecho la petición. Para ello, créate una nueva propiedad, por ejemplo:

```objectivec
@property (nonatomic,strong) Connection *theConnectionDelete;
```
Y puedes saber desde qué conexión se ha recibido la respuesta en el método

```objectivec
-(void)connectionSucceed:(Connection *)connection withData:(NSData *)data
{
    if (connection == self.theConnection) {
        // Hacer algo
    }
    else if (connection == self.theConnectionDelete) {
        // Hacer otra cosa
    }
}
```

#### Editar

Añade un botón `Edit` en la barra de navegación de `DetailViewController` para editar una película. Cuando el usuario lo pulse, deberá poder modificar los datos de la película usando el mismo controlador que para añadirla (`PelisTableViewController`). Ojo, tendrás que hacer una copia de la película a modificar por si el usuario cancela los cambios volviendo atrás sin guardarla.

En esta opción, en lugar de mostrar en la barra de navegación _New movie_ deberá indicarse el nombre de la película, y cuando se guarde deberá actualizarse tanto en el listado local como en el servidor.

Para actualizar la tabla necesitarás acceder al `MasterViewController` desde `DetailViewController`. Puedes hacerlo del siguiente modo:

```objectivec
    UINavigationController *parentController = (UINavigationController *)[self.splitViewController.viewControllers objectAtIndex:0];

    MasterViewController *masterController= (MasterViewController *)[parentController.viewControllers objectAtIndex:0];
```

#### Apartados opcionales:

* Usar un formulario para pedir usuario y contraseña la primera vez que se necesite. Se puede usar un `UIAlertController`, y guardar el usuario y la contraseña en `[NSUserDefaults standardUserDefaults]`. En realidad, la información sensible como las contraseñas debe guardarse en el KeyChain, pero esto se verá más adelante en la asignatura de persistencia.

* Añadir opción de búsqueda de películas con servicios REST. Para esto se debe implementar la búsqueda en el servidor mediante querystring. En la app se puede mostrar esta opción en la escena `MasterViewController`, reemplazando el botón _Edit_, ya que en realidad el borrado se puede hacer sin él (mediante un gesto hacia la izquierda en la fila a eliminar).
