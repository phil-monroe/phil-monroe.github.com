class: center, middle
# Dropwizard + Scala


---
class: middle

``` javascript
{
    "name": "Phil Monroe",

    "site": "http://philmonroe.com",

    "email": "phil@philmonroe.com",

    "about": "Ruby, Rails, Scala, Dropwizard, Hadoop, Scalding.",

    "work": [
        "Senior Software Engineer @ Workday",
        "Co-Founder and Developer @ RBSavvy",
        "Data Team Lead Developer @ Identified"
    ],

    "social": {
        "github": "phil-monroe",
        "facebook": "phil.monroe",
        "twitter": "@philmonroe",
        "linkedin": "philipmonroe"
    }
}
```


---
class: middle
``` javascript
{
    "slides": "philmonroe.com/slides/dropwizard-scala",

    "example": "https://github.com/phil-monroe/scala-dropwizard-example"
}
```


---
class: middle center
![scala](/slides/dropwizard-scala/img/dropwizard-scala.png)

Everything you want without the hassle.


---
class: center middle inverse
# Running Dropwizard Applications


---
class: middle inverse

``` sh
> java -jar dw-application.jar server config.yml
```


---
class: middle inversegi

``` sh
> java -jar dw-application.jar
```

.footnote[\* ops will love you...]


---
class: middle inverse
background-image: url(/slides/dropwizard-scala/img/dropwizard-starting.png)


---
class: center middle inverse
# Applications


---
# Basic Application

###### DwExampleApp.scala
``` scala
class DwExampleApp extends Application[DwExampleConfig] with Logging {
  override def getName = "Dropwizard Example"

  override def initialize(bootstrap: Bootstrap[DwExampleConfig]): Unit = {
    // Various setup
    bootstrap.addBundle(new ScalaBundle)
  }


  override def run(config: DwExampleConfig, env: Environment): Unit = {
    // Register RESTful things
    env.jersey().register(new HelloWorldResource)
  }
}

```


---
class: center middle inverse
# Configs


---
# Configs - Config Object

###### DwExampleConfig.scala
``` scala
class DwExampleConfig extends Configuration {
  @Valid
  @NotNull
  private val server: ServerFactory = new SimpleServerFactory


  @Valid
  val twitter = new TwitterConfig


  @Valid
  @NotNull
  val elasticSearchUrl = System.getenv("BONSAI_URL")

  @Valid
  @NotNull
  val hostname = System.getenv("HOSTNAME")
}
```


---
# Configs - Config File
###### config.yml
``` yaml
twitter:
  apiKey:         "API_KEY"
  apiSecret:      "API_SECRET"
  oauthToken:     "AUTH_TOKEN"
  oauthSecret:    "AUTH_SECRET"
  topics:         "scala"

elasticSearchUrl: "http://localhost:9200"

hostname:         "localhost:8080"
```


---
class: center middle inverse
# Resources


---
# Resources - Hello World!

###### resources/HelloWorldResource.scala
``` scala
@Path("/helloworld")
@Produces(Array(MediaType.TEXT_PLAIN))
class HelloWorldResource {

  @GET
  def helloWorld: String = {
    "Hello World"
  }
}
```

###### DwExampleApp.scala
``` scala
override def run(config: Config, env: Environment): Unit = {
  env.jersey().register(new HelloWorldResource)
}
```


---
# Resources - What time is it?

###### api/Time.scala
``` scala
case class Time(stamp: String)
```

###### resources/TimeResource.scala
``` scala
@Path("/time")
@Produces(Array(MediaType.APPLICATION_JSON))
class TimeResource {

  @GET
  def getTime: Time = {
    Time(currentTime)
  }

  def currentTime =
    new java.sql.Timestamp(System.currentTimeMillis()).toString
}
```


###### DwExampleApp.scala
``` scala
override def run(config: Config, env: Environment): Unit = {
  env.jersey().register(new TimeResource)
}
```


---
class: center middle inverse
# Managed Objects


---
# Managed Objects - Tweet Stream
###### core/tweet_stream/ManagedTweetStream.scala
``` scala
class ManagedTweetStream(config: TwitterConfig) extends Managed {
  private val hosebirdEndpoint = new StatusesFilterEndpoint()
  hosebirdEndpoint.trackTerms(config.topics)

  val hosebird = new ClientBuilder()
    .endpoint(hosebirdEndpoint)
    // snip...
    .build()


  def start(): Unit = {
    hosebird.connect()
  }

  def stop(): Unit = {
    hosebird.stop()
  }
}
```

###### setup/bundles/TweetStramBundle.scala
``` scala
 override def run(config: DwExampleConfig, env: Environment): Unit = {
    val tweetStream = new ManagedTweetStream(config.twitter)
    env.lifecycle().manage(tweetStream)
  }```


---
class: center middle inverse
# Bundles


---
class: middle, center
# Admin Interface


---
class: center, middle
# Health Checks


---
class: middle, center
# Metrics


---
background-image: url(/slides/dropwizard-scala/img/metrics1.png)


---
background-image: url(/slides/dropwizard-scala/img/metrics2.png)


---
# Metrics - Resource Annotations

###### resources/HelloResource.scala
``` scala
@Path("/helloworld")
@Produces(Array(MediaType.TEXT_PLAIN))
class HelloWorldResource {

  @GET
  @Metered
  @ExceptionMetered(name = "hello-errors")
  def helloWorld: String = {
    "Hello World"
  }
}

```

---
background-image: url(/slides/dropwizard-scala/img/metrics3.png)


---
# Metrics - Graphite

###### config.yml
``` yaml
metrics:
  reporters:
    - type: graphite
      host: localhost
      port: 8080
      prefix: my-key-prefix
```


---
class: center, middle
# Tasks


---
# Dang... What else?

- JDBI / Hibernate

- Migrations

- Authentication ( HTTP Basic or OAuth )

- Views ( Mustache or FreeMarker)


---
class: middle, center
# Beyond Dropwizard

### A few more libraries that make life awesome.

---
class: center, middle
# Swagger

### Super awesome API documentation


---
background-image: url(/slides/dropwizard-scala/img/swagger.png)


---
class: center, middle
# Jooq

### Programmatic SQL Queries


---
# Jooq
###### Setup
``` scala
// Database
class JooqExecutor(dataSource: DataSource, dialect: SQLDialect) {
  def apply[T](body: DSLContext => T): T = {
    val conn = dataSource.getConnection
    try{
      val dslContext = DSL.using(conn, jooqSqlDialect, new Settings)
      body(dslContext)
    } finally { conn.close }
  }
}

val dataSource = config.dbFactory.build(env.metrics(), "database")
env.lifecycle().manage(dataSource)

val jooq = new JooqExecutor(dataSource, SQLDialect.POSTGRESQL, new Settings )
```

###### Query
``` scala
jooq { sql =>
  sql.select(FOO_TABLE.VERSION.max)
    .from(FOO_TABLE)
    .where(FOO_TABLE.TYPE eq "bar")
    .fetchOne()._1
}

```


---
class: center, middle
# References
[dropwizard.github.io/dropwizard](https://dropwizard.github.io/dropwizard)

[helloreverb.com/developers/swagger](https://helloreverb.com/developers/swagger)

[jooq.org](http://www.jooq.org/)
