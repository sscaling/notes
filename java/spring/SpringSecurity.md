Spring Security
===============

[http://docs.spring.io/spring-security/site/docs/4.2.2.RELEASE/reference/htmlsingle/](http://docs.spring.io/spring-security/site/docs/4.2.2.RELEASE/reference/htmlsingle/)

Authentication
--------------

-	(interface) AuthenticationManager - Authenticates an Authentication (can throw RTE)
-	ProviderManager is common implementation, can delegate to chain of AuthenticationProviders.

Authorization
-------------

-	AccessDecisionManager - decide what to do once authenticated.
-	(interface) AccessDecisionVoter - delegate of AccessDecisionManager, considers Authenticate and a secure Object representing the thing that is being secured.

SecurityContext
---------------

```
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
assert(authentication.isAuthenticated);
```

-	Spring Security is thread bound.
-	SecurityContextHolder static methods can be used to get the SecurityContext
-	Authentication details are injected via @AuthenticationPrincial parameter to an @RequestMapping function.

General notes
-------------

-	Web Security - based on chained servlet filters. Filters are managed by Spring boot via @Order annotation, or implement Ordered (Alternatively can be part of FilterReigstrationBean).
-	Spring Security is one physical filter, but delegates to a chain of internal filters. i.e. could match /foo/\*\* to one filter chain etc.
-	Spring Actuator (managing endpoints) - when added to an application, you automatically get an additional fitler chain.
