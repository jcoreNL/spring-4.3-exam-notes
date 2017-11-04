# Spring 4.3 Certification Exam Study Notes 

Remember: Unless a question explicitly references Spring Boot you can assume Spring Boot is not involved in any question.

## Container, Dependency, and IOC

### What is dependency injection and what are the advantages?
 dependency injection is a technique whereby one object supplies the dependencies of another object. A dependency is an 
 object that can be used (a service). An injection is the passing of a dependency to a dependent object (a client) that 
 would use it. The service is made part of the client's state.[1] Passing the service to the client, rather than 
 allowing a client to build or find the service, is the fundamental requirement of the pattern.
 
### What is an interface and what are the advantages of making use of them in Java?
An interface is a contract that defines a set of functionality which must be implemented by a concrete class. Interfaces 
allow for multiple inheritance in Java. 
Interfaces allow for dependency injection without knowing the exact implementation.

### What is meant by “application-context” and how do you create one?
The ApplicationContext is the central interface within a Spring application for providing configuration information to 
the application. It is read-only at run time, but can be reloaded if necessary and supported by the application. 
A number of classes implement the ApplicationContext interface, allowing for a variety of configuration options and 
types of applications.

The ApplicationContext provides:
- Bean factory methods for accessing application components.
- The ability to load file resources in a generic fashion.
- The ability to publish events to registered listeners.
- The ability to resolve messages to support internationalization.
- Inheritance from a parent context.

Create an application-context:
`ApplicationContext applicationContext = new ClassPathXmlApplicationContext("/application-context.xml");`

### What is the concept of a “container” and what is its lifecycle?
The application-context (see last question) represents the Spring Inversion of Control Container. 

**Todo: container lifecycle?**

### How do you use dependency injection using Java configuration

### How do you use dependency injection in XML, using constructor or setter injection

### How do you use dependency injection using annotations (`@Component`,`@Autowired`)

### What is Component scanning
Spring is able to scan given packages for bean classes annotated with `@Component` or any of the Stereotype annotations 
and add them to the bean definitions dynamically.

### What are Stereotypes and Meta-Annotations
Annotations denoting the roles of types or methods in the overall architecture (at a conceptual, rather than implementation, level).

- `@Component`
- `@Controller`
- `@Repository`
- `@Service`

### Scopes for Spring beans. What is the default?
- **singleton**_(default)_: Scopes a single bean definition to a single object instance per Spring IoC container.
- **prototype**: Scopes a single bean definition to any number of object instances.
- **request**: Scopes a single bean definition to the lifecycle of a single HTTP request; that is each and every HTTP 
request will have its own instance of a bean created off the back of a single bean definition. Only valid in the context 
of a web-aware Spring ApplicationContext.
- **session**: Scopes a single bean definition to the lifecycle of a HTTP Session. Only valid in the context of a 
web-aware Spring ApplicationContext.
- **global session**: Scopes a single bean definition to the lifecycle of a global HTTP Session. Typically only valid 
when used in a portlet context. Only valid in the context of a web-aware Spring ApplicationContext.

### What is an initialization method and how is it declared in a Spring bean?
The initialization method on a bean is called after all injections are complete. More precisely after the injections 
`@PostConstruct` is called, followed by the `InitializingBean.afterPropertiesSet()` method and concluded with the init-method.

- **xml declaration**: `<bean id="myBean" class="com.example.MyBean" init-method="initMethodName" />`
- **java/annotation declaration**: ` @Bean(initMethod = "initMethodName")`

### What is a destroy method, how is it declared and when is it called?
The destroy method on a bean is called just before the bean lifecycle ends. More precisely first
`@PreDestroy` is called, followed by the `DisposableBean.destroy()` method and concluded with the destroy-method.

- **xml declaration**: `<bean id="myBean" class="com.example.MyBean" destroy-method="destroyMethodName" />`
- **java/annotation declaration**: ` @Bean(destroyMethod = "destroyMethodName")`

