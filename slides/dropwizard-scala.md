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
    },

    "slides": "philmonroe.com/slides/dropwizard-scala"
}
```


---
class: middle center

![scala](/img/dropwizard-scala.png)


---
class: center middle inverse

# Running Dropwizard Applications


---
class: middle inverse

``` sh
> java -jar dw-application.jar server config.yml
```

---
class: middle inverse

``` sh
> java -jar dw-application.jar
```

.footnote[\* ops will love you...]


---
class: center middle inverse

# Services

---
# Basic Service

###### TweetMeService.scala
``` scala
class TweetMeService extends Application[TweetMeConfig] with Logging {
  override def getName = "Tweet Me"

  val scalaBundle = new ScalaBundle


  override def initialize(bootstrap: Bootstrap[GitHubStatusConfig]): Unit = {
    // Various setup
    bootstrap.addBundle(scalaBundle)
  }


  override def run(config: GitHubStatusConfig, env: Environment): Unit = {
    // Register RESTful things
  }
}

```


---
class: center middle inverse

# Configs


---
class: center middle inverse

# Resources


---
# Hello World!

###### HelloWorldResource.scala
``` scala
@Path("/helloworld")
@Produces(Array(MediaType.APPLICATION_JSON))
class HelloWorldResource {

  @GET
  def helloWorld: String = {
    "Hello World"
  }
}
```


###### Service.scala
``` scala
override def run(config: Config, env: Environment): Unit = {
  env.jersey().register(new HelloWorldResource)
}
```


---
# What time is it?

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


###### Service.scala
``` scala
override def run(config: Config, env: Environment): Unit = {
  env.jersey().register(new TimeResource)
}
```

