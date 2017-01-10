# Parsing y servicios REST en iOS

## Parsing en iOS

En la primera parte de esta sesión veremos cómo parsear la información recibida de un servidor, tanto en JSON como en XML.

### Parsing de JSON

El parsing de JSON no se incorporó al SDK de iOS hasta la versión 5.0. Anteriormente contábamos con diferentes librerías que podíamos incluir para realizar esta tarea, como  (<a href="https://github.com/johnezang/JSONKit">*JSONKit*</a>) o (<a href="https://github.com/stig/json-framework/">*JSON-framework* </a>). Sin embargo, actualmente podemos trabajar con JSON directamente con las clases de Cocoa Touch sin necesidad de incluir ninguna librería adicional.

Para esto simplemente necesitaremos la clase `JSONSerialization`. A partir de ella obtendremos el contenido del JSON en una jerarquía de objetos. El método `jsonObject` de la clase `JSONSerialization` nos devolverá un diccionario o un array según si el elemento principal del JSON es un objeto o una lista, respectivamente.

<!--- https://developer.apple.com/swift/blog/?id=37 -->


<!--let response = try JSONSerialization.jsonObject(with: data, options:JSONSerialization.ReadingOptions(rawValue:0)) as? [String: AnyObject]-->

```swift
let data: Data // Contenido JSON obtenido de la red, por ejemplo
let json = try? JSONSerialization.jsonObject(with: data, options: [])
```

A veces la información JSON está en forma de diccionario (el elemento principal del JSON es un objeto), y otras organizada como un array (el elemento principal es una lista).  Vamos a ver un ejemplo cuando el elemento principal es un objeto:

```json
{
  "someKey": 42.0,
  "anotherKey": {
    "someNestedKey": true
  }
}
```

En este caso, nuestro código podría ser el siguiente:

```swift
if let dictionary = json as? [String: Any] {
  if let number = dictionary["someKey"] as? Double {
    // Procesamos el valor number
  }
  if let nestedDictionary = dictionary["anotherKey"] as? [String: Any] {
    // Accedemos al diccionario anotherKey para hacer algo con sus valores
  }
  for (key, value) in dictionary {
		// Si quisiéramos acceder a todos los pares clave/valor del diccionario raíz
  }
}
```

Si en su lugar el elemento principal del JSON fuera un array:

```json
[
  "hello", 3, true
]
```

Podríamos procesarlo del siguiente modo:

```swift
if let array = json as? [Any] {
	for object in array {
		// Acceder a todos los objetos del array
	}
}
```

El objeto `JSONSerialization` también nos permite realizar la transformación en el sentido inverso, permitiendo transformar una jerarquía de objetos `Array` y `Dictionary` en una representación JSON. Para eso contaremos con el método `jsonObject`:

```swift
var dict : [String : AnyObject] = ["someKey": 42.0, "anotherKey": "prueba"]

do {
        let data = try? JSONSerialization.data(withJSONObject: dict, options: .prettyPrinted)
        if let str = String(data: data!, encoding: .utf8) { // Para imprimirlo por pantalla
            print (str)
        }

} catch {
        print (error.localizedDescription)
}
```


#### Parsing JSON con MVC

Hemos visto la forma básica de serializar o deserializar datos en JSON. Sin embargo, nuestras apps suelen seguir el patrón de diseño MVC, por lo que normalmente es más limpio y conveniente convertir directamente los objetos JSON al formato de nuestro modelo. Vamos a ver un ejemplo de cómo se haría la deserialización. Dado el siguiente modelo:

```swift
class Restaurant
{
  let name: String
	let location: (latitude: Double, longitude: Double)
  let meals: [String]
}
```

Y el siguiente JSON:

```json
{
	"name": "Caffè Macs",
	"coordinates": {
		"lat": 37.330576,
		"lng": -122.029739
	},
	"meals": ["breakfast", "lunch", "dinner"]
}
```