### What is a BeanFactoryPostProcessor and what is it used for?
Allows for custom modification of an application context's bean definitions, adapting the bean property values of the 
context's underlying bean factory.  
A BeanFactoryPostProcessor may interact with and modify bean definitions, but never bean instances. 
Doing so may cause premature bean instantiation, violating the container and causing unintended side-effects. 
If bean instance interaction is required, consider implementing BeanPostProcessor instead.

### What is a BeanPostProcessor and how is the difference to a BeanFactoryPostProcessor? What do they do? When are they called?
Factory hook that allows for custom modification of new bean instances, e.g. checking for marker interfaces or wrapping 
them with proxies. 
A BeanFactoryPostProcessor may interact with and modify bean definitions, but never bean instances. 
Doing so may cause premature bean instantiation, violating the container and causing unintended side-effects. 
If bean instance interaction is required, consider implementing BeanPostProcessor instead.

### Are beans lazily or eagerly instantiated by default? How do you alter this behavior?
Beans are eagerly instantiated by default. This behavior can be altered by either setting the `lazy-init="true"` using 
xml configuration or adding the `@Lazy` annotation to a bean definition.

xml example: `<bean id="myBean" class="com.example.MyBean" lazy-init="true"/>`

### What does component-scanning do?
Spring is able to scan given packages for bean classes annotated with `@Component` or any of the Stereotype annotations 
and add them to the bean definitions dynamically.

### What is the behavior of the annotation `@Autowired` with regards to field injection, constructor injection and method injection?
The `@Autowired` annotation injects an instance of an implementation of the needed class or interface into the field, 
constructor or method. For constructors and methods the dependency needs to be an argument to that method or constructor.

### How does the `@Qualifier` annotation complement the use of `@Autowired`?
In case there are multiple implementations of the dependency that you are trying to autowire, Spring may not be able to 
figure out which instance should be injected. The `@Qualifier` annotation is used to specify which implementation is 
required.

### What is the role of the `@PostConstruct` and `@PreDestroy` annotations? When will they get called?
A method annotated with `@PostConstruct` will be called directly after all injections have taken place.  
A method annotated with `@PreDestroy` will be called just before the bean lifecycle ends and the bean is destroyed.

### What is a proxy object and what are the two different types of proxies Spring can create?
A proxy object is like a wrapper around the object instance which can add behavior on top of the original object's 
implementation.

Spring can create JDK Dynamic Proxies and CGLIB Proxies.

### What is the power of a proxy object and where are the disadvantages?
The power of proxies is that they can add behavior to an object without cluttering the actual object implementation with 
this behavior.

### What are the limitations of these proxies (per type)?
- JDK dynamic proxies:
    - Can only proxy objects which implement at least one interface and only the methods 
    defined by the implemented interfaces.
- CGLIB:
    - final methods cannot be advised, as they cannot be overriden.
    - You will need the CGLIB 2 binaries on your classpath, whereas dynamic proxies are available with the JDK. 
    Spring will automatically warn you when it needs CGLIB and the CGLIB library classes are not found on the classpath.
    - The constructor of your proxied object will be called twice. This is a natural consequence of the CGLIB proxy model 
    whereby a subclass is generated for each proxied object. For each proxied instance, two objects are created: the actual 
    proxied object and an instance of the subclass that implements the advice. This behavior is not exhibited when using JDK proxies. 
    Usually, calling the constructor of the proxied type twice, is not an issue, as there are usually only assignments 
    taking place and no real logic is implemented in the constructor.

### How do you inject scalar/literal values into Spring beans?
The `@Value` annotation is used to inject literal values into fields, constructors or methods. With `@Value` it is 
possible to inject property- and environment values. It is also possible to use _SpEL_ in `@Value`.

**xml literal value constructor injection:**  
```xml
<bean id="myBean" class="com.example.MyBean">
    <constructor-arg index="0" value="myLiteral"/>
</bean>
```

