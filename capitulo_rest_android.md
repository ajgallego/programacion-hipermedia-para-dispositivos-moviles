<!-- *********************************************************************** -->
# Parsing y servicios REST en Android

Como ya hemos visto antes, en Android para descargar el contenido desde una URL podemos utilizar la librería _Http Client_. Dicha librería ofrece una mayor facilidad para controlar y utilizar objetos de conexión HTTP, nos permitirá realizar peticiones tipo GET, POST, PUT, DELETE y además acceder a las cabeceras.

Por ejemplo para realizar una petición tipo GET a una URI y obtener su contenido tendríamos que hacer:

```java
HttpClient client = new DefaultHttpClient();
HttpGet request = new HttpGet("http://<domain>/resource");
try {
    ResponseHandler<String> handler = new BasicResponseHandler();
    String contenido = client.execute(request, handler);

    tvResponse.setText(contenido);

} catch (ClientProtocolException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    client.getConnectionManager().shutdown();
}
```


Observamos que primero instanciamos el cliente HTTP y procedemos a crear un objeto que representa el método HTTP GET. Con el cliente y el método instanciado, necesitamos ejecutar la petición con el método `execute`. Si queremos tener un mayor control sobre la respuesta, en lugar de utilizar un `BasicResponseHandler` podríamos ejecutar directamente la petición sobre el cliente (`HttpResponse response = client.execute(request);`), y obtener así la respuesta completa, tal como vimos en la sesión anterior.

A partir del objeto `HttpResponse` podemos tener acceso a los **códigos de estado** que se envían en la cabecera, por ejemplo:


```java
HttpResponse response = client.execute(request);
StatusLine statusLine = response.getStatusLine();
int statusCode = statusLine.getStatusCode();

if (statusCode == 200 )
    // ...

// O también:
if (statusCode == HttpStatus.SC_OK)
    // ...
```

En `HttpStatus` están definidos como constantes todos los códigos de estado con los cuales podemos comparar. Para más información podéis consultar: http://developer.android.com/reference/org/apache/http/HttpStatus.html


Para obtener el resto de **cabeceras** de la respuesta del servidor usaremos también el objeto `HttpResponse`, de la forma:

```java
HttpResponse response = client.execute(request);

// Obtener todas las cabeceras
Header[] headers = response.getAllHeaders();
for (Header header : headers) {
	Log.d("Headers", "Key : " + header.getName()
         	       + " ,Value : " + header.getValue());
}

// Obtener una cabecera por su clave
String server = response.getFirstHeader("Server").getValue();
```


Para obtener el contenido de la respuesta a partir del objeto `HttpResponse` la forma más sencilla es utilizar `EntityUtils` (dado que sino tendríamos que crear un bucle para leer linea a línea del _stream_ de entrada ):

```java
String contenido = EntityUtils.toString( response.getEntity() );
```


Hemos visto cómo realizar una petición GET, tal como se vio en sesiones anteriores, para acceder a servicios REST. Sin embargo, para determinadas operaciones REST utiliza métodos HTTP distintos, como POST, PUT o DELETE. Para cambiar el método simplemente tendremos que cambiar el objeto `HttpGet` por el del método que corresponda (`HttpPost`, `HttpPut` o `HttpDelete`). Cada tipo incorpora los métodos necesarios para el tipo de petición HTTP que realice. Por ejemplo, a continuación vemos un ejemplo de petición POST. Creamos un objeto `HttpPost` al que le deberemos pasar una entidad que represente el bloque de contenido a enviar (en una petición POST ya no sólo tenemos un bloque de contenido en la respuesta, sino que también lo tenemos en la petición). Podemos crear diferentes tipos de entidades, que serán clases que hereden de `HttpEntity`. La más habitual para los servicios que estamos utilizando será `StringEntity`, que nos facilitará incluir en la petición contenido XML o JSON como una cadena de texto. Además, deberemos especificar el tipo MIME de la entidad de la petición mediante `setContentType` (en el siguiente ejemplo consideramos que es XML). Por otro lado, también debemos especificar el tipo de representación que queremos obtener como respuesta, y como hemos visto anteriormente, esto debe hacerse mediante la cabecera `Accept`. Esta cabecera la deberemos establecer en el objeto que representa la petición POST (`HttpPost`).

