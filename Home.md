# Welcome to wabase!

---

On this page, you can find the documentation for wabase. Please note that the current documentation isn't finished yet.
Therefore, it might not have some key concepts/ideas. If you have any questions - do not hesitate to post those
in the [Issues section](https://github.com/mrumkovskis/wabase/issues). 

---

## Table of Contents
* Introduction 
  * [Home](Home.md)
  * [Environment Setup](Setup-Install.md)
* View Definition
  * [Intro](View-Intro.md)
  * [View for get, list and count APIs](View-Get.md)
  * [View for insert and update APIs](View-InsertUpdate.md)



## TOC (wish list)

* [Introduction](intro/00_intro.md)
  * [Sample project structure](intro/10_sample.md)
  * Quick start guide  (**TBD**)
  * Multi module projects
* Reference guide
  * Tresql documentation
    * Introduction
    * API guide
    * Macros
    * [function signatures](ref/14_function_signatures.md)
  * [Table definition](ref/20_tabledef.md)
    * MD conventions
    * Multi db support
  * [View definition](ref/30_viewdef.md)
    * Basic structure
    * Action
    * Validation
  * [Scheduling](misc/10_scheduling.md)
  * [Sbt mojoz documentation](misc/20_sbt_mojoz.md)
  * Authentication / Authorization  (**TBD**)
  * Scala classes (API guide)
  * Rest client / wabase http client
  * Renderers (pdf, excel, csv, json)
  * File upload / download / cleanup
  * Configuration reference
* Tests
  * Yaml business scenarios
* Documentation

(**TBD**) - may change in wabase v. 8

---

## What is wabase for?

Wabase is designed as framework for building RESTful APIs. It is built on top of [akka-http](https://doc.akka.io/docs/akka-http/current/index.html) . 
Wabase is designed to be simple and easy to use, while still providing a lot of flexibility and power. 
It is designed to be used in a microservice architecture, where each service is responsible for a specific domain.

## Used libraries

Following libraries are used in wabase to provide functionality commonly needed in web applications: 

* jmustache - for templating - https://github.com/samskivert/jmustache
* akka and akka-http - for http server - https://doc.akka.io/docs/akka-http/current/index.html
* hikariCP - for connection pooling - https://github.com/brettwooldridge/HikariCP
* logback - for logging - http://logback.qos.ch/
* commons-validator - for validation - https://commons.apache.org/proper/commons-validator/
* commons-codec - for encoding - https://commons.apache.org/proper/commons-codec/
* lambdaworks Scrypt - for password hashing - https://github.com/wg/scrypt
* borer - for serialization - https://sirthias.github.io/borer/
* quartz - for scheduling - https://www.quartz-scheduler.org/
* openpdf - for pdf generation and parsing - https://librepdf.github.io/OpenPDF/  **TODO ???**
* flying saucer - for pdf generation - https://flyingsaucerproject.github.io/flyingsaucer/r8/guide/users-guide-R8.html
* simple-java-mail - for email sending - https://www.simplejavamail.org/
* graalvm js - for javascript execution - https://www.graalvm.org/reference-manual/js/
* scalatest - for testing - https://www.scalatest.org/
* akka testkit - for testing - https://doc.akka.io/docs/akka/current/testing.html

"In-house" products:

* tresql - for SQL generation -
* mojoz - for table metadata management -
* querease - for database access - 
* sbt-mojoz - for mojoz integration with sbt -

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