### How are you going to create a new instance of an ApplicationContext?
Create an application-context:
`ApplicationContext applicationContext = new ClassPathXmlApplicationContext("/application-context.xml");`


### What is a prefix?

### What is the lifecycle on an ApplicationContext?

### What does the `@Bean` annotation do?
The `@Bean` annotation registers a Spring bean which can be registered in the application context 

### How are you going to create an ApplicationContext in an integration test or a JUnit test?

### What do you have to do, if you would like to inject something into a private field?
Spring injects fields annotated with `@Autowired` through reflection. This also works for private fields so no extra 
effort is needed. Additionally constructor and setter injection also work for setting private fields.

### What are the advantages of JavaConfig? What are the limitations?

### What is the default bean id if you only use `@Bean`?
The name of the method annotated with `@Bean` wil be used as the default id of the bean.

### Can you use `@Bean` together with `@Profile`?
Yes, the `@Profile` annotation can be used on method level to include/exclude a bean based on the active profile.

### What is Spring Expression Language (SpEL for short)?

### What is the environment abstraction in Spring?

### What can you reference using SpEL?

### How do you configure a profile. What are possible use cases where they might be useful?

### How many profiles can you have?
There is no limit to the amount of profiles you can define and use in a Spring application.

### How do you enable JSR-250 annotations like `@PostConstruct`?
JSR-250 annotations can be enabled using `<context:annotation-config/>` in xml configuration or using the annotation `@AnnotationDrivenConfig` 

### Why are you not allowed to annotate a final class with `@Configuration`
Spring creates a CGLIB proxy for each configuration class. CGLIB proxies work through inheritance which is not possible 
with final classes.

### Why must you have a default constructor in your `@Configuration` annotated class?
Prior to Spring 4, CGLIB-based proxy classes require a default constructor. And this is not the limitation of CGLIB 
library, but Spring itself. Fortunately, as of Spring 4 this is no longer an issue. CGLIB-based proxy classes no longer 
require a default constructor.

### Why are you not allowed to annotate final methods with `@Bean`?
Spring creates a CGLIB proxy for each configuration class. CGLIB proxies work through inheritance which is not possible 
with final methods.

### What is the preferred way to close an application context?

### How can you create a shared application context in a JUnit test?

### What does a static `@Bean` method do?
`@Bean` annotated methods are proxied using CGLIB through inheritance. Static methods cannot be overridden which means 
they cannot be proxied. This way the static method annotated with `@Bean` will generate a new instance of the bean on each 
call which is not the expected singleton behavior.

### What is a ProperySourcesPlaceholderConfigurer used for?

### What is a namespace used for in XML configuration

### What are the <context/> elements covered in the course and what do they do?

### What is `@Value` used for?

### What is the difference between $ and # in `@Value` expressions?

## Aspect oriented programming

### What is the concept of AOP? Which problem does it solve?
Aspect Oriented Programming solves the problem of crosscutting concerns, which are concerns that are mostly scattered 
throughout an application because the same concept applies to many parts of the code. With AOP these concerns can be 
centralized which decreases clutter and code scattering.

### What is a pointcut, a join point, an advice, an aspect, weaving?
- Pointcut: a predicate that matches join points. Advice is associated with a pointcut expression and runs at any join 
point matched by the pointcut (for example, the execution of a method with a certain name). The concept of join points 
as matched by pointcut expressions is central to AOP, and Spring uses the AspectJ pointcut expression language by default.

- Join point: a point during the execution of a program, such as the execution of a method or the handling of an exception. 
In Spring AOP, a join point always represents a method execution.
              
- Advice: action taken by an aspect at a particular join point. Different types of advice include "around," "before" and 
"after" advice. Many AOP frameworks, including Spring, model an advice as an interceptor, maintaining a chain of 
interceptors around the join point.

