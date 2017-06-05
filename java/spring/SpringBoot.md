Spring Boot
===========

[http://projects.spring.io/spring-boot/](http://projects.spring.io/spring-boot/)

General
-------

-	has it’s own CLI - Can be installed via hombrew / sdkman
-	spring run -d app.groovy - shows auto-configuration report for debugging what spring CLI assumed for us
-	Recommended to use with Spock : [https://code.google.com/archive/p/spock/](https://code.google.com/archive/p/spock/)
-	Auto-configuration: automatically create & inject beans based on class path. e.g. JdbcTemplate
-	Starter-dependencies: automatically pull in transitive dependencies with compatible versions.
-	CLI: manages dependencies/configuration. allows to focus on code.
-	Actuator: Inspect app internals at runtime.
-	Builds application fat-jar with embedded container (tomcat / jetty etc) for easier deployment.

Spring CLI
----------

```
spring init -dweb,jpa,security --build gradle // initialise new project with dependencies and builder type
spring run <app>
spring test <tests> …
```

Maven
-----

```
spring-boot:run goal
```
