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
# Agenda

1. Introduction
2. Deep-dive
3. ...


---

# Introduction

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