podemos crear un constructor para nuestro modelo a partir de un diccionario:

```swift
init?(json: [String: Any]) {

    guard let name = json["name"] as? String,
        let coordinatesJSON = json["coordinates"] as? [String: Double],
        let latitude = coordinatesJSON["lat"],
        let longitude = coordinatesJSON["lng"],
        let mealsJSON = json["meals"] as? [String]
    else {
            return nil
    }

    self.name = name
    self.location = (latitude, longitude)
    self.meals = mealsJSON
}
```

Y la llamada a nuestro constructor sería:

```swift
let data: Data // Contenido JSON obtenido de la red, por ejemplo
if let jsonData = try? JSONSerialization.jsonObject(with: data, options: []) as! [String:Any] {
    let r = Restaurant(json:jsonData)
}
```

Puedes encontrar más ejemplos de cómo trabajar con JSON en <a href="https://developer.apple.com/swift/blog/?id=37">este enlace de Apple</a>.

### Parsing de XML

Para analizar XML en iOS contamos con `XMLParser` en el SDK de iOS. Con esta librería el análisis se realiza de forma parecida a los parsers SAX de Java. Este es el parser principal incluido en el SDK, aunque también contamos dentro del SDK con libxml2, escrito en C, que incluye tanto un parser SAX como DOM. Además encontramos otras librerías que podemos incluir en nuestro proyecto como parsers DOM XML:

<table>
		<tr><th>Parser</th><th>URL</th><th></th><th></th></tr>
		<tr><td>TBXML</td><td colspan="3"><a href="http://www.tbxml.co.uk/">http://www.tbxml.co.uk/</a></td></tr>
		<tr><td>TouchXML</td><td colspan="3"><a href="https://github.com/TouchCode/TouchXML">https://github.com/TouchCode/TouchXML</a></td></tr>
		<tr><td>KissXML</td><td colspan="3"><a href="http://code.google.com/p/kissxml">http://code.google.com/p/kissxml</a></td></tr>
		<tr><td>TinyXML</td><td colspan="3"><a href="http://www.grinninglizard.com/tinyxml/">http://www.grinninglizard.com/tinyxml/</a></td></tr>
		<tr><td>GDataXML</td><td colspan="3"><a href="http://code.google.com/p/gdata-objectivec-client">http://code.google.com/p/gdata-objectivec-client</a></td></tr>
</table>

Nos vamos a centrar en el estudio de `XMLParser`, por ser el parser principal incluido en la API de Cocoa Touch.

Para implementar un parser con esta librería deberemos crear una clase que adopte el protocolo `XMLParserDelegate`, el cual define, entre otros, los siguientes métodos:

```swift
func parser(_: XMLParser,
    didStartElement: String,
       namespaceURI: String?,
      qualifiedName: String?,
         attributes: [String : String] = [:])

func parser(_: XMLParser,
     didEndElement: String,
      namespaceURI: String?,
     qualifiedName: String?)

func parser(_:XMLParser,
   foundCharacters: String)
```

Podemos observar que nos informa de tres tipos de eventos: `didStartElement`, `didEndElement` y `foundCharacters`. El análisis del XML será secuencial, es decir, el parser irá leyendo el documento y nos irá notificando los elementos que encuentre. Cuando se abra una etiqueta, llamará al método `didStartElement` de nuestro parser, cuando encuentre texto llamará a `foundCharacters`, y cuando se cierra la etiqueta llamará a `didEndElement`. Será responsabilidad nuestra implementar de forma correcta estos tres eventos, y guardar la información de estado que necesitemos durante el análisis.

Por ejemplo, imaginemos un documento XML sencillo como el siguiente:

```xml
<![CDATA[<mensajes>
    <mensaje usuario="pepe">Hola, ¿qué tal?</mensaje>
    <mensaje usuario="ana">Fetén</mensaje>
</mensajes>]]>
```

Podemos analizarlo mediante un parser `XMLParser` como el siguiente:

```swift

// Modelo:
class UAMensaje
{
    var usuario : String?
    var texto: String?
}

// Código en nuestro controlador:

    var listaMensajes = [Any]()
    var currentMessage : UAMensaje?

    func parserDidStartDocument(_ parser: XMLParser) { // Se invoca al comenzar el parsing
    }

    func parserDidEndDocument(_ parser: XMLParser) { // Se invoca cuando hemos terminado el parsing
    }

    func parser(_ parser: XMLParser,
                didStartElement elementName: String,
                namespaceURI: String?,
                qualifiedName: String?,
                attributes attributeDict: [String : String] = [:])
    {
        if elementName.lowercased() == "messages" {
            // Ok, no hacer nada
        }
        else if elementName.lowercased() == "message" {

            self.currentMessage = UAMensaje()
            self.currentMessage!.usuario = attributeDict["usuario"]
        }
        else { // Si no puede haber etiquetas distintas a message o menssages
                parser.abortParsing()
        }
    }

    func parser(_: XMLParser,
                didEndElement elementName: String,
                namespaceURI: String?,
                qualifiedName: String?)
    {
        if elementName.lowercased() == "message" {
            if let message = self.currentMessage {
                self.listaMensajes.append(message)
            }
        }
    }

    func parser(_:XMLParser,
                foundCharacters characters: String)
    {
        // Quitamos espacios en blanco
        let trimmedString = characters.trimmingCharacters(in: .whitespacesAndNewlines)

        if trimmedString != "" {
            self.currentMessage?.texto = trimmedString
        }
    }
```

Podemos observar que cada vez que encuentra una etiqueta de apertura, podemos obtener tanto la etiqueta como sus atributos. Cada vez que se abre un nuevo mensaje, se van introduciendo en el objeto de tipo `UAMensaje` los datos que se encuentran en el XML, hasta encontrar la etiqueta de cierre (en nuestro caso el texto, aunque podríamos tener etiquetas anidadas).

Para que se ejecute el _parser_ que hemos implementado mediante el delegado, deberemos crear un objeto `XMLParser` y proporcionarle dicho delegado (en el siguiente ejemplo suponemos que nuestro objeto `self` hace de delegado). El parser se debe inicializar proporcionando el contenido XML a analizar (encapsulado en un objeto `Data`):

```swift
let parser = XMLParser(data:self.content)
parser.delegate = self
let result = parser.parse()
```

Tras inicializar el _parser_, lo ejecutamos llamando el método `parse`, que realizará el análisis de forma síncrona, y nos devolverá `true` si todo ha ido bien, o `false` si ha habido algún error al procesar la información (se producirá un error si en el delegado durante el _parsing_ llamamos a `parser.abortParsing`).


## Acceso a servicios REST desde iOS

