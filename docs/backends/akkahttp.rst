.. _akkahttp:

akka-http backend
=================

To use, add the following dependency to your project::

  "com.softwaremill.sttp" %% "akka-http-backend" % "1.1.4"

This backend depends on `akka-http <http://doc.akka.io/docs/akka-http/current/scala/http/>`_. A fully **asynchronous** backend. Sending a request returns a response wrapped in a ``Future``.

Next you'll need to add an implicit value::

  implicit val sttpBackend = AkkaHttpBackend()
  
  // or, if you'd like to use an existing actor system:
  implicit val sttpBackend = AkkaHttpBackend.usingActorSystem(actorSystem)

This backend supports sending and receiving `akka-streams <http://doc.akka.io/docs/akka/current/scala/stream/index.html>`_ streams of type ``akka.stream.scaladsl.Source[ByteString, Any]``.

To set the request body as a stream::

  import com.softwaremill.sttp._
  import com.softwaremill.sttp.akkahttp._
  
  import akka.stream.scaladsl.Source
  import akka.util.ByteString
  
  val source: Source[ByteString, Any] =   ...
  
  sttp
    .streamBody(source)
    .post(uri"...")

To receive the response body as a stream::

  import com.softwaremill.sttp._
  import com.softwaremill.sttp.akkahttp._
  
  import akka.stream.scaladsl.Source
  import akka.util.ByteString
  
  implicit val sttpBackend = AkkaHttpBackend()
  
  val response: Future[Response[Source[ByteString, Any]]] = 
    sttp
      .post(uri"...")
      .response(asStream[Source[ByteString, Any]])
      .send()
    