- Aspect:  a modularization of a concern that cuts across multiple classes. Transaction management is a good example of 
a crosscutting concern in J2EE applications. In Spring AOP, aspects are implemented using regular classes 
(the schema-based approach) or regular classes annotated with the `@Aspect` annotation (the `@AspectJ` style).

- Weaving: linking aspects with other application types or objects to create an advised object. This can be done at 
compile time (using the AspectJ compiler, for example), load time, or at runtime. Spring AOP, like other pure Java AOP 
frameworks, performs weaving at runtime.

### How does Spring solve (implement) a cross cutting concern?
Spring uses proxies to add cross cutting concern logic to a bean in a dynamic way.

### Which are the limitations of the two proxy-types?
- JDK dynamic proxies:
    - Can only proxy objects which implement at least one interface and only the methods 
    defined by the implemented interfaces.
- CGLIB:
    - final methods cannot be advised, as they cannot be overriden.
    - You will need the CGLIB 2 binaries on your classpath, whereas dynamic proxies are available with the JDK. Spring will automatically warn you when it needs CGLIB and the CGLIB library classes are not found on the classpath.
    - The constructor of your proxied object will be called twice. This is a natural consequence of the CGLIB proxy model whereby a subclass is generated for each proxied object. For each proxied instance, two objects are created: the actual proxied object and an instance of the subclass that implements the advice. This behavior is not exhibited when using JDK proxies. Usually, calling the constructor of the proxied type twice, is not an issue, as there are usually only assignments taking place and no real logic is implemented in the constructor.

### How many advice types does Spring support. What are they used for?
- **before**: Run advice before the method execution. 	
- **after**: Run advice after the method execution, regardless of its outcome.
- **after**-returning: Run advice after the method execution, only if the method completes successfully.	
- **after-throwing**: Run advice after the method execution, only if the method exits by throwing an exception.
- **around**: Run advice before and after the advised method is invoked.

### What do you have to do to enable the detection of the `@Aspect` annotation?
Add _aspectjweaver.jar_ and _aspectjrt.jar_ to the classpath.

**xml:** add `<aop:aspectj-autoproxy />` to configuration
**java:** add `@AspectJAutoProxy` to configuration

### Name three typical cross cutting concerns.
- Transactions
- Security
- Logging
- Caching

### What two problems arise if you don't solve a cross cutting concern via AOP?
- **Code clutter**: Code for the crosscutting concerns clutters each method where that logic is needed.
- **Code scattering**: Code for the crosscutting concerns is scattered over many methods. When a change has to be made 
to the logic, it might have to be changed over many methods.

### What does `@EnableAspectJAutoProxy` do?
Enables support for handling components marked with AspectJ's `@Aspect` annotation, similar to functionality found in 
Spring's `<aop:aspectj-autoproxy>` XML element. To be used on @Configuration classes.

### What is a named pointcut?
A named pointcut can be referenced at multiple locations to prevent repeating the same pointcut logic. Named pointcuts 
are DRY. 

**xml example**: 
```xml
<aop:config>
    <aop:pointcut id="setterMethods" expression="execution(void set*(*))"/>
    <aop:aspect ref="beanToAdvice">
        <aop:after-returning pointcut-ref="setterMethods" method="trackChange"/>
        <aop:after-throwing pointcut-ref="setterMethods" method="logFailure"/>
    </aop:aspect>
</aop:config> 
```
**Java example:**
```java
package com.xyz.someapp;

@Aspect
public class SystemArchitecture {
    @Pointcut("execution(void set*(*))")
    public void setterMethods() {} 
}
```
```java
@Before("com.xyz.someapp.SystemArchitecture.setterMethods()")
  public void doAccessCheck() {
    // ...
  }
```

### How do you externalize pointcuts? What is the advantage of doing this?

### What is the JoinPoint argument used for?

### What is a ProceedingJoinPoint?

### What are the five advice types called?
- **before**: Run advice before the method execution. 	
- **after**: Run advice after the method execution, regardless of its outcome.
- **after**-returning: Run advice after the method execution, only if the method completes successfully.	
- **after-throwing**: Run advice after the method execution, only if the method exits by throwing an exception.
- **around**: Run advice before and after the advised method is invoked.

