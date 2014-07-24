class: center, middle

# Dropwizard + Scala

???

- [Introduction]()
- [Running / Deployment](#5)
- [Services]()
- [Configs]()
- [Resources]()
- [Bundles]()
- [Admin Interface]()
  - [Metrics]()
  - [Health Checks]()
  - [Tasks]()



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
class: center middle inverse

``` bash
java -jar dw-application.jar server config.yml
```

---
class: center middle inverse

``` bash
java -jar dw-application.jar
```

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