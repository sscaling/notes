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

Testing
=======

Annotation driven testing with Spring test-context framework. See summary in [SpringTesting.md](SpringTesting.md)

[https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html).

Some utilities can be found in `org.springframework.test.util`.

Use `@SpringBootTest` annotation instead of `@ContextConfiguration`. This creates an ApplicationContext in a SpringApplication so you get all Logging, external properties and other SpringBoot features.

`@SpringBootTest` will search up the package structure until it finds an `@SpringBootApplication` or `@SpringBootConfiguration` annotated class.

Test config can be overriden by supplying an `@TestConfiguration` class.

Random ports can be assigned by configuring `@SpringBootTest` and injected with `@LocalServerPort` or used by an `@Autowired` TestRestTemplate.:w

`@MockBean` creates a Mockito instance of a Bean (or similary use `@SpyBean`).

Integration testing
-------------------

Utilities in `org.springframework.test` will provide correct wiring of IoC.