### Which advice do you have to use if you would like to try and catch exceptions?
after-throwing

### What is the difference between `@EnableAspectJAutoProxy` and `<aop:aspectj-autoproxy>`?

## Data Management: JDBC, Transactions, JPA, Spring Data

### What is the difference between checked and unchecked exceptions?
Checked exceptions must be caught or explicitly passed on by the caller, while unchecked exceptions don't have this 
restriction but can at the same time be more of a silent killer.

### Why do we (in Spring) prefer unchecked exceptions?
Checked exceptions are a form of tight-coupling.

### What is the data access exception hierarchy?

### How do you configure a DataSource in Spring? Which bean is very useful for development/test databases?

### What is the Template design pattern and what is the JDBC template?

### What is a callback? What are the three JdbcTemplate callback interfaces described in the notes? What are they used for?
_(You would not have to remember the interface names in the exam, but you should know what they do if you see them in a code sample)._

### Can you execute a plain SQL statement with the JDBC template?

### Does the JDBC template acquire (and release) a connection for every method called or once per template?

### Is the JDBC template able to participate in an existing transaction?

### What is a transaction? What is the difference between a local and a global transaction?

### Is a transaction a cross cutting concern? How is it implemented in Spring?

### How are you going to set up a transaction in Spring?

### What does `@Transactional` do? 

### What is the PlatformTransactionManager?

### What is the TransactionTemplate? Why would you use it?

### What is a transaction isolation level? How many do we have and how are they ordered?

### What is the difference between `@EnableTransactionManagement` and `<tx:annotation-driven>`?

### How does the JdbcTemplate support generic queries? How does it return objects and lists/maps of objects?

### What does transaction propagation mean?

### What happens if one `@Transactional` annotated method is calling another `@Transactional` annotated method on the same object instance?

### Where can the `@Transactional` annotation be used? What is a typical usage if you put it at class level?

### What does declarative transaction management mean?

### What is the default rollback policy? How can you override it?

### What is the default rollback policy in a JUnit test, when you use the SpringJUnit4ClassRunner and annotate your `@Test` annotated method with `@Transactional`?

### Why is the term "unit of work" so important and why does JDBC AutoCommit violate this pattern?

### What does JPA mean - what is ORM? What is the idea behind an ORM?

### What is a PersistenceContext and what is an EntityManager. What is the relationship between both?

### Why do you need the `@Entity` annotation. Where can it be placed?

### What do you need to do in Spring if you would like to work with JPA?

### Are you able to participate in a given transaction in Spring while working with JPA?

### What does `@PersistenceContext` do?

### What are disadvantages of ORM? What are the benefits?

### What is an "instant repository"? (hint:recall Spring Data)

### How do you define an “instant” repository?

### What is `@Query` used for?

## Spring MVC and the Web Layer

### MVC is an abbreviation for a design pattern. What does it stand for and what is the idea behind it?

### Do you need spring-mvc.jar in your classpath or is it part of spring-core?

### What is the DispatcherServlet and what is it used for?

### Is the DispatcherServlet instantiated via an application context?

### What is the root application context? How is it loaded?

### What is the `@Controller` annotation used for? How can you create a controller without an annotation?

### What is the ContextLoaderListener and what does it do?

### What are you going to do in the web.xml. Where do you place it?

### How is an incoming request mapped to a controller and mapped to a method?

### What is the `@RequestParam` used for?

### What are the differences between `@RequestParam` and `@PathVariable`?

### What are some of the valid return types of a controller method?

### What is a View and what's the idea behind supporting different types of View?

### How is the right View chosen when it comes to the rendering phase?

### What is the Model?

### Why do you have access to the model in your View? Where does it come from?

### What is the purpose of the session scope?

### What is the default scope in the web context?

### Why are controllers testable artifacts?