En iOS podemos acceder a servicios REST utilizando las clases para conectar con URLs vistas en anteriores sesiones. Por ejemplo, para hacer una consulta al servidor de OpenWeatherMap podríamos utilizar un código como el siguiente para iniciar la conexión (recordemos que este método de conexión es asíncrono:

```swift
  let url = URL(string: "http://api.openweathermap.org/data/2.5/weather?q=Alicante")!
  let request = URLRequest(url:url)     
  let session = URLSession(configuration:URLSessionConfiguration.default)

  session.dataTask(with: request, completionHandler: { data, response, error in
           // Se recibe la respuesta como se ha visto en el capítulo de red
       }).resume() // En esta línea lanzamos la petición asíncrona
```


Podemos modificar los datos de la petición y de esta forma establecer todos los datos necesarios para la petición al servicio: método HTTP, mensaje a enviar (como XML o JSON), y cabeceras (para indicar el tipo de contenido enviado, o los tipos de representaciones que aceptamos). Por ejemplo:

```swift
  let url = URL(string: "http://localhost/videoclub/api/v1/catalog")!
  let datosPelicula = ... // Componer mensaje JSON con datos de la peli a crear
  var request = URLRequest(url:url)

  request.httpMethod = "POST"
  request.httpBody = datosPelicula
  request.setValue("application/json", forHTTPHeaderField: "Accept")
  request.setValue("application/json", forHTTPHeaderField: "Content-Type")  
```

Podemos ver que en la petición POST hemos establecido todos los datos necesarios. Por un lado su bloque de contenido, con los datos del recurso que queremos añadir en la representación que consideremos adecuada. En este caso suponemos que utilizamos XML como representación. En tal caso hay que avisar de que el contenido lo enviamos con este formato, mediante la cabecera `Content-Type`, y de que la respuesta también queremos obtenerla en XML, mediante la cabecera `Accept`.

## Seguridad HTTP

Vamos a ver en primer lugar cómo acceder a servicios protegidos con seguridad HTTP estándar. En estos casos, deberemos proporcionar en la llamada al servicio las cabeceras de autenticación al servidor, con las credenciales que nos den acceso a las operaciones solicitadas.

Para quienes no estén muy familiarizados con la seguridad en HTTP, conviene mencionar el funcionamiento del protocolo a grandes rasgos. Cuando realizamos una petición HTTP a un recurso protegido con seguridad básica, HTTP nos devuelve una respuesta indicándonos que necesitamos autentificarnos para acceder. Es entonces cuando el cliente solicita al usuario las credenciales (usuario y password), y entonces se realiza una nueva petición con dichas credenciales incluidas en una cabecera _Authorization_. Si las credenciales son válidas, el servidor nos dará acceso al contenido solicitado.

<!---
<p>Este es el funcionamiento habitual de la autentificación. En el caso del acceso mediante HttpClient que hemos visto anteriormente, el funcionamiento es el mismo, cuando el servidor nos pida autentificarnos
la librerÌa lanzar· una nueva peticiÛn con las credenciales especificadas en el proveedor de credenciales.</p>
--->


Sin embargo, si sabemos de antemano que un recurso va a necesitar autenticación, podemos tambiçen autentificarnos de forma preventiva. La autenticación preventiva consiste en mandar las credenciales en la primera petición, antes de que el servidor nos las solicite. Con esto ahorramos una petición, pero podríamos estar mandando las credenciales en casos en los que no resulta necesario.


<!---
Con HttpClient podemos activar o desactivar la autentificaciÛn preventiva con el siguiente mÈtodo:

<source lang="java">client.getParams().setAuthenticationPreemptive(true);</source>
--->

### Autenticación no preventiva

En iOS, la autenticación la puede hacer el delegado de la conexión. Cuando enviamos una petición sin credenciales, el sevidor nos responderá que necesita autenticación invocando al método delegado `didReceive challenge` del protocolo `URLSessionDelegate`. En este método, que puede ser a nivel de sesión o de tarea, podremos especificar los datos de la acreditación.

* A nivel de sesión, se invoca ``URLSession:didReceive challenge:completionHandler`` cuando el servidor requiere autenticación. Este método debe usarse para servidores con SSL/TLS, o cuando todas las peticiones que hagamos para una sesión necesiten la misma acreditación.

* A nivel de petición, podemos usar el método ``URLSession:task:didReceive challenge:completionHandler:`` si el servidor requiere autenticación para una tarea en concreto. Esto es necesario si las peticiones de una misma sesión requieren acreditaciones distintas.

Para usar estos métodos con autenticación Basic debemos crear un objeto `URLCredential` a partir de nuestras credenciales (usuario y password). A continuación vemos un ejemplo típico de implementación de una autenticación a nivel de sesión:


```swift
func urlSession(
    _ session: URLSession,
    didReceive challenge: URLAuthenticationChallenge,
    completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void)
{
    print("task-didReceiveChallenge")

    if challenge.previousFailureCount > 0 {
        print("Error: Please check the credential")
        completionHandler(.cancelAuthenticationChallenge, nil)
    } else {
        let credential = URLCredential(user:"mi_login", password:"mi_password", persistence: .forSession)
        completionHandler(.useCredential, credential)
    }
}
```

Podemos observar que comprobamos los fallos previos de autenticación que hemos tenido. Es decir, si con los credenciales que tenemos en el código ha fallado la autenticación, será mejor que cancelemos el acceso, ya que si volvemos a intentar acceder con los mismos credenciales vamos a tener el mismo error. En caso de que sea el primer intento, creamos los credenciales (podemos ver que se puede indicar que se guarden de forma persistente para los próximos accesos), y los utilizamos para responder al reto de autenticación (`URLAuthenticationChallenge`).

<!---
completionHandler(.useCredential, URLCredential(trust: challenge.protectionSpace.serverTrust)) // TLS?
-->

A nivel de tarea sería exactamente igual, pero usando el siguiente prototipo, esta vez del protocolo `URLSessionTaskDelegate`:

```swift
func urlSession(
  _ session: URLSession,
    task: URLSessionTask,
    didReceive challenge: URLAuthenticationChallenge,
    completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void)
```

Si es necesaria la autenticación y no implementamos el método a nivel de sesión, entonces iOS llama automáticamente al método a nivel de tarea.

El tipo de persistencia (``URLCredential.Persistence``) puede ser:
* ``none``: Las credenciales no se guardan
* ``forSession``: Las credenciales se guardan para esta sesión
* ``permanent``: Las credenciales se guardan en el _keychain_
* ``synchronizable``: Las credenciales se guardan en el _keychain_, y además se comparten entre dispositivos iOS.

### Autenticación preventiva

Este tipo de autenticación se suele usar para servicios REST, y consiste en enviar las credenciales antes de que las pida el servidor. De este modo nos ahorramos un mensaje de petición y de respuesta, por lo que es más eficiente y ahorramos ancho de banda. Este esquema es recomendable cuando sabemos de antemano que nuestra petición va a necesitar autenticación.

Al igual que en el caso anterior, también podemos hacer la autenticación a nivel de sesión o de tarea. En cualquier caso, deberemos codificar en Base64 la cadena para autenticación. En el caso de autenticación Basic, dados un usuario y una contraseña podemos generar la cadena del siguiente modo:

```swift
let login = "my_login"
let password = "mi_password"
let authStr = login + ":" + password
if let authData = authStr.data(using: .utf8) {
    let authValue = "Basic \(authData.base64EncodedString(options: []))"
    // Ya podemos usar authValue
}
```

En el código podemos ver que primero se genera la cadena de credenciales, concatenando `login:password`. A continuación esta cadena se codifica en base64, y con esto ya podemos añadir una cabecera `Authorization` cuyo valor es la cadena `Basic <credenciales_base64>`. Una vez tengamos la cadena de las credenciales, si queremos añadir la autenticación preventiva a la sesión podemos especificarla en la configuración:

```swift
let config = URLSessionConfiguration.default
config.httpAdditionalHeaders = ["Accept" : "application/json",
                                "Authorization" : authValue]
let session = URLSession(configuration: config)
```

Si en lugar de hacerlo a nivel de sesión lo que queremos es añadir esta autenticación a una petición, tenemos que modificar su request:

```swift
var request = URLRequest(url: URL(string:"http://www.ua.es")!)
request.addValue(authValue, forHTTPHeaderField:"Authorization")
```

#### Autenticación TLS

Si necesitas conectarte a un servidor con seguridad TLS o hacer peticiones más complejas que las vistas anteriormente, actualmente la mejor opción es usar el framework <a href="https://github.com/Alamofire/Alamofire">AlamoFire</a>, que simplifica mucho las operaciones de conexión en iOS. Para los siguientes ejercicios no se permite usar este framework, por motivos pedagógicos deben usarse los métodos nativos que hemos visto anteriormente.

# Ejercicios de servicios REST en iOS

## Weather app (2 puntos)

En este primer ejercicio vamos a practicar el parsing de XML y JSON. Para ello haremos una aplicación que nos permita visualizar el tiempo de una ciudad accediendo a la API de <a href="http://openweathermap.org/api">Openweathermap</a>.

Se proporciona una plantilla `Weather` que ya realiza la llamada asíncrona a la API. Según el usuario elija XML o JSON, la respuesta del servidor se recibirá en el formato correspondiente. Crea una nueva conexión y lánzala en el método `search`.

Se pide parsear la respuesta en ambos formatos para poder mostrar la información en pantalla. Sólo se solicitan unos pocos datos, que son la temperatura actual, la humedad, velocidad del viento, el país, y la descripción (que es un mensaje, como por ejemplo _clear skies_).

Hay que completar los métodos `parseXML` y `parseJSON` para mostrar la información correspondiente en los outlets del interfaz. Es recomendable comenzar con `parseJSON`, completando el método `init` de la clase `Weather`. Para parsear el XML hace falta añadir al final del `ViewController` los métodos `didStartElement`, `didEndElement` y `foundCharacters`.

Cuando hayas terminado estos métodos, descarga la imagen del icono que se encuentra en el campo `icon` de la respuesta para mostrarlo en el `UIImageView`, dentro del método `updateView`. Ejemplo de la URL correspondiente al icono `10d`: http://openweathermap.org/img/w/10d.png.

## Proyecto de iOS (7 puntos)

El proyecto consistirá en hacer una aplicación para iOS que gestione el videoclub que se ha implementado en sesiones anteriores. Partiremos de la aplicación `Pelis` implementada en el ejercicio de la sesión anterior.

#### Listado

Vamos a cambiar en el `MasterViewController` la carga de películas, de manera que esta vez se haga una petición GET al servidor. Puedes usar localhost si ya lo tienes implementado, o gbrain si no es así.

Para empezar, añade al proyecto la clase `Connection` que hemos implementado en el ejercicio anterior.

Prepara `MasterViewController` para usar esta clase, añadiendo el protocolo `ConnectionDelegate` e implementando los métodos siguientes:

```swift
func connectionSucceed(_ connection: Connection, with data: Data)
func connectionFailed(_ connection: Connection, with error: String)
```

Implementa el código para hacer la petición del listado de películas desde el método ``createPelis``. Recuerda que es un método `GET`, indícalo en el `HTTPMethod`. Si tras recibir los datos no se muestra nada en la tabla, probablemente debas añadir la siguiente instrucción:

```swift
self.tableView.reloadData()
```

Los datos JSON de las películas están almacenados en un array. Por cada elemento del array crea una nueva película mediante un constructor de la clase `Pelicula` que reciba un diccionario con el contenido de la posición correspondiente de dicho array. Añade el campo `identif` (de tipo _Int?_) a la clase `Peli` y guárdalo cuando leas del JSON el valor del `id`. Esto te hará falta para implementar el resto de opciones.

#### Alquilar / devolver

En la vista `DetailViewController` implementa la opción de alquiler mostrando un botón (en cualquier parte visible) que indique el estado actual con el texto _Alquilada_ o _Libre_, y que pueda pulsarse para cambiar su estado.

Si la conexión ha sido válida y se ha alquilado o devuelto la película, se debe actualizar su campo `rented` y el título del botón en el interfaz.

Esta petición requiere autenticación Basic. Puedes hacerla de tipo preventivo y a nivel de tarea, añadiendo un método adicional a la clase `Connection` que acepte usuario y contraseña para hacer la petición:

```swift
func startConnection(_ session:URLSession, with request:URLRequest, login:String, password:String)
```

Recuerda que si usas gbrain esto sólo funcionará para las películas del final del listado. En las del principio (las añadidas por los profesores inicialmente) fallará la autorización por falta de permisos.

#### Añadir

Vamos a implementar la opción para añadir una película. Para esto, puedes descomentar el código de `addButton` en `viewDidLoad`.

En el método `insertNewObject` tendremos que mostrar una vista  para pedir los datos de la película a añadir. Para ello se proporciona como material la clase `PelisTableViewController`. El método quedaría así:

```swift
func insertNewObject(_ sender: Any) {
    let pelisAdd = PelisTableViewController()
    pelisAdd.delegate = self
    self.navigationController?.pushViewController(pelisAdd, animated: true)
}
```

Hay que crear un nuevo método `init` en la clase `Pelicula` para inicializar los datos de una película vacía. Las nuevas películas estarán libres por defecto.

En `PelisTableViewController` debes implementar el método `save` que se llama cuando el usuario pulsa el botón para guardar la película. También debes modificar `connectionSucceed` para actualizar el identificador de la película que se ha recibido, antes de devolverla al delegado mediante el método `saved`.

Dicho método `saved` hay que implementarlo en `MasterViewController` para actualizar el array local de películas sin tener que volver a hacer una llamada al servidor.

#### Eliminar

Descomenta el código de edición de las tablas en `MasterViewController`, tanto en el método `viewDidLoad` como `commit editingStyle`, y devolviendo `true` en `canEditRowAtIndexPath`.

Haz los cambios correspondientes para que cuando se elimine una película en la tabla también se haga en el servidor, de modo que si falla en el servidor no se llegue a borrar en la tabla.

Al tener dos tipos de conexiones en el mismo controlador, en el método `connectionSucceed` necesitarás identificar cuál de ellas es la que ha hecho la petición. Para ello, lo más sencillo es crear una nueva variable en la clase `Connection` para almacenar el nombre de la conexión:

```swift
var name : String?
```

Añadimos un nuevo método init en `Connection`:

```swift
init(name:String, delegate:ConnectionDelegate) {
    self.delegate = delegate
    self.name = name
}
```

Ahora, podemos crear una conexión para borrar, por ejemplo:

```swift
let connectionDelete = Connection(name : "delete", delegate : self)
```

Y podemos saber desde qué conexión se ha recibido la respuesta en el método `connectionSucceed`:

```swift
func connectionSucceed(_ connection: Connection, with data: Data) {

       if connection.name == "delete" {
          // Gestionar borrado
       }
       else ...
}
```

#### Editar

Añade un botón `Edit` en `DetailViewController` a la derecha de la barra de navegación para editar una película. Cuando el usuario lo pulse, deberá poder modificar los datos de la película usando el mismo controlador que para añadirla (`PelisTableViewController`). Ojo, tendrás que hacer una copia de la película a modificar por si el usuario cancela los cambios volviendo atrás sin guardarla.

En esta opción, en lugar de mostrar en la barra de navegación _Add movie_ deberá indicarse el nombre de la película, y cuando se guarde deberá actualizarse tanto en el listado local como en el servidor.

Para actualizar la tabla necesitarás acceder al `MasterViewController` desde `DetailViewController`. Puedes hacerlo del siguiente modo:

```swift
  let parentController =  self.splitViewController!.viewControllers[0] as! UINavigationController
  let masterController = parentController.viewControllers[0] as! MasterViewController
  masterController.tableView.reloadData()
```

#### Apartados opcionales:

* Usar un formulario para pedir usuario y contraseña la primera vez que se necesite. Se puede usar un `UIAlertController`, y guardar el usuario y la contraseña en `NSUserDefaults.standardUserDefaults()`. En realidad, la información sensible como las contraseñas debe guardarse en el _KeyChain_, pero esto se verá más adelante en la asignatura de persistencia.

* Añadir opción de búsqueda de películas con servicios REST. Para esto se debe implementar la búsqueda en el servidor mediante querystring. En la app se puede mostrar esta opción en la escena `MasterViewController`, reemplazando el botón _Edit_, ya que en realidad el borrado se puede hacer sin él (mediante un gesto hacia la izquierda en la fila a eliminar).
