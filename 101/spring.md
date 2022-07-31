# Spring

## Spring Framework
* [Docs](https://docs.spring.io/spring-framework/docs/current/reference/html/)
    * "Provide choice at every level."
    * "Accommodate diverse perspectives. Spring embraces flexibility and is not opinionated about how things should be done."
* IoC
  * Bean
      * xml
      * @Bean config method
      * component-scan (@Component, ...)
   * DI
      * @Autowired (ctor, setter, field)
   * Lifecycle
* AOP
   * Interceptors
   * Proxys (JDK, CGLib)
* "Helpers"
    * TX
    * DAO (z.B. JDBC, JPA)
    * OXM (z.B JAXB)
    * Web (Servlet + MVC)
    * Messaging (z.B. JMS)
    * ...

## Spring Boot
* [Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/)
    * "We take an opinionated view of the Spring platform and third-party libraries, so that you can get started with minimum fuss."
* [Initializr](https://start.spring.io)
* Autoconfig
   * when on classpath
   * customize using properties
* Packaging (z.B. uber-jar)
* Actuator (z.B. Properties, Beans)

## weitere Spring Projekte
* Data
* Security
* Integration
* ...