```java
HttpClient client = new DefaultHttpClient();

HttpPost post = new HttpPost("http://<dominio>/peliculas");

StringEntity se = new StringEntity(" [contenido xml] ");
se.setContentType("application/xml");    // O "application/json"

post.setEntity(se);
post.setHeader("Accept", "application/xml");
post.setHeader("Content-type", "application/xml");

HttpResponse response = client.execute(post);
StatusLine statusLine = response.getStatusLine();

if (statusLine.getStatusCode() == 200 )
    String contenido = EntityUtils.toString( response.getEntity() );
else
    // error...
```


Como podemos ver en el ejemplo anterior, una vez configurada la petición POST la forma de ejecutar la petición es la misma que la vista anteriormente para peticiones GET. Para el resto de métodos HTTP el funcionamiento será similar, simplemente cambiando el tipo del objeto de la petición por el que corresponda (por ejemplo `HttpPut` o `HttpDelete`). Además, en las peticiones tipo POST, PUT y DELETE es importante comprobar el código que se devuelve en la cabecera, para asegurarnos de que la operación se ha realizado correctamente.


> Nota: todas las conexión tienen que ir dentro de un bloque `try...catch` para capturar las excepciones y además cerrar la conexión al final. Por brevedad en algunos ejemplos de los apuntes no se incluye el cógido completo.





<!-- *********************************************************************** -->
# Parsing de estructuras XML

En las comunicaciones por red es muy común transmitir información en formato XML, el ejemplo más conocido depués del HTML, son las noticias RSS. En este último caso, al delimitar cada campo de la noticia por tags de XML se permite a los diferentes clientes lectores de RSS obtener sólo aquellos campos que les interese mostrar.

Android nos ofrece dos maneras de trocear o "parsear" XML. El `SAXParser` y el `XmlPullParser`:
* El parser SAX requiere la implementación de manejadores que reaccionan a eventos tales como encontrar la apertura o cierre de una etiqueta, o encontrar atributos.
* Por el contrario `XmlPullParser` necesita menos implementación ya que consiste en iterar sobre el árbol de XML (sin tenerlo completo en memoria) conforme el código lo va requiriendo, indicándole al parser que tome la siguiente etiqueta (método `next()`) o texto (método `nextText()`).

A continuación mostramos un ejemplo sencillo de uso del `XmlPullParser`. Préstese atención a las sentencias y constantes resaltadas, para observar cómo se identifican los distintos tipos de etiqueta, y si son de apertura o cierre. También se puede ver cómo encontrar atributos y cómo obtener su valor.

```java
try {
  URL text = new URL("http://www.ua.es");

  XmlPullParserFactory parserCreator = XmlPullParserFactory.newInstance();
  XmlPullParser parser = parserCreator.newPullParser();
  parser.setInput(text.openStream(), null);
  int parserEvent = parser.getEventType();

  while (parserEvent != XmlPullParser.END_DOCUMENT)
  {
    switch (parserEvent) {
        case XmlPullParser.START_DOCUMENT:
          break;
        case XmlPullParser.END_DOCUMENT:
          break;
        case XmlPullParser.START_TAG:
          String tag = parser.getName();
          if (tag.equalsIgnoreCase("title"))
          {
            Log.i("XML","El titulo es: "+ parser.nextText());
          }
          else if(tag.equalsIgnoreCase("meta"))
          {
            String name = parser.getAttributeValue(null, "name");
            if(name.equalsIgnoreCase("description")) {
              Log.i("XML","La descripción es:" +
                          parser.getAttributeValue(null,"content"));
            }
          }
          break;
        case XmlPullParser.END_TAG:
          break;
    }

    parserEvent = parser.next();
  }
} catch (Exception e) {
  Log.e("Net", "Error en la conexion de red", e);
}
```

> Nota: el ejemplo anterior, igual que todas las operaciones que requieran acceso a Internet, tendrá que estar en un hilo.


El ejemplo anterior serviría para imprimir en el LogCat el título del siguiente fragmento de página web, que en este caso sería "Universidad de Alicante", y para encontrar el `meta` cuyo atributo `name` sea "description" y mostrar el valor de su atributo `content`:


```html
<html xmlns="http://www.w3.org/1999/xhtml" lang="es" xml:lang="es">
<head>
<title>Universidad de Alicante*</title>
<meta name="Description" content="Informacion Universidad Alicante.
  Estudios, masteres, diplomaturas, ingenierias, facultades, escuelas"/>
<meta http-equiv="pragma" content="no-cache" />
<meta name="Author" content="Universidad de Alicante" />
<meta name="Copyright" content="&copy; Universidad de Alicante" />
<meta name="robots" content="index, follow" />
```



<!-- *********************************************************************** -->
# Parsing de estructuras JSON

JSON es una representación muy utilizada para formatear los recursos solicitados a un servicio web RESTful. El formato sigue la misma notación de objetos de JavaScript, por lo tanto ocupa muy poco, es texto plano y puede ser manipulado muy fácilmente utilizando JavaScript, PHP u otros lenguajes.

La gramática de los objetos JSON es simple y requiere la agrupación de la definición de los datos y valores de los mismos. En primer lugar, los elementos están contenidos dentro de llaves `{` y `}` y separados por comas; los valores de los diferentes elementos se organizan en pares con la estructura `"nombre":"valor"`; y finalmente, las secuencias de elementos (o arrays) están contenidas entre corchetes `[` y `]`. Y esto es todo! Para una descripción detallada de la gramática, podéis consultar <a href="http://www.json.org/fatfree.html"> http://www.json.org/fatfree.html</a>

Con la definición de agrupaciones anterior, podemos combinar múltiples conjuntos para crear cualquier tipo de estructura requerido. El siguiente ejemplo muestra una descipción JSON de un objeto con información sobre una lista de mensajes:

```json
[
    {"texto":"Hola, ¿qué tal?", "usuario":"Pepe" },
    {"texto":"Fetén", "usuario":"Ana" }
]
```

Antes de visualizar cualquiera de los valores de una respuesta JSON, necesitamos convertirla en otro tipo de estructura para poder procesarla. Dentro de la API de Android encontramos una serie de clases que nos permiten analizar y componer mensajes JSON. Las dos clases fundamentales son `JSONArray` y `JSONObject`. La primera de ellas representa una lista de elementos, mientras que la segunda representa un objeto con una serie de propiedades. Podemos combinar estos dos tipos de objetos para crear cualquier estructura JSON. Cuando en el JSON encontremos una lista de elementos (`[ ... ]`) se representará mediante `JSONArray`, mientras que cuando encontremos un conjunto de propiedades _clave-valor_ encerrado entre llaves (`{ ... }`) se representará con `JSONObject`. Por ejemplo, para procesar el JSON del ejemplo anterior podríamos utilizar el siguiente código:

```java
JSONArray mensajes = new JSONArray(contenido);

for(int i=0;i<mensajes.length();i++) {
    JSONObject mensaje = mensajes.getJSONObject(i);

    String texto = mensaje.getString("texto");
    String usuario = mensaje.getString("usuario");

    //...
}
```



El objeto `JSONArray` nos permite conocer el número de elementos que contiene (`length`), y obtenerlos a partir de su índice, con una serie de métodos `get-`. Los elementos pueden ser de tipos básicos (`boolean`, `double`, `int`, `long`, `String`), o bien ser objetos o listas de objetos (`JSONObject`, `JSONArray`).

Los objetos (`JSONObject`) tienen una serie de campos, a los que también se accede mediante una serie de métodos `get-` que pueden ser de los mismos tipos que en el caso de las listas. En el ejemplo anterior son cadenas (_texto_, y _usuario_), pero podrían ser listas u otros objetos anidados.

Esta librería no sólo nos permite analizar JSON, sino que también podemos componer mensajes con este formato. Los objetos `JSONObject` y `JSONArray` tienen para cada método `get-`, un método `put-` asociado que nos permite añadir campos o elementos. Una vez añadida la información necesaria, podemos obtener el texto JSON mediante el método `toString()` de los objetos anteriores.

Además es importante que capturemos las excepciones al procesar cadenas en JSON ya que en caso de intentar obtener un tipo de elemento que no estuviera presente se lanzaría una excepción del tipo `JSONException`.

