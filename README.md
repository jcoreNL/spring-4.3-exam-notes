# Spring 4.3 Certification Exam Study Notes 

Remember: Unless a question explicitly references Spring Boot you can assume Spring Boot is not involved in any question.

## Index
1. [Container, Dependency, and IOC](#container-dependency-and-ioc)
2. [Aspect oriented programming](#aspect-oriented-programming)
3. [Data Management: JDBC, Transactions, JPA, Spring Data](#data-management-jdbc-transactions-jpa-spring-data)
4. [Spring MVC and the Web Layer](#spring-mvc-and-the-web-layer)
5. [Security](#security)
6. [REST](#rest)
7. [Spring Boot](#spring-boot)
8. [Microservices](#microservices)

## Container, Dependency, and IOC

### What is dependency injection and what are the advantages?
Dependency injection is a technique whereby one object supplies the dependencies of another object. A dependency is an 
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
Spring is able to scan given packages for bean classes annotated with `@Component` or any of the Stereotype annotations and add them to the bean definitions dynamically.

### What are Stereotypes and Meta-Annotations
Stereotypes are annotations denoting the roles of types or methods in the overall architecture (at a conceptual, rather than implementation, level). For instance:
`@Component`, `@Controller`, `@Repository`, `@Service`.

An annotation is termed a meta-annotation if it is used on another annotation, such as the `@Component` being used as a meta-annotation in the `@Controller` annotation:

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // <-- here
public @interface Controller { ... }
```

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
- Advantages:
  - JavaConfig is type safe, errors can be found at compile-time
  - Refactoring is easier because of compile-time checking
  - XML is verbose, so configuration files can quickly grow big (although imports can be used)

- Limitations
  - XML config is completely decoupled from Java, while spring annotations such as @autowired still have a form of coupling
  - TODO: other limitations

### What is the default bean id if you only use `@Bean`?
The name of the method annotated with `@Bean` wil be used as the default id of the bean.

### Can you use `@Bean` together with `@Profile`?
Yes, the `@Profile` annotation can be used on method level to include/exclude a bean based on the active profile.

### What is Spring Expression Language (SpEL for short)?
The Spring Expression Language (SpEL) is a powerful expression language that supports querying and manipulating an object graph at runtime. It can be used with XML or annotation-based Spring configurations.

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
- `${...}` is the property placeholder syntax. It can only be used to dereference properties.
- `#{...}` is SpEL syntax, which is far more capable and complex. It can also handle property placeholders, and a lot more besides.

Property placeholders cannot contain SpEL expressions, but expressions can contain property references: `#{${someProperty} + 2}`

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
- Transaction Management
- Security
- Logging and Tracing
- Caching
- Error Handling
- Performance Monitoring
- Custom Business Rules

### What two problems arise if you don't solve a cross cutting concern via AOP?
- **Code clutter/tangling**: Code for the crosscutting concerns clutters each method where that logic is needed and mixes concerns.
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
Pointcuts can be externalized by using named pointcuts. The advantage of using named pointcuts is that they can be reused:

***XML:***
```xml
<aop:config>
    <aop:pointcut id="namedPointcut" expression="execution(void someMethod())">
    <aop:aspect ref="aspectBean">
        <aop:after-returning pointcut-ref="namedPointcut" method="adviceMethod" />
    </aop:aspect>

    <bean id="aspectBean" class="...">
</aop:config>
```

***Java:***
```java
@Aspect
public class AspectBean {

    // Pointcut methods are not executed, and must be void
    @Pointcut("execution(void someMethod())")
    public void namedPointcut() {}

    @Around("namedPointcut()")
    public void adviceMethod() { ... }
}
```

### What is the JoinPoint argument used for?
The JoinPoint argument is used for retrieving different information about the adviced method (if talking about Spring AOP particularly), and controlling the flow of the adviced method. For example:

```java
@Before("execution(public void com.somepackage.someClass)")
public void performAdvice(JoinPoint jp) {
    System.out.println("Adviced method name: " + jp.getSignature().getName());
}
```

### What is a ProceedingJoinPoint?
A `ProceedingJoinPoint` inherits from `JoinPoint` and adds the `proceed()` method, which can be used in an around advice 
to either proceed or not proceed with the original method call.

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
The data access exception hierarchy uncouples database technology from the business logic by converting specific exceptions 
to more general DataAccessException subclasses which are consistent across all supported Data Access technologies and are unchecked.

### How do you configure a DataSource in Spring? Which bean is very useful for development/test databases?
The `EmbeddedDatabaseBuilder` is ideal for creating an embedded development/test `DataSource`

For xml configuration the `<jdbc:embedded-database>` element can be used.
### What is the Template design pattern and what is the JDBC template?
In the template design pattern, an abstract class exposes defined way(s)/template(s) to execute its methods. It defines the outline or skeleton of an algorithm.

- Leave the details to specific implementations later
- Hides away large amounts of boilerplate code

***Example:***
```java
public abstract class Game {
   abstract void initialize();
   abstract void startPlay();
   abstract void endPlay();

   public final void play(){
      initialize();
      startPlay();
      endPlay();
   }
}
```

### What is a callback? What are the three JdbcTemplate callback interfaces described in the notes? What are they used for?
_(You would not have to remember the interface names in the exam, but you should know what they do if you see them in a code sample)._

### Can you execute a plain SQL statement with the JDBC template?
Yes, SQL queries can be passed to the multiple query methods of the JDBC template.

### Does the JDBC template acquire (and release) a connection for every method called or once per template?
JDBC template acquires and releases a connection for every method called.

### Is the JDBC template able to participate in an existing transaction?
Yes, JDBC automatically participates in a transaction.

### What is a transaction? What is the difference between a local and a global transaction?
- Global Transaction is an application server managed transaction, allowing to work with different transactional resources 
(this might be two different database, database and message queue, etc)
- Local Transaction is resource specific transaction (for example Oracle Transactions) and application server has nothing 
to do with them.

### Is a transaction a cross cutting concern? How is it implemented in Spring?
Yes, transactions are implemented through AOP.

### How are you going to set up a transaction in Spring?
- Declare a `PlatformTransactionManager` bean
- Declare the transactional methods
    - Using Annotations, XML, Programmatic
    - Can mix and match

### What does `@Transactional` do? 
The `@Transactional` annotation marks a method (or all methods if placed on class level) to be run inside a transaction.

- Transaction started before entering the method
- Commit at the end of the method
- Rollback if method throws a RuntimeException

### What is the PlatformTransactionManager?
The `PlatformTransactionManager` is an abstraction layer which hides the implementation details of data access technology 
specific transaction handling.

### What is the TransactionTemplate? Why would you use it?
The TransactionTemlate is a helper class that simplifies programmatic transaction demarcation and transaction exception handling. It uses callback methods for executing some operations in a transaction. That is achieved by anonymous classes build on the TransactionCallback callback interface. 

```java
TransactionTemplate tt = new TransactionTemplate(transactionManager);
tt.execute(new TransactionCallback() {
    public Object doInTransaction(TransactionStatus arg0) {
        // ...
    }
});
```
You may choose to use TransactionTemplate in case you have a small number of transactional methods in your project. 

### What is a transaction isolation level? How many do we have and how are they ordered?
Transaction isolation levels set the access concurrent transactions have to changes by each other.

- `READ_UNCOMMITTED`: allows dirty reads, shows result of other uncommitted units-of-work.
- `READ_COMMITTED` _(default for most databases)_: Only committed information can be accessed.
- `REPEATABLE_READ`: Does not allow dirty reads. Non-repeatable reads are prevented: reading a row twice in one transaction 
will have same result.
- `SERIALIZABLE`: Does not allow dirty reads and non-repeatable reads. Also prevents phantom reads.

### What is the difference between `@EnableTransactionManagement` and `<tx:annotation-driven>`?
Both  @EnableTransactionManagement and `<tx:annotation-driven/>` are responsible for registering the necessary Spring components that power annotation-driven transaction management. Both have the attribute transactionManager / transaction-manager="". The difference is that, if the attribute is not explicitly set, `<tx:annotation-driven/>` is hard-wired to look for a bean _named_ "transactionManager" by default, while the @EnableTransactionManagement is will fall back to a _by-type_ lookup for any PlatformTransactionManager bean in the container.

### How does the JdbcTemplate support generic queries? How does it return objects and lists/maps of objects?

### What does transaction propagation mean?
Transaction propagation is the way transactions act if another transaction has to be started during a transaction. 
The new transaction can either be embedded or the current transaction will be used for this transaction.

### What happens if one `@Transactional` annotated method is calling another `@Transactional` annotated method on the same object instance?
By default the propagation type is `Propagation.REQUIRED`, which uses the current transaction for any new transactions. 
The `Propagation.REQUIRES_NEW` embeds a new transaction by suspending the current transaction and starting a new one.

### Where can the `@Transactional` annotation be used? What is a typical usage if you put it at class level?
The annotation can be used on both method and class level. At class level every method will be transactional. 

A typical usage of putting the `@Transactional` on class level is on a Repository level class in a layered architecture.

### What does declarative transaction management mean?
Declarative transaction management is a model build on AOP. Spring has some transactional aspects that may be used to advice methods for them to work in a transactional manner. Declarative transaction management has the least impact on application code, and hence is most consistent with the ideals of a non-invasive lightweight container.

### What is the default rollback policy? How can you override it?
The default rollback policy is to rollback on runtime exceptions. It can be overridden by setting the `rollbackFor` and 
`noRollbackFor` properties of `@Transactional` with the (un)wanted exception classes to rollback for.

### What is the default rollback policy in a JUnit test, when you use the SpringJUnit4ClassRunner and annotate your `@Test` annotated method with `@Transactional`?
By default the transaction is rolled back after the test is run. No need to clean up the database yourself after testing. 
By adding the `@Commit` annotation to a test this behavior can be overridden.

### Why is the term "unit of work" so important and why does JDBC AutoCommit violate this pattern?
A database transaction is a sequence of actions that are treated as a single unit of work.
A Unit of Work keeps track of everything you do during a business transaction that can affect the database. When you're done, it figures out everything that needs to be done to alter the database as a result of your work. As JDBC AutoCommit will treat each individual SQL statement as a transaction, the statements are not grouped into a single unit of work.

### What does JPA mean - what is ORM? What is the idea behind an ORM?
- **JPA**: Java Persistance API
- **ORM**: Object Relational Mapping

ORM maps domain objects onto relational databases which allows the user to seemingly directly store/retrieve domain 
objects in/from a relational database.

### What is a PersistenceContext and what is an EntityManager. What is the relationship between both?
A `PersistenceContext` is a group of persistent objects in combination with a unit of work. This `PersistenceContext` is 
managed by an `EntityManager`. An instance of `EntityManager` is often bound to a transaction.

### Why do you need the `@Entity` annotation. Where can it be placed?
The `@Entity` annotation marks a POJO as a relational entity which can be mapped and stored/retrieved. 
It is placed on class level.

### What do you need to do in Spring if you would like to work with JPA?
- Define an EntityManagerFactory bean
- Define a DataSource
- Define a Transaction Manager bean
- Define Mapping Metadata
- Define DAOs

### Are you able to participate in a given transaction in Spring while working with JPA?

### What does `@PersistenceContext` do?
- JPA;s equivalent to `@Autowired`
- At runtime the proxy resolves to current `EntityManager` for current transaction in current thread

### What are disadvantages of ORM? What are the benefits?
***Disadvantages***
- Overhead for simple applications
- You need to learn implementation
- The performance is lower
- It's hard to make complex queries

***Benefits***
- Easy mapping object model to data model
- Much less code
- Concurrency support
- Automatic management of cache, connection pool, transactions and keys

### What is an "instant repository"? (hint:recall Spring Data)
Spring Data can automatically create an implementation of a user defined repository instance. CRUD methods are auto-generated. 
Paging, custom queries and sorting are supported out-of-the-box.

### How do you define an “instant” repository?
- Annotate comain class: define keys and enable persistence
- Create an interface which extends from `Repository<T, K>`

### What is `@Query` used for?
`@Query` is used to add custom query methods to an instant repository by specifying a query as annotation argument on a method in the interface:

```java
public interface UserRepository extends JpaRepository<User, Long> {
  @Query("select u from User u where u.emailAddress = ?1")
  User findByEmailAddress(String emailAddress);
}
```

## Spring MVC and the Web Layer

### MVC is an abbreviation for a design pattern. What does it stand for and what is the idea behind it?
The Model, View, Controller architecture splits an application into the domain models, the frontend (view) and the 
controller which links the model and view with business logic.

### Do you need spring-mvc.jar in your classpath or is it part of spring-core?

### What is the DispatcherServlet and what is it used for?
The `DispatcherServlet`:
- handles all incoming request and dispatches them to their respective controllers
- Receives the model and Logical view name from the controller.
- Consults the View Resolver to find the correct View.
- Renders the View with the Model
- Responds with the rendered view to the original request.

### Is the DispatcherServlet instantiated via an application context?
The `DispatcherServlet` is defined by the `WebApplicationInitializer` or a `web.xml` file. It creates a seperate servlet 
application context with a private configuration.

### What is the root application context? How is it loaded?
The root application context can still be loaded through the `ContextLoaderListener` which is shared across servlets.

### What is the `@Controller` annotation used for? How can you create a controller without an annotation?
It indicates that an annotated class is a "Controller" (e.g. a web controller). The `@Controller` annotation itself is annotated with `@Component`, meaning that every controller is a component (allowing for implementation classes to be autodetected through classpath scanning).

Spring 4.0 introduced `@RestController`, a specialized version of the controller which is a convenience annotation that does nothing more than add the `@Controller` and `@ResponseBody` annotations. By annotating the controller class with @RestController annotation, you no longer need to add `@ResponseBody` to all the request mapping methods. The `@ResponseBody` annotation is active by default.

To create a controller without the `@Controller` annotation (and thus without component-scanning); define it as a bean, let it implement the `Controller` interface and override the `handleRequest()` method.

### What is the ContextLoaderListener and what does it do?

### What are you going to do in the web.xml. Where do you place it?

### How is an incoming request mapped to a controller and mapped to a method?

### What is the `@RequestParam` used for?
The @RequestParam annotation binds request parameters to mapping method’s parameters. If the attribute `name` (or its alias `value`) isn't set, the `@RequestParam` will use the name of the parameter:

```java
@RequestMapping(path = "/some-endpoint")
    public String someMethod(@RequestParam String someParam) {
        return someParam;
    }
```

When calling `http://localhost:8080/some-endpoint?someParam=someVal`, someVal is returned. By default, the `required` attribute of the annotation is set to true, leading to an exception being thrown if the parameter is missing in the request. Switch this to false if you prefer a null value if the parameter is not present in the request, or provide a defaultValue(), which implicitly sets this flag to false.

### What are the differences between `@RequestParam` and `@PathVariable`?
- `@PathVariable` indicates that a method parameter should be bound to a URI template variable. Supported for RequestMapping annotated handler methods in Servlet environments.
- `@RequestParam` indicates that a method parameter should be bound to a web request parameter.

***Example***
```java
@RequestMapping("/example/{somePathVar}")
public String getDetails(@PathVariable(value="somePathVar") String pathVar,
    @RequestParam(value="someRequestParam", required=true) String requestParam){
    ...
}
```
Navigating to `http://localhost:8080/example/123?someRequestParam=456` will set `pathVar` to 123 and `requestParam` to 456.

### What are some of the valid return types of a controller method?

### What is a View and what's the idea behind supporting different types of View?

### How is the right View chosen when it comes to the rendering phase?

### What is the Model?

### Why do you have access to the model in your View? Where does it come from?

### What is the purpose of the session scope?

### What is the default scope in the web context?
Singleton.

### Why are controllers testable artifacts?

### What does the InternalResourceViewResolver do?

## Security

### What is the delegating filter proxy?
`DelegatingFilterProxy` is a chain of Spring configured security filters.

### What is the security filter chain?
The instance of the `DelegatingFilterProxy` **must** be called `springSecurityFilterChain`. _(see previous question)_

### In the notes several predefined filters were shown. What do they do and what order do they occur in?
- `SecurityContextPersistenceFilter`: Creates and manages the security context 
- `LogoutFilter`: Handles logout requests by clearing the security context and redirecting to logout success url.
- `UsernamePasswordAuthenticationFilter`: Handles login requests by populating the security context and redirecting to 
either the target url or failure url depending on the Authentication Managers response.
- `ExceptionTranslationFilter`: Handles exceptions thrown further on in the chain by translating them to an action.
- `FilterSecurityInterceptor`: Checks with the access decision manager whether the current resource request is allowed 
and either throws an appropriate exception or returns the resource based on the response.

### Are you able to add and/or replace individual filters?
Yes, filters can be added to the chain either before or after existing filters. They can also be replaced by adding a filter 
which extends the filter to be replaced.

### Is it enough to hide sections of my output (e.g. JSP-Page)?
No. Although Spring Security provides a tag library to hide sections in JSP pages:
```html
 <%@ taglib prefix="sec" uri="http://www.springframework.org/security/tags" %>
 
 <sec:authorize access="hasRole('admin')">
   Welcome, admin
 </sec:authorize>
```
It only provides a form of security in the view layer. To secure the web application on a URL basis, use `authorizeRequests()` and ... in JavaConfig, or `intercept-url` in XML config.

### Why do you need the intercept-url?
The `<intercept-url/>` is used to define the URL for the requests that we want to have some security constraints. This tag has a pattern attribute that accepts either ant style paths or regex for matching the required resources. Access attribute accepts comma-separated roles that will be allowed to access the resource (any match will grant the access).

***XML example:***
```xml
<http use-expressions="true">
    <intercept-url pattern="/admin/**" access="hasRole('admin')"/>
</http>
```

***Similar java config:***
```java
protected void configure(HttpSecurity http) throws Exception {
     http.authorizeRequests()
         .antMatchers("/admin/**").access("hasRole('admin')");
}
```

### Why do you need method security? What type of object is typically secured at the method level (think of its purpose not its Java type).

### Is security a cross cutting concern? How is it implemented internally?

### What do `@Secured` and `@RolesAllowed` do? What is the difference between them?
- `@RolesAllowed` is a JSR-250 annotation which only allows method security based on roles.
- `@Secured` allows for some extra security options on top of role-based security.

### What is a security context?

### In which order do you have to write multiple intercept-url's?
Intercept-URL patterns are always evaluated in the order they are defined. Thus it is important that more specific patterns are defined higher in the list than less specific patterns. 

### How is a Principal defined?

### What is authentication and authorization? Which must come first?
Authentication is checking who you are. Authorization is checking what you can do/see based on who you are.

### In which security annotation are you allowed to use SpEL?
The `@PreAuthorize`, `@PostAuthorize`, `@PreFilter` and `@PostFilter` annotations allows for SpEL arguments.

### Does Spring Security support password hashing? What is salting?

## REST

### What does REST stand for?
Representational state transfer

### What is a resource?
The fundamental concept in any RESTful API is the resource. A resource is an object with a type, associated data, 
relationships to other resources, and a set of methods that operate on it. It is similar to an object instance in an 
object-oriented programming language, with the important difference that only a few standard methods are defined for 
the resource (corresponding to the standard HTTP GET, POST, PUT and DELETE methods), while an object instance typically 
has many methods.

### What are safe REST operations?
Safe methods are HTTP methods that do not modify resources. For instance, using GET or HEAD on a resource URL, should 
NEVER change the resource. However, this is not completely true. It means: it won't change the resource representation. 
It is still possible, that safe methods do change things on a server or resource, but this should not reflect in a 
different representation. 

### What are idempotent operations? Why is idempotency important?
An idempotent HTTP method is a HTTP method that can be called many times without different outcomes. It would not matter 
if the method is called only once, or ten times over. The result should be the same. Again, this only applies to the result, 
not the resource itself. This still can be manipulated (like an update-timestamp, provided this information is not shared 
in the (current) resource representation.

### Is REST scalable and/or interoperable?
Yes, REST is scalable because it is stateless.

### What are the advantages of the RestTemplate?

### Which HTTP methods does REST use?
Method  | Spring RestTemplate’s method
------- | -------
DELETE  | delete(java.lang.String, java.lang.Object...)
GET     | getForObject(java.lang.String, java.lang.Class<T>, java.lang.Object...), getForEntity(java.lang.String, java.lang.Class<T>, java.lang.Object...)
HEAD    | headForHeaders(java.lang.String, java.lang.Object...)
OPTIONS | optionsForAllow(java.lang.String, java.lang.Object...)
POST    | postForLocation(java.lang.String, java.lang.Object, java.lang.Object...), postForObject(java.lang.String, java.lang.Object, java.lang.Class<T>, java.lang.Object...)
PUT     | put(java.lang.String, java.lang.Object, java.lang.Object...)

### What is an HttpMessageConverter?
The `HttpMessageConverter` convert HTTP request/response body data to/from domain objects. Message converters are used to marshall and unmarshall Java Objects to and from JSON, XML, etc – over HTTP

### Is REST normally stateless?
Yes, REST is designed to prevent state keeping on a server.

### What does `@RequestMapping` do?
The @RequestMapping annotation is used for mapping web requests onto specific handler classes and/or handler methods. Since Spring 4.3, several annotations have been composed from the @RequestMapping meta-annotation, such as:

| Composed annotation | Short for |
--- | ---
@GetMapping | @RequestMapping(method = RequestMethod.GET)
@PostMapping | @RequestMapping(method = RequestMethod.POST)
@PutMapping | @RequestMapping(method = RequestMethod.PUT)
@DeleteMapping | @RequestMapping(method = RequestMethod.DELETE)

### Is `@Controller` a stereotype? Is `@RestController` a stereotype?
`@Controller` is a stereotype, but `@RestController` is not, because the `@RestController` implicitly adds the 
`@ResponseBody` annotation to all methods annotated with `@RequestMapping`, while stereotypes should not differ in behavior.

### What is the difference between `@Controller` and `@RestController`?
@RestController is a composed annotation of the @Controller meta-annotaion, which is visible in the source code of the @RestController:

```java
...
@Controller <--- A rest controller is a controller
@ResponseBody <--- A rest controller has a ResponseBody annotation on class level
public @interface RestController { ... }
```

The real difference is caused by the @ResponseBody annotation added to the @RestController:

- If a controller(method) does not have the @ResponseBody annotation, the return object is forwarded to the `ViewResolver`, which will resolve the appropriate view according to the object returned.
- If a controller(method) has the @ResponseBody annotation, Spring loops through all registered `HTTPMessageConverters` seeking the first that fits the given mime type and class, and then uses it for the actual conversion of the object to be returned.

### When do you need `@ResponseBody`?
If the returned value of a controller method is the actual response body instead of a logical view name the `@ResponseBody` 
is needed. This also takes care of automatic marshalling if a converter is available.

### What does `@PathVariable` do?
`@PathVariable` extracts a part of the `@RequestMapping` path as method argument for use in the implemented logic:

```java
@GetMapping(" /user/{userId}")
public String getUser(@PathVariable String userId) { ... }

// The method parameter can have a different name:
@GetMapping(" /user/{someOtherName}")
public String getUser(@PathVariable("someOtherName") String userId) { ... }
```

### What is the HTTP status return code for a successful DELETE statement?
There are no strict rules on which HTTP status code is the correct one for each method. In general, a 2xx (request was successful) is returned. For example:
- 204: No content
- 202: Accepted
- 200: OK

### What does CRUD mean?
Create, Read (Retrieve), Update, Delete (Destroy)

### Is REST secure? What can you do to secure it?
REST is not a specific web service but a design concept (architecture) for managing state information. Therefore, REST by itself does not provide any form of security. To secure a REST service, several security protocols (such as OAuth 2.0 and TLS) are available. On an applicational level, Spring Security can be considered.

### Where do you need `@EnableWebMVC`?

### Name some common http response codes. When do you need `@ResponseStatus`?

### Does REST work with transport layer security (TLS)?
Yes. Transport Layer Security can encrypt the communication to a RESTful Webservice and authenticate the server to a client.

### Do you need Spring MVC in your classpath?
Yes. Spring MVC is the core component for REST support.

## Spring Boot

### What is Spring Boot?
Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run". 
We take an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. 
Most Spring Boot applications need very little Spring configuration.

Features:
- Create stand-alone Spring applications
- Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
- Provide opinionated 'starter' POMs to simplify your Maven configuration
- Automatically configure Spring whenever possible
- Provide production-ready features such as metrics, health checks and externalized configuration
- Absolutely no code generation and no requirement for XML configuration

### What are the advantages of using Spring Boot?
Spring Boot allows for easy/fast startup of application development, because less configuration is required.

### Why is it “opinionated”?
Spring Boot uses sensible defaults (opinions). All defaults can be overridden, however.

### How does it work? How does it know what to configure?
Spring Boot configures it's defaults based on the contents of the classpath and property files.

### What things affect what Spring Boot sets up?
- Classpath contents
- Property files contents

### How are properties defined? Where?
Properties can be defined in `application.properties` or `application.yml` files and profiled property files.

### What are the common Spring Boot annotations and configuration properties?

### What is the difference between an embedded container and a WAR?
A WAR file needs to be run inside an application server while Spring Boot applications can be build with an embedded 
container, which results in a JAR file which can run independently.

### What embedded containers does Spring Boot support?
- Tomcat
- Jetty
- Undertow

### What does `@EnableAutoConfiguration` do? What about `@SpringBootApplication`?
- `@EnableAutoConfiguration` causes Spring Boot to automatically create beans it thinks you need.
- `@SpringBootApplication` is a combination of `@Configuration`, `@ComponentScan` and `EnableAutoConfiguration`. 

### What is a Spring Boot starter POM? Why is it useful?
A Spring Boot starter POM is a predefined POM file with all basic dependencies needed for a particular case. 
This allows users to immediately start with implementing business logic, because Spring Boot automatically configures 
these dependencies.

### Spring Boot supports both Java properties and YML files. What do they look like and how do they work?

### Can you control logging with Spring Boot? How?
The logging level can be set per package through property files.

example: 
```properties
logging.level.org.springframework=DEBUG
logging.level.com.mycompany.mypackage=INFO
```

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