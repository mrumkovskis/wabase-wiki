# Welcome to wabase!

---

On this page, you can find the documentation for wabase. Please note that the current documentation isn't finished yet.
Therefore, it might not have some key concepts/ideas. If you have any questions - do not hesitate to post those
in the [Issues section](https://github.com/mrumkovskis/wabase/issues). 

---

## TOC

* [Introduction](intro/00_intro.md)
  * [Sample project structure](intro/10_sample.md)
  * [Quick start guide](intro/20_quick_start.md)
  * [Rest API overview](intro/30_rest_api.md)
  * Multi module projects
* Reference guide
  * [Tresql documentation](https://github.com/mrumkovskis/tresql/blob/develop/docs/language-guide.md)
    * Introduction
    * API guide
    * [Macros](ref/13_macros.md)
    * [Function signatures](ref/14_function_signatures.md)
  * [Table definition](ref/20_tabledef.md)
    * MD conventions
    * Multi db support
  * [View definition](ref/30_viewdef.md)
  * [View definition actions and validations](ref/40_viewdef_actions_and_validations.md)
  * [Scheduling](misc/10_scheduling.md)
  * [Sbt mojoz documentation](misc/20_sbt_mojoz.md)
  * Authentication / Authorization  (**TBD**)
  * Scala classes (API guide)
  * [Rest client / wabase http client](misc/50_clients.md)
  * [Renderers (pdf, excel, csv, json)](misc/60_renderers.md)
  * [Web sockets, Server-Sent Events , deferred responses](misc/70_deferred.md)
  * [File upload / download / cleanup](misc/80_files.md)
  * Configuration reference
* Tests
  * Yaml business scenarios
* Documentation

(**TBD**) - may change in wabase v. 8

---

## What is wabase for?

Wabase is designed as framework for building RESTful APIs. It is built on top of [pekko-http](https://pekko.apache.org). 
Wabase is designed to be simple and easy to use, while still providing a lot of flexibility and power. 
It is designed to be used in a microservice architecture, where each service is responsible for a specific domain.

## Used libraries

Following libraries are used in wabase to provide functionality commonly needed in web applications: 

* pekko - for http server - https://pekko.apache.org
* borer - for json serialization, deserialization - https://sirthias.github.io/borer/
* quartz - for scheduling - https://www.quartz-scheduler.org/
* flying saucer - for pdf generation - https://flyingsaucerproject.github.io/flyingsaucer/r8/guide/users-guide-R8.html
* simple-java-mail - for email sending - https://www.simplejavamail.org/
* jmustache - for templating - https://github.com/samskivert/jmustache
* graalvm js - for javascript execution - https://www.graalvm.org/reference-manual/js/
* json web token management (jwt) - https://github.com/jwt-scala/jwt-scala
* cryptography support - https://github.com/wg/scrypt
* hikariCP - for connection pooling - https://github.com/brettwooldridge/HikariCP
* logback - for logging - http://logback.qos.ch/
* commons-validator - for validation - https://commons.apache.org/proper/commons-validator/
* commons-codec - for encoding - https://commons.apache.org/proper/commons-codec/
* lambdaworks Scrypt - for password hashing - https://github.com/wg/scrypt


"In-house" products:

* tresql - for SQL generation -
* mojoz - for metadata definition -
* querease - for view execution -
* sbt-mojoz - for integration with sbt (like view compilation) -

For testing:

* scalatest - https://www.scalatest.org/
* hsqldb - https://hsqldb.org

# TODO:

* Describe project structure in general 
* Write a quick start guide
* Write reference guide
* Describe how to write multi module projects
* Describe how to write tests
* Describe how to write documentation - we don't have jet a documentation generator, there is something for ppa
* link tresql documentation
* describe app and appservice extensions
* sbt-mojos usage