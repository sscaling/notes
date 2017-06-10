Spring Testing
==============

[http://docs.spring.io/spring/docs/4.3.8.RELEASE/spring-framework-reference/htmlsingle/#testing](http://docs.spring.io/spring/docs/4.3.8.RELEASE/spring-framework-reference/htmlsingle/#testing)

Example:

```
@RunWith(SpringRunner.class)
@ContextConfiguration({"/app-config.xml", "/test-data-access-config.xml"})
@ActiveProfiles("dev")
public class MyTest {
    @Autowired
    private ApplicationContext applicationContext;

    ...
}
```

-	@RunWith(SpringRunner.class) - use the annotations defined for the test
-	@BootstrapWith - how the Spring TestContext framework is bootstrapped
-	@WebApplicationConfig - indicates the underlying context should be a WebApplicationContext
-	@ContextConfiguration - determine how to load the ApplicationContext`
	@ContextConfiguration(classes = TestConfig.class)
	`
-	@ContextHierarchy - defines hierarchy of ApplicationContexts for integration testing
-	@ActiveProfile - set the spring profile(s)
-	@TestPropertySource - configure location of properties and inlined properties `
	@TestPropertySource("/test.properties")
	or
	@TestPropertySource(properties = { "timezone = GMT", "port: 4242" })`
-	@DirtiesContext - indicates underlying ApplicationContext has been dirtied during execution of the test. Can be appled to class, or method to indicate method dirties the context. `
	@DirtiesContext(classMode = BEFORE_CLASS)`
-	@Commit / @Rollback / @BeforeTransaction / @AfterTransaction

Within a Spring TextContext all standard annotations (such as @AutoWired) are supported.

Mocks
-----

-	org.springframework.mock.env - for Environment
-	org.springframework.mock.jndi
-	org.springframework.mock.web - Servlets

Utilities
---------

`AbstractJUnit4SpringContextTests` provides an ApplicationContext as a protected instance field.
