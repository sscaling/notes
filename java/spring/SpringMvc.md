Spring web MVC
==============

[https://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html)

Annotations
-----------

-	`@Controller` (or `@RestController` for JSON/XML interfaces)
-	`@RequestMapping` - method to receive request (specifics in `@GetMapping`, `@PostMapping` etc)
-	`@ReponseBody` - indidcate there is a mapped response to return

Request methods
---------------

Possible injected params:

-	`WebRequest` and `NativeWebRequest` - full access to the request context
-	`Locale`
-	`TimeZone` (`ZoneId` in Java8+)
-	`InputStream` / `Reader`
-	`OutputStream` / `Writer`
-	`HttpMethod`
-	`Principal` - The currently authenticated user
-	`@PathVariable` / `@MatrixVariable` / `@RequestParam` / `@RequestPart` / `@SessionAttribute` / `@SessionAttributes` / `@RequestAttribute`
-	`HttpEntity<?>` - The Servlet request HTTP headers and content
-	`Map` / `Model` / `ModelMap` - the model exposed to the web view
-	`Errors` / `BindingResult`
-	`SessionStatus`
-	`UriComponentsBuilder`

Possible response types (default supported):

\-