### What does the InternalResourceViewResolver do?

## Security 

### What is the delegating filter proxy?

### What is the security filter chain?

### In the notes several predefined filters were shown. What do they do and what order do they occur in?

### Are you able to add and/or replace individual filters?

### Is it enough to hide sections of my output (e.g. JSP-Page)?

### Why do you need the intercept-url?

### Why do you need method security? What type of object is typically secured at the method level (think of its purpose not its Java type).

### Is security a cross cutting concern? How is it implemented internally?

### What do `@Secured` and `@RolesAllowed` do? What is the difference between them?

### What is a security context?

### In which order do you have to write multiple intercept-url's?

### How is a Principal defined?

### What is authentication and authorization? Which must come first?

### In which security annotation are you allowed to use SpEL?

### Does Spring Security support password hashing? What is salting?

## REST

### What does REST stand for?

### What is a resource?

### What are safe REST operations?

### What are idempotent operations? Why is idempotency important?

### Is REST scalable and/or interoperable?

### What are the advantages of the RestTemplate?

### Which HTTP methods does REST use?

### What is an HttpMessageConverter?

### Is REST normally stateless?

### What does `@RequestMapping` do?

### Is `@Controller` a stereotype? Is `@RestController` a stereotype?

### What is the difference between `@Controller` and `@RestController`?

### When do you need `@ResponseBody`?

### What does `@PathVariable` do?

### What is the HTTP status return code for a successful DELETE statement?

### What does CRUD mean?

### Is REST secure? What can you do to secure it?

### Where do you need `@EnableWebMVC`?

### Name some common http response codes. When do you need `@ResponseStatus`?

### Does REST work with transport layer security (TLS)?

### Do you need Spring MVC in your classpath?

## Spring Boot

### What is Spring Boot?

### What are the advantages of using Spring Boot?

### Why is it “opinionated”?

### How does it work? How does it know what to configure?

### What things affect what Spring Boot sets up?

### How are properties defined? Where?

### What are the common Spring Boot annotations and configuration properties?

### What is the difference between an embedded container and a WAR?

### What embedded containers does Spring Boot support?

### What does `@EnableAutoConfiguration` do? What about `@SpringBootApplication`?

### What is a Spring Boot starter POM? Why is it useful?

### Spring Boot supports both Java properties and YML files. What do they look like and how do they work?

### Can you control logging with Spring Boot? How?

## Microservices

### What is a microservices architecture?
A microservice architecture is an architectural style that structures an application as a collection of loosely coupled 
services, which implement business capabilities.

### What are the advantages and disadvantages of microservices?
**Advantages:** 
- Possibility to scale individual parts of the application
- Reusability of services for multiple end goals
- Microservices can be individually developed more easily
- Possibility to use a different language and datastore for each service
- Individual parts of the application can have their own release cycle

**Disadvantages:** 
- More overhead per service (memory, network)
- Transactional behavior gets a lot more complicated

### What are the Spring Cloud annotations and configuration we used in the course?
- `@EnableEurekaServer`
- `@EnableDiscoveryClient`
- `@LoadBalanced`

### What Netflix projects were used in the course?
Eureka and Ribbon

### What is Service Discovery? How is this related to Eureka?
Microservices need to be able to find each other on the network to be able to 'talk'. This is done through a discovery 
service. Eureka is a discovery service.

### How do you setup Service Discovery?
- Start a service annotated with `@EnableEurekaServer`. This is the discovery client.
- Now each service can be annotated with `@EnableDiscoveryClient`. This makes them register at the EurekaServer based on 
the `eureka.client.serviceUrl.defaultZone` property which has to be set with the Eureka Server Url.

### How do you access a RESTful microservice?
With the previous setup the `RestTemplate` is able to resolve the URI of a service based on their logical name set in the 
`spring.application.name` property.  
The `RestTemplate` can be annotated with `@LoadBalanced` to automatically use Ribbon for load balancing.