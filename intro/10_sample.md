# Sample project structure

This is a simple project structure for a wabase project. 

* `db` - directory contains files related to database creation
  * `00_schema.sql` - contains schema creation script 
  * `01_data.sql` - contains data creation script
* `project` - sbt project builder
  * `build.sbt` - contains sbt build file for project builder
  * `FunctionSignature.scala` - contains sql function signatures for the project
  * `Macros.scala` - contains tresql macros for the project
  * `plugin.sbt` - contains sbt plugins 
* `src` - standard sbt project structure
  * `main`
    * `resources`
      * `md-conventions` - metadata conventions provide automatic field type detection
        * boolean-name-patterns.txt
        * date-name-patterns.txt
        * datetime-name-patterns.txt
        * integer-name-patterns.txt
      * `application.conf` - contains application specific configuration in typesafe config format
      * `logback.xml` - contains logging configuration
      * `constraint-translation.yaml` - contains automatic localization of db constraint messages
    * `scala`
      * `App.scala` - Main class for the application
      * `AppService.scala` - Controller for the application
      * `Server.scala` - Bootstraps the application
* `tables` - directory contains mojoz table definitions
  * foo_table.yaml 
* `views` - directory contains mojoz view definitions
  * foo_view.yaml 
* `build.sbt` - sbt build file
* `README.md` - project documentation



# File examples

## application.conf

This file contains application specific configuration in typesafe config format. 
For more details please see configuration reference.

Example: `application.conf`

```
akka {
  loggers = ["akka.event.slf4j.Slf4jLogger"]
  # Options: OFF, ERROR, WARNING, INFO, DEBUG
  loglevel = "DEBUG"
  logging-filter = "akka.event.slf4j.Slf4jLoggingFilter"
}

akka.http{
  server {
    server-header = ""
    remote-address-header = on
    parsing.max-content-length = 10m
  }

  client.parsing {
    max-response-reason-length = 512
  }
}

app {
  host = "http://localhost:8081/"
  ws-url = "ws://localhost:90/ws"
  portal_host = "https://portal.gg.uniso.lv/"
  files.path = "/tmp/files"
  notification-address = "<some address>"
  db.streaming.enabled = true
  upload.size-limit = 10m
  rest-client.request-timeout = 20 s
}

tresql {
  debug = true
  max-result-size = 100000
}

session.timeout = "12 h"
```

## logback.xml

This file contains logging configuration.

Example: `logback.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- encoders are assigned the type
             ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="akka" level="info"/>
   <!-- <logger name="org.wabase.tresql" level="info"/>-->
   <!-- <logger name="org.wabase.tresql.db" level="info"/>-->
    <logger name="org.wabase.package" level="info"/>
    <logger name="com.zaxxer.hikari" level="info"/>
    <logger name="gg.common.GgDbAccess" level="info"/>

    <root level="debug">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

## plugin.sbt

Sbt mojoz plugin must be enabled:

```sbt
resolvers += Resolver.sbtPluginRepo("releases")

addSbtPlugin("org.mojoz" % "sbt-mojoz" % "3.2.0")
```


## build.sbt

This file contains sbt build file.

Example: `build.sbt`

**TODO what is smallest working build xml for wabase??**
```

```

## *-name-patterns.txt

These files contain regular expressions for detecting field types in table definitions.

Example: `boolean-name-patterns.txt`

```
is_*
has_*
```

## constraint-translation.yaml

This file contains automatic localization of db constraint messages.

Example: `constraint-translation.yaml`

```yaml
uk_persona_card_number:
- Card number must be unique

fk_kla_certificate_parent_id:
 - Cant delete record, because it is used as parent record
 - Cant find parent record
```

## foo_table.yaml

Table folder contains table definitions in mojoz format. Folder may contain multiple files, table definition
separation in files is up to developer.
For more details please see table definition reference.

Example: `foo_table.yaml`

```yaml
table: file_extensions
comments: List of file extensions supported in system
columns:
  - id
  - file_type ! (image document) : File type enumeration 
  - is_enabled          ! = true : Is file type enabled
  - content_type               ! : File content type
  - extension                  ! : File extension
  - description                  : File type description
```

## foo_view.yaml

View folder contains view definitions in mojoz format. Folder may contain multiple files, view definition
separation in files is up to developer.
For more details please see view definition reference.

```yaml
name: file_extension_list
table: file_extensions e
comments: List of file extensions
api: list, count
fields:
  - id
  - content_type
  - extension
  - is_enabled
filter:
  - is_enabled
  - content_type
  - extension


name: file_extension_details
table: file_extensions e
comments: File extension details
api: get save delete
fields:
  - id
  - file_type
  - is_enabled
  - content_type
  - extension
  - description
```

## FunctionSignature.scala

## Macros.scala

## App.scala

## AppService.scala

## Server.scala