Esta librería es sencilla y fácil de utilizar, pero puede generar demasiado código para parsear estructuras de complejidad media. Existen otras librerías que podemos utilizar como *GSON* (<a href="http://sites.google.com/site/gson/gson-user-guide">`http://sites.google.com/site/gson/gson-user-guide`</a>) o *Jackson* (<a href="http://wiki.fasterxml.com/JacksonInFiveMinutes">`http://wiki.fasterxml.com/JacksonInFiveMinutes`</a>) que nos facilitarán notablemente el trabajo, ya que nos permiten mapear el JSON directamente con nuestros objetos Java, con lo que podremos acceder al contenido JSON de forma similar a como se hace en Javascript.





<!-- *********************************************************************** -->
# Autentificación en servicios remotos

Los servicios REST estan fuertemente vinculados al protocolo HTTP, por lo que los mecanismos de seguridad
utilizados también deberían ser los que define dicho protocolo. Pueden utilizar los diferentes tipos de
autentificación definidos en HTTP: _Basic_, _Digest_ y _X.509_. Sin embargo, cuando se
trata de servicios que se dejan disponibles para que cualquier desarrollador externo pueda acceder a ellos, utilizar
directamente estos mecanismos básicos de seguridad puede resultar peligroso. En estos casos la autentificación suele
realizarse mediante el protocolo OAuth. Este último se sale de los contenidos del curso, en la siguiente sección
nos centraremos en la seguridad HTTP básica.



<!-- ************************************* -->
## Seguridad HTTP básica

Para acceder a servicios protegidos con seguridad HTTP estándar deberemos
proporcionar en la llamada al servicio las cabeceras de autentificación con los credenciales que
nos den acceso a las operaciones solicitadas.

Por ejemplo, desde un cliente Android en el que utilicemos la API de red estándar de Java SE deberemos definir
un `Authenticator` que proporcione estos datos:

```java
Authenticator.setDefault(new Authenticator() {
    protected PasswordAuthentication getPasswordAuthentication() {
        return new PasswordAuthentication (
              "usuario", "password".toCharArray());
    }
});
```

En caso de que utilicemos HttpClient de Apache simplemente tendremos que añadir la siguiente cabecera:

```java
HttpClient client = new DefaultHttpClient();
HttpPost post = new HttpPost( url );
post.addHeader(BasicScheme.authenticate(
        new UsernamePasswordCredentials("usuario", "password"), "UTF-8", false));
```

Otra posible opción es usando el método `setCredentials` de la clase `DefaultHttpClient`: 

```java
DefaultHttpClient client = new DefaultHttpClient();
client.getCredentialsProvider().setCredentials(
    new AuthScope("jtech.ua.es", 80),
    new UsernamePasswordCredentials("usuario", "password")
);
```

Aquí además de las credenciales, hay que indicar el ámbito al que se aplican (host y puerto).

Para quienes no estén muy familiarizados con la seguridad en HTTP, conviene mencionar el funcionamiento
del protocolo a grandes rasgos. Cuando realizamos una petición HTTP a un recurso protegido con seguridad
básica, HTTP nos devuelve una respuesta indicándonos que necesitamos autentificarnos para acceder. Es
entonces cuando el cliente solicita al usuario las credenciales (usuario y password), y entonces se
realiza una nueva petición con dichas credenciales incluidas en una cabecera _Authorization_.
Si las credenciales son válidas, el servidor nos dará acceso al contenido solicitado.

Este es el funcionamiento habitual de la autentificación. En el caso del acceso mediante HttpClient
que hemos visto anteriormente, el funcionamiento es el mismo, cuando el servidor nos pida autentificarnos
la librería lanzará una nueva petición con las credenciales especificadas en el proveedor de credenciales.

Sin embargo, si sabemos de antemano que un recurso va a necesitar autentificación, podemos también autentificarnos
de forma preventiva. La autentificación preventiva consiste en mandar las credenciales en la primera petición,
antes de que el servidor nos las solicite. Con esto ahorramos una petición, pero podríamos estar mandando
las credenciales en casos en los que no resulta necesario.

Con HttpClient podemos activar o desactivar la autentificación preventiva con el siguiente método:

