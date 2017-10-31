# Spring 4.3 Certification Exam Study Notes 

## Container, Dependency, and IOC

### What is dependency injection and what are the advantages?

### What is an interface and what are the advantages of making use of them in Java?

### What is meant by “application-context” and how do you create one?

### What is the concept of a “container” and what is its lifecycle?

### How do you use dependency injection using Java configuration

### How do you use dependency injection in XML, using constructor or setter injection

### How do you use dependency injection using annotations (`@Component`,`@Autowired`)

### What is Component scanning

### What are Stereotypes and Meta-Annotations

### Scopes for Spring beans. What is the default?

### What is an initialization method and how is it declared in a Spring bean?

### What is a destroy method, how is it declared and when is it called?

### What is a BeanFactoryPostProcessor and what is it used for?

### What is a BeanPostProcessor and how is the difference to a BeanFactoryPostProcessor? What do they do? When are they called?

### Are beans lazily or eagerly instantiated by default? How do you alter this behavior?
Beans are eagerly instantiated by default. This behavior can be altered by either setting the `lazy-init="true"` using 
xml configuration or adding the `@Lazy` annotation to a bean definition.

xml example: `<bean id="myBean" class="com.example.MyBean" lazy-init="true"/>`

### What does component-scanning do?

### What is the behavior of the annotation `@Autowired` with regards to field injection, constructor injection and method injection?

### How does the `@Qualifier` annotation complement the use of `@Autowired`?

### What is the role of the `@PostConstruct` and `@PreDestroy` annotations? When will they get called?
A method annotated with `@PostConstruct` will be called directly after all injections have taken place.  
A method annotated with `@PreDestroy` will be called just before the bean lifecycle ends and the bean is destroyed.

### What is a proxy object and what are the two different types of proxies Spring can create?

### What is the power of a proxy object and where are the disadvantages?

### What are the limitations of these proxies (per type)?

### How do you inject scalar/literal values into Spring beans?

### How are you going to create a new instance of an ApplicationContext?

### What is a prefix?

### What is the lifecycle on an ApplicationContext?

### What does the `@Bean` annotation do?

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

### What is a ProperySourcesPlaceholderConfigurer used for?

### What is a namespace used for in XML configuration

### What are the <context/> elements covered in the course and what do they do?

### What is `@Value` used for?

### What is the difference between $ and # in `@Value` expressions?

## Aspect oriented programming

### What is the concept of AOP? Which problem does it solve?

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

### Which are the limitations of the two proxy-types?
- JDK dynamic proxies:
    - Can only proxy objects which implement at least one interface and only the methods 
    defined by the implemented interfaces.
- CGLIB:
    - final methods cannot be advised, as they cannot be overriden.
    - You will need the CGLIB 2 binaries on your classpath, whereas dynamic proxies are available with the JDK. Spring will automatically warn you when it needs CGLIB and the CGLIB library classes are not found on the classpath.
    - The constructor of your proxied object will be called twice. This is a natural consequence of the CGLIB proxy model whereby a subclass is generated for each proxied object. For each proxied instance, two objects are created: the actual proxied object and an instance of the subclass that implements the advice. This behavior is not exhibited when using JDK proxies. Usually, calling the constructor of the proxied type twice, is not an issue, as there are usually only assignments taking place and no real logic is implemented in the constructor.

### How many advice types does Spring support. What are they used for?

### What do you have to do to enable the detection of the `@Aspect` annotation?

### Name three typical cross cutting concerns.
- Transactions
- Security
- Logging
- Caching

### What two problems arise if you don't solve a cross cutting concern via AOP?

### What does `@EnableAspectJAutoProxy` do?

### What is a named pointcut?

### How do you externalize pointcuts? What is the advantage of doing this?

### What is the JoinPoint argument used for?

### What is a ProceedingJoinPoint?

### What are the five advice types called?

### Which advice do you have to use if you would like to try and catch exceptions?

### What is the difference between `@EnableAspectJAutoProxy` and `<aop:aspectj-autoproxy>`?

## Data Management: JDBC, Transactions, JPA, Spring Data

### What is the difference between checked and unchecked exceptions?

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

### What are the advantages and disadvantages of microservices?

### What sub-projects of Spring Cloud did we cover in the course?
_Spring Cloud is a large umbrella project – only what we covered in the course will be tested_

### What are the Spring Cloud annotations and configuration we used in the course?

### What Netflix projects were used in the course?

### What is Service Discovery? How is this related to Eureka?

### How do you setup Service Discovery?

### How do you access a RESTful microservice?