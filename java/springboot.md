#什么是 Spring Boot？

多年来，随着新功能的增加，spring 变得越来越复杂。访问spring官网页面，我们就会看到可以在我们的应用程序中使用的所有 Spring 项目的不同功能。如果必须启动一个新的 Spring 项目，我们必须添加构建路径或添加 Maven 依赖关系，配置应用程序服务器，添加 spring 配置。因此，开始一个新的 spring 项目需要很多努力，因为我们现在必须从头开始做所有事情。

Spring Boot 是解决这个问题的方法。Spring Boot 已经建立在现有 spring 框架之上。使用 spring 启动，我们避免了之前我们必须做的所有样板代码和配置。因此，Spring Boot 可以帮助我们以最少的工作量，更加健壮地使用现有的 Spring功能。

#SpringBoot核心功能
 
* 独立运行Spring项目

>Spring boot 可以以jar包形式独立运行，运行一个Spring Boot项目只需要通过java -jar xx.jar来运行。

* 内嵌servlet容器

>Spring Boot可以选择内嵌Tomcat、jetty或者Undertow,这样我们无须以war包形式部署项目。

* 提供starter简化Maven配置

>Spring提供了一系列的start pom来简化Maven的依赖加载，例如，当你使用了spring-boot-starter-web，会自动加入如图5-1所示的依赖包。


* 自动装配Spring 

>SpringBoot会根据在类路径中的jar包，类、为jar包里面的类自动配置Bean，这样会极大地减少我们要使用的配置。当然，SpringBoot只考虑大多数的开发场景，并不是所有的场景，若在实际开发中我们需要配置Bean，而SpringBoot灭有提供支持，则可以自定义自动配置。

* 准生产的应用监控
 
>SpringBoot提供基于http ssh telnet对运行时的项目进行监控。

* 无代码生产和xml配置　　

>SpringBoot不是借助与代码生成来实现的，而是通过条件注解来实现的，这是Spring4.x提供的新特性。


#SpringBoot优点
1. 快速构建项目。
2. 对主流开发框架的无配置集成。
3. 项目可独立运行，无须外部依赖Servlet容器。
4. 提供运行时的应用监控。
5. 极大的提高了开发、部署效率。
6. 与云计算的天然集成。

