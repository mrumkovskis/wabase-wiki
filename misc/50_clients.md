# RestClient

Wabase provides helper methods to make rest calls. RestClient is a simple wrapper around akka-http client.

In most cases two methods are used: `get` and `post`. Both methods return `Future[R]`, 
if `FromResponseUnmarshaller[R]` is provided and take `String` url and parameters.

## httpGet

```scala
lazy val port = 8080
lazy val serverPath = s"http://localhost:$port/"
def httpGet[R](path: String, params: Map[String, Any] = Map.empty, headers: iSeq[HttpHeader] = iSeq(),
                 cookieStorage: CookieMap = getCookieStorage, timeout: FiniteDuration = requestTimeout)
                     (implicit unmarshaller: FromResponseUnmarshaller[R])
```

Parameters are: 

* path - absolute or relative to `serverPath` path to the resource
* params - list of query parameters passed to resource as `?param1=value1&param2=value2`
* headers - list of headers
* cookieStorage - cookie storage, cookie storage based on `scala.collection.mutable.Map[String, String]`, `getCookieStorage` returns `ThreadLocal` instance of it
* timeout - request timeout
* unmarshaller - implicit unmarshaller for response to expected result type

Example: 

```scala
package test.app.remote

import org.wabase.client.RestClient
import scala.concurrent.ExecutionContext.Implicits.global

object Test extends scala.App{
  val client = new RestClient{
    override def actorSystemName = "rest-client"
  }

  client.httpGet[String]("https://jsonplaceholder.typicode.com/posts/1").foreach(println)

}
```


## httpPost

```scala
def httpPost[T, R](method: HttpMethod, path: String, content: T, headers: iSeq[HttpHeader] = iSeq(),
                   cookieStorage: CookieMap = getCookieStorage, timeout: FiniteDuration = requestTimeout)
                  (implicit marshaller: Marshaller[T, RequestEntity], unmarshaller: FromResponseUnmarshaller[R])
```

Parameters are:

* method - http method, this can be `POST`, `PUT`, `DELETE`, etc.
* path - absolute or relative to `serverPath` path to the resource
* content - content to be sent to the server in request body, will require implicit marshaller `ToEntityMarshaller[T]` for given content type
* headers - list of headers
* cookieStorage - cookie storage, cookie storage based on `scala.collection.mutable.Map[String, String]`, `getCookieStorage` returns `ThreadLocal` instance of it
* timeout - request timeout
* marshaller - implicit marshaller for request
* unmarshaller - implicit unmarshaller for response


This example is of: 

1. Preparing a simple rest client
2. Creation of authentication headers
3. Creating multipart form data
4. Calling `httpPost` that in turn:
   1. Marshals form to request entity
   2. Sends request to server
   3. Unmarshals response to JsValue

```scala
package test.app.remote

import akka.http.scaladsl.model.Multipart.FormData.BodyPart
import akka.http.scaladsl.model.{HttpHeader, HttpMethods, Multipart}
import akka.http.scaladsl.model.headers.RawHeader
import org.wabase.client.RestClient
import org.wabase.jDate
import spray.json._
import test.app.AppService._

import scala.concurrent.Future

class FormExample (url: String, authKey: String) extends RestClient {
   override lazy val serverPath: String = url
   override def actorSystemName = "form-client"

   val headers: List[HttpHeader] = List(RawHeader("Authorization", authKey))

   def postForm(name: String, surname: String,description: String, age: jDate): Future[JsValue] = {
      val sdf = new java.text.SimpleDateFormat("yyyy-MM-dd")

      val formData = Multipart.FormData(
         BodyPart.Strict("name", name),
         BodyPart.Strict("surname", surname),
         BodyPart.Strict("description", description),
         BodyPart.Strict("age", sdf.format(age)),
      )

      httpPost[Multipart.FormData, JsValue](
         HttpMethods.POST,
         "/form",
         formData,
         headers = headers
      )
   }
}

object Test extends scala.App{
   val example = new FormExample("http://localhost", "authKey")
   example.postForm("John", "Doe", "Description", new jDate())
}

```

## Marshaling/Unmarshalling

Marshaling is the process of converting an object into a format that can be transmitted over the network.
In the example above, the `postForm` method takes a `Multipart.FormData` object and sends it to the server. 
The `httpPost` method uses an implicit marshaller to convert the `Multipart.FormData` object into a `RequestEntity` that can be sent to the server.
Unmarshalling is the process of converting a response from the server into an object that can be used in the client code.

In addition to [Akka http provided marshallers and unmarshallers](https://doc.akka.io/docs/akka-http/current/common/marshalling.html) ,
Wabase provides a few additional ones:

* map marshaller - `ToEntityMarshaller[Map[String, Any]]`
* list of maps marshaller - `ToEntityMarshaller[List[Map[String, Any]]]`
* future map unmarshaller - `ToEntityMarshaller[Future[Map[String, Any]]]`
* option unmarshaller - `FromResponseUnmarshaller[Option[T]]`
* tuple2 unmarshaller - `FromEntityUnmarshaller[(T1, T2)]`
* content type unmarshaller - `FromEntityUnmarshaller[ContentType]`
* status code unmarshaller - `FromResponseUnmarshaller[StatusCode]`
* source unmarshaller - `FromEntityUnmarshaller[Source[ByteString, Any]]`
* headers unmarshaller - `FromResponseUnmarshaller[Seq[HttpHeader]]`
* unit unmarshaller - `FromResponseUnmarshaller[Unit]`

Wabase also provides a few helper marshallers for marshalling/unmarshalling DTOs:

* wabase dto unmarshaller - `dtoUnmarshaller[T <: Dto](implicit m: Manifest[T]): FromEntityUnmarshaller[T]`
* wabase list dto unmarshaller - `dtoSeqUnmarshaller[T <: Dto](implicit m: Manifest[T]): FromEntityUnmarshaller[Seq[T]]`
* wabase dto marshaller - `dtoForViewMarshaller: ToEntityMarshaller[(Dto, String)]` and `dtoMarshaller: ToEntityMarshaller[Dto]`
* wabase list dto marshaller - `dtoSeqForViewMarshaller: ToEntityMarshaller[(Seq[Dto], String)]` and `dtoSeqForViewMarshaller: ToEntityMarshaller[Seq[Dto]]`

These wabase marshallers deal with DTO marshalling and unmarshalling to and from JSON. 
For marshalling responses to other formats see [Renderers Section](60_renderers.md)



# WabaseHttpClient