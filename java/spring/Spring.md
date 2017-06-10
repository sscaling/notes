Spring core / framework
=======================

Main docs:

[http://docs.spring.io/spring-framework/docs/current/spring-framework-reference/html/overview.html](http://docs.spring.io/spring-framework/docs/current/spring-framework-reference/html/overview.html)

*NOTE*: Specifically of note is the section on Logging configuration

Guides:

[https://spring.io/guides](https://spring.io/guides)

Dependency Injection (IoC)
--------------------------

Main spring dependencies:

-	org.springframework.beans
-	org.springframework.context

Main concepts:

-	A Bean is the thing managed by Spring IoC container. It is instantiated anad managed by Spring. Dependencies are represented in configuration metadata.
-	ApplicationContext - represents the Spring IoC container. Responsible for Bean management. Several concrete implementations (XML etc)
-	Beans can be configured via constructor / setter-based tinjection
-	Constructor arguments can be explicitly named with `@ConstructorProperties({"foo", "bar"})` annotation.
-	Autowiring - resolve collaborators automatically by inspecting ApplicationContext.
-	(interface) ApplicationContextAware - implementing this allows class to negate some IoC and call into the ApplicationContext when needed. *NOTE* This is now tightly coupled with the Spring framework.

### Shutting down

If not using in a web application environment, make sure shutdown hook is registered

```
ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext(new String []{"beans.xml"});
ctx.registerShutdownHook();
```

### Dependency Resolution

The order in which dependencies are instantiated.

1.	ApplicationConbtext is instantiated with all bean metadata configuration
2.	Each bean's dependencies are provided to the bean when it is instantiated
3.	Each property/constructor argument is supplied with value / reference
4.	Each property/constructor argument is supplied with converted value \(i.e. `"12" -> <int>`\)

Unless a singleton, beans are only created when they are requested.

Bean configuration
------------------

Beans are represented by a BeanDefinition - containing metadata to references etc.

-	basic - @Bean method defined within an @Configuration class
-	ids must be unique within the container. If no name is supplied then it will be generated.
-	Can be scoped. Default is 'singleton', others include 'prototype', 'session', 'request' etc.

### Annotations

*NOTE*: Annotation injection is performed before XML injection.

General:

-	@Required - [setter] - Must be provided at config time
-	@Autowired - [ctor,setter,func,field] - Wire in dependencies (JSR-250 - @Inject). @Autowried(required=false) ignore missing.
	-	Can be also used to wrie in well-known interfaces for resolvable dependencies. i.e. ApplicationContext, Environment etc.
-	@Primary - indicate a bean is given priorty when multiple are available.
-	@Resource / @Qualifier - mark / select name based Beans
-	@PostConstruct / @PreDestroy - lifecycle annotations (see Life cycles below)
-	@Order -
-	@Priority -
-	@Lazy - mark as lazily initialized

Scoping:

-	@Scope("prototype")
-	@RequestScope - for the lifetime of a HTTP request
-	@SessionScope - for the lifetime of a HTTP session
-	@ApplicationScope - for the entire application

### Life cycles

-	(interface) InitializingBean - additional initialization behaviour (JSR-250 - @PostConstruct)
-	(interface) DisposableBean - additional teardown behaviour (JSR-250 - @PreDestroy)
-	(interface) Lifecycle - any Bean implementing this will have start()/stop() called by ApplicationContext. Useful for managing resources, i.e. DB pool.
-	(interface) SmartLifecycle - extends Lifecycle, provides a numeric integer representing order (so managed resources can be controlled)

Classpath scanning and managed components
-----------------------------------------

How to detect the candidate components suitable for injection.

```
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```

Detection:

-	@ComponentScan\(`[basePackages = ][, nameGenerator = x.class]`\)

Markers:

-	@Configuration - indicates class provides Bean definitions for the container
	-	@Import(x.class) - can be used to import another @Configuration class
-	@Conditional(x.class) - indicate Condition implementations that must return true before Bean is registered
-	@Component - generic spring-managed component. (@JSR-330 - @Named / @ManagedBean)
	-	@Bean\(`[initMethod="x"][,destroyMethod="y"]`\) - contribute a Bean definition to the container
		-	by default close() or shutdown() methods are enlisted as a destruction callback. Typically for DataSource for Java EE app servers disable with @Bean(destroyMethod="")
-	@Service - service layer sterotype.
-	@Controller -
-	@Repository - marks a class as a sterotype of a repository.

Environment
-----------

```
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
```

or:

```
-Dspring.profiles.active=development
```

Models profiles and properties within the container.

-	Profile - a named logical group of bean defintions that are registered when a profile is active
-	Properties - model environment specific configuration in properties files, system envs, JNDI etc

Annotations:

-	@Profile("<name>") - the naemd profile this @Configuration class is active for
	-	The 'default' profile is enabled by default. Used when no profile is active.
-	@PropertySource("classpath:/path/to/properties").
	-	Used with `@Autowired Environment env` the @Bean exposed can use `env.getProperty("<name>")` to access the .properties values.