```java
client.getParams().setAuthenticationPreemptive(true);
```









<!-- *********************************************************************** -->
<!-- *********************************************************************** -->
<!-- *********************************************************************** -->
<!-- *********************************************************************** -->


<!-- *********************************************************************** -->
# Ejercicios


## Ejercicio 1 - Cliente Android mediante servicios REST del videoclub (3 puntos)

En este ejercicio se pide realizar una aplicación Android que actue de cliente de los servicios públicos tipo REST del videoclub. Para esto tenéis que crear una nueva aplicación, llamada `Videoclub`, que va a tener solamente dos actividades: un listado con las películas y la vista detalle de una película (la apariencia de estas dos pantallas se deja a vuestra elección). En las plantillas se facilita la clase `Movie.java` que podéis utilizar para almacenar los datos de las películas.

En la primera actividad, el listado de películas, se tendrá que:
* Solicitar en primer lugar el listado al servidor realizando una petición tipo GET a la URL "http://gbrain.dlsi.ua.es/videoclub/api/v1/catalog" (recordad que tiene que ser asíncrona).
* El servidor podrá devolver:
  * El array de películas en JSON y el código de respuesta 200.
  * Los códigos de error 404 o 500.
* Una vez completada la descarga tendréis que procesar el JSON (creando una lista de objetos tipo _Movie_).
* A partir de la lista obtenida se creará un adaptador que irá asociado a la lista. Este adaptador realizará la descarga de las portadas usando la técnica _lazy loading_ (el código de esta parte es muy parecido al del último ejercicio de la sección anterior).

Al pulsar sobre un elemento de esta lista se tendrá que abrir la vista detalle de la misma pasándole (en el _intent_ de llamada) los datos de la película.

En la vista detalle primero tendréis que recoger el _intent_ de llamada con todos los datos de la película, mostrar la información y crear un hilo para descargar la portada de la película.




## Ejercicio Opcional - Cliente completo

De forma opcional se pide completar el cliente del videoclub con el resto de métodos. En este caso se tendrá que solicitar en una pantalla inicial el usuario y contraseña, y guardarlos para poder utilizarlos posteriormente en las llamadas que lo soliciten. En el servidor de prueba "http://gbrain.dlsi.ua.es/videoclub" podéis utilizar el usuario "test123" y el password "test123".

Los métodos RESTful implementados de esta API son:

| Método | URI                  | Método   |
| ------ | -------------------- | -------- |
| GET    | /catalog             | Listado  |
| GET    | /catalog/{id}        | Elemento |
| POST   | /catalog             | Crear    |
| PUT    | /catalog/{id}        | Editar   |
| PUT    | /catalog/{id}/rent   | Alquilar |
| PUT    | /catalog/{id}/return | Devolver |
| DELETE | /catalog/{id}        | Eliminar |

> Todas las URI tienen el prefijo: "http://gbrain.dlsi.ua.es/videoclub/api/v1", por ejemplo, para obtener el listado será: "http://gbrain.dlsi.ua.es/videoclub/api/v1/catalog".

> Todos los métodos están protegidos con usuario y contraseña usando autenticación HTTP básica (excepto los métodos por GET de obtener el listado y de obtener un elemento).

> Solo será posible modificar los recursos que creéis vosotros mismos, es decir, tendréis que crear una película para posteriormente poder editarla o eliminarla. Si se intenta modificar o eliminar una película para la cual no tenéis permisos os devolverá el código de error 403.

> Es importante que al trabajar con la API se indiquen siempre las cabeceras de JSON, sobre todo al enviar los datos al servidor para que pueda procesarlos correctamente. Esta cabecerá tendrá que ser: `Content-Type: application/json`.


Los posibles códigos que devolverá el servidor son:

| Código | Significado |
| ------ | ----------- |
| 200    | Película creada, Modificada, alquilada, devuelva, eliminada correctamente |
| 401    | Usuario no autorizado |
| 403    | No tienes permisos para editar o eliminar el recurso |
| 404    | Recurso no encontrado |
| 500    | Error interno del servidor al procesar al petición |

Además, al crear una película en la cabecera se envía el `Location` con la nueva URI.
























