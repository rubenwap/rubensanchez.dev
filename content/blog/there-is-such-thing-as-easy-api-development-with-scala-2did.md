---
stackbit_url_path: posts/there-is-such-thing-as-easy-api-development-with-scala-2did
title: There is such thing as easy API development with Scala
date: '2020-02-29T14:59:19.821Z'
excerpt: >-
  I have to admit I am far from expert in Scala, that's why when recently I had
  to prototype an API in...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--OSJ2mF1T--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/mwfexnm2g06fobhzig7s.png
comments_count: 0
positive_reactions_count: 8
tags:
  - scala
  - cask
  - api
canonical_url: >-
  https://dev.to/rubenwap/there-is-such-thing-as-easy-api-development-with-scala-2did
template: post
---
I have to admit I am far from expert in Scala, that's why when recently I had to prototype an API in this language, I shuddered. Frameworks like [Akka HTTP](https://doc.akka.io/docs/akka-http/current/index.html) or [HTTP4S](https://http4s.org/) were far too complex for a novice in the field like me, because learning Scala is not about the syntax, but about the mindset. And it's only logical that someone coming from the field of totally different languages such as Python or Javascript, can have a little trouble understanding concepts like the actor model or the functional proper way of using HTTP4S. 

### The original Actor Model

![Actor model in action](https://dev-to-uploads.s3.amazonaws.com/i/08qrrc2ecgwcpctlvdse.jpg) 

### Cask

That's why I welcomed the discovery of [Cask](https://github.com/lihaoyi/cask), a micro HTTP framework which tries to follow closely the footsteps of Flask for Python. This looked like a good compromise between not switching straight away to Akka/Http4s, but still enjoying the benefits of a strongly typed language as interesting as Scala. 

## Example of Cask usage for an API endpoint from a Postgres database

### Setting up:

The author recommends to use his own build tool called [Mill](http://www.lihaoyi.com/mill/). Even though I found it easy to understand, I decided to stick with SBT out of habit, since that is what I had used in the past. 

#### Dependencies for this project:


```scala
libraryDependencies ++= Seq("com.lihaoyi" %% "cask" % "0.2.9",
  "com.lihaoyi" %% "utest" % "0.7.4" % Test,
  "com.lihaoyi" %% "requests" % "0.5.1",
  "org.postgresql" % "postgresql" % "42.2.8",
  "ch.qos.logback" % "logback-classic" % "1.2.3",
  "io.getquill" %% "quill-jdbc" % "3.2.0",
  "com.typesafe.scala-logging" %% "scala-logging" % "3.9.2",
  "com.typesafe" % "config" % "1.4.0")
```


#### What are we retrieving in the API

This is an API which retrieves information about movies showing in the local cinemas of my city, so the modeled data is basically a movie. We create a case class, because that is what will be used by [Quill](https://getquill.io/) to map to the values in the Postgres table and be able to retrieve the data. So when I do:


```scala
case class Movies(
                   cinema: String,
                   details: String,
                   title: String,
                   movie_date: String,
                   movie_time: String
                 )
```


with this I actually mean that there is a table with the name 
`movies`
 that has as fields each of the case class fields. By the way! I am aware that certain data types are not correct. Date and time should not be strings. However, this is for the moment done like this to deal with data in a database that has been scraped and does not conform to proper date format yet. Work in progress!

#### How to manage all the connection settings for the DB

As simple as creating an 
`application.properties`
 file in your resources folder with the following content


```scala
ctx.dataSourceClassName=org.postgresql.ds.PGSimpleDataSource
ctx.dataSource.user=your_user
ctx.dataSource.password=your_password
ctx.dataSource.databaseName=your_db_name
ctx.dataSource.portNumber=port_(5432_for_postgres)
ctx.dataSource.serverName=your_server
ctx.connectionTimeout=up_to_you
```


Of course, take the necessary precautions when dealing with version control and this file. 

#### So where is my easy API? 

There you go. We do this via defining a route for 
`movies`
 with a function which creates a context to run the database query. See the Quill documentation for more details, but in this example we are basically doing a 
`SELECT * from movies`
, so it couldn't get easier. 
  


```scala
object moviescrape extends cask.MainRoutes with LazyLogging {
  override def port: Int = 8081
  lazy val ctx = new PostgresJdbcContext(SnakeCase, "ctx")
  import ctx._
  @cask.get("/movies")
  def getMovies() = {
    val content = ctx.run(query[Movies])
      .map(m => {ujson.Obj("cinema" -> ujson.Str(m.cinema),
        "details" -> ujson.Str(m.details), "title" -> ujson.Str(m.title),
        "movie_date" -> ujson.Str(m.movie_date),
        "movie_time" -> ujson.Str(m.movie_time))})

    upickle.default.write(ujson.Obj("data" -> content))
  }

  initialize()
}
```


Things might get a bit tricky around the part of returning the proper json with the response. I tried a few different tools, such as Circe and the author's own Ujson and at the end I settled with the latter.  

### But... will it scale? 

I don't think this library is meant to handle your super scalable APIs for deployments that need to bear a load of millions of users, but if you are ever in the need of playing around with API development in Scala in order to understand the language a little better, Cask will definitely help you to get your feet wet without the headaches of jumping straight into Akka HTTP. 

## Resources

* [Cask](http://www.lihaoyi.com/cask/)
* [Quill](https://getquill.io/)
* [Repo with full code example](https://github.com/rubenwap/ov-movies-api)
 


*[This post is also available on DEV.](https://dev.to/rubenwap/there-is-such-thing-as-easy-api-development-with-scala-2did)*


<script>
const parent = document.getElementsByTagName('head')[0];
const script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/4.1.1/iframeResizer.min.js';
script.charset = 'utf-8';
script.onload = function() {
    window.iFrameResize({}, '.liquidTag');
};
parent.appendChild(script);
</script>    
