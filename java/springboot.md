## Spring Boot 打成的 jar 和普通的 jar 有什么区别
Spring Boot 项目最终打包成的 jar 是可执行 jar ，这种 jar 可以直接通过 java -jar xxx.jar 命令来运行，这种 jar 不可以作为普通的 jar 被其他项目依赖，即使依赖了也无法使用其中的类。普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 Spring Boot 打包成的可执行 jar 解压后，在 \BOOT-INF\classes 目录下才是我们的代码，因此无法被直接引用

## SpringBoot如何实现打包
进入项目目录在控制台输入mvn clean package

## spring-boot-starter-parent
我们都知道，新创建一个 Spring Boot 项目，默认都是有 parent 的，这个 parent 就是 spring-boot-starter-parent, 总结就是打包用的

定义了 Java 编译版本为 1.8 
使用 UTF-8 格式编码
继承自 spring-boot-dependencies，这个里边定义了依赖的版本
执行打包操作的配置

## Spring Boot 中如何实现定时任务
在 Spring Boot 中使用定时任务主要有两种不同的方式，一个就是使用 Spring 中的 @Scheduled 注解，另一-个则是使用第三方框架 Quartz

## Spring Boot 中的 starter 到底是什么

## 您使用了哪些 starter maven 依赖项
spring-boot-starter-web 嵌入tomcat和web开发需要servlet与jsp支持
spring-boot-starter-data-jpa 数据库支持
spring-boot-starter-data-redis redis数据库支持
spring-boot-starter-data-solr solr支持

## SpringBoot微服务中如何实现 session 共享 
常见的方案就是 Spring Session + Redis 来实现 session 共享

## SpringBoot性能如何优化
将springboot内置服务器由tomcat设置为undertow
在项目启动时设置JVM初始内存和最大内存相同
如果项目比较大，类比较多，不使用@SpringBootApplication，采用@Compoment指定扫包范围

## spring 的 6个特征
核心技术 ：依赖注入(DI)，AOP，事件(events)，资源，i18n，验证，数据绑定，类型转换，SpEL。
测试 ：模拟对象，TestContext框架，Spring MVC 测试，WebTestClient。
数据访问 ：事务，DAO支持，JDBC，ORM，编组XML。
Web支持 : Spring MVC和Spring WebFlux Web框架。
集成 ：远程处理，JMS，JCA，JMX，电子邮件，任务，调度，缓存。
语言 ：Kotlin，Groovy

Spring Core： 基础,可以说 Spring 其他所有的功能都需要依赖于该类库。主要提供 IOC 依赖
Spring AOP ：提供了面向方面的编程实现
Spring JDBC : Java数据库连接
Spring JMS ：Java消息服务
Spring ORM : 用于支持Hibernate等ORM工具
Spring Web : 为创建Web应用程序提供支持
Spring Test : 提供了对 JUnit 和 TestNG 测试的支持

## IoC
Inverse of Control:控制反转）是一种设计思想，就是 将原本在程序中手动创建对象的控制权，交由Spring框架来管理。
IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象。

## AOP
AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

Spring AOP就是基于动态代理的，如果要代理的对象，实现了某个接口，那么Spring AOP会使用JDK Proxy，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候Spring AOP会使用Cglib 

Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。 Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比Spring AOP 快很多。

## Spring 中的 bean 的作用域有哪些
singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
prototype : 每次请求都会创建一个新的 bean 实例
request : 每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效
session : 每一次HTTP请求都会产生一个新的 bean，该bean仅在当前 HTTP session 内有效


## request, session 
request对象和session对象的最大区别是生命周期

request对象的生命周期是针对一个客户端(说确切点就是一个浏览器应用程序)的一次请求，当请求完毕之后，request里边的内容也将被释放点 。

session可以跨越很多页面, session的生命周期也是针对一个客户端，但是却是在别人设置的会话周期内(一般是20-30分钟)，session里边的内容将一直存在，即便关闭了这个客户端浏览器 session也不一定会马上释放掉的。
