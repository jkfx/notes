> 版本 5.3.19

Spring使得创建Java企业应用非常容易。它提供了一切事物，你只需要在企业环境里拥抱Java语言，支持Groovy和Kotlin作为JVM的替代语言，并且具有灵活性的创建基于应用需求各种类型的架构。截止到Spring框架5.1，Spring需要JDK8+（Java SE 8+）以及对JDK 11 LTS提供了开箱即用的支持。建议将Java SE 8 update 60作为Java 8的最低补丁版本，但是通常推荐使用最近的补丁版本。

Spring支持广泛的应用场景。在多数企业，应用通常存在很长时间并且必须运行在JDK与应用服务器上，其更新周期是开发者无法控制的。其他人也许运行单一的jar在嵌入式服务器中，有可能是在云端环境。但是其他人也许是不需要服务器的独立应用（例如批量或整合工作量（workload））。

Spring是开源的。它有很大的并且活跃的社区，基于真实世界中各式各样的使用案例提供连续的反馈。这帮助Spring在很长一段时间内成功地发展。

## What We Mean by "Spring"

“Spring”术语在不同的上下文代表着不同的事物。它可以用来指代Spring框架项目本身，这是这一切的开始。久而久之，其它的Spring项目构建在Spring框架之上。最常见的是，当人们说到“Spring”的时候，他们指代了整个项目家族。此篇参考文档指的是基层：Spring框架本身。

Spring框架被分为各个模块。应用可以自行选择所需要的模块。其中核心的模块是核心容器（core container），包括了配置模型和依赖注入机制。除此之外，Spring框架对不同的应用架构提供了基础支持，包括messaging、事务性数据（transactional data）和持久化（persistence）、web。它也包含了基于Servlet的Spring MVC的web框架和Spring WebFlux reactive web框架。

一个关于模块的说明：Spring的框架jar允许部署到JDK9的模块路径（Jigsaw）。对于使用Jigsaw的应用，Spring框架5的jar附带了“自动模块名（Automatic-Module-Name）”表明了定义稳定语言层面模块名称入口（“spring.core”、“spring.context”等），这与jar artifact名称（jar以相同的命名模式，以“-”而不是“.”，例如，“spring-core”和“spring-context”）相独立。当然，Spring框架的jar依然可以工作在JDK 8和9+的类路径上。

## History of Spring and the Spring Framework

为了响应早期[J2EE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition)规范的复杂性，Spring在2003年诞生了。尽管有些人认为Java EE与Spring是竞争关系，实际上，Spring是对Java EE的补充。Spring的编程模型并不包含Java EE平台规范；相反，它与精心挑选的EE框架中的个别规范进行了整合。

- Servlet API ([JSR 340](https://jcp.org/en/jsr/detail?id=340))
- WebSocket API ([JSR 356](https://www.jcp.org/en/jsr/detail?id=356))
- Concurrency Utilities ([JSR 236](https://www.jcp.org/en/jsr/detail?id=236))
- JSON Binding API ([JSR 367](https://jcp.org/en/jsr/detail?id=367))
- Bean Validation ([JSR 303](https://jcp.org/en/jsr/detail?id=303))
- JPA ([JSR 338](https://jcp.org/en/jsr/detail?id=338))
- JMS ([JSR 914](https://jcp.org/en/jsr/detail?id=914))
- 如果需要，还有JTA/JCA对事务协调（transaction coordination）的设置

Spring框架也支持依赖注入（[JSR 330](https://www.jcp.org/en/jsr/detail?id=330)）以及常规注解（[JSR 250](https://jcp.org/en/jsr/detail?id=250)）规范，开发人员也许会选择其来使用，而不是由Spring框架提供的SPring特定的机制。

截止到Spring框架5.0，Spring最低版本要求是Java EE 7层面（例如，Servlet 3.1+、JPA 2.1+），尽管在相同时期，在运行时遇到的情况下，提供了以更新的API在Java EE 8层面（例如Servlet 4.0、JSON Binding API）的开箱即用的整合。这仍然使得Spring完全兼容Tomcat 8和9、WebSphere 9以及JBoos EAP 7。

随着时间的推移，Java EE在应用开发中的作用也在不断发展。在Java EE和Spring的早期，应用程序是为了部署到应用服务器上而创建的。今天，在Spring Boot的帮助下，应用程序以一种有利于开发和云计算的方式被创建，Servlet容器被嵌入其中，并且易于改变。从Spring Framework 5开始，WebFlux应用程序甚至不直接使用Servlet API，可以在非Servlet容器的服务器（如Netty）上运行。Spring不断创新，不断发展。除了Spring框架，还有其他项目，如Spring Boot、Spring Security、Spring Data、Spring Cloud、Spring Batch等等。重要的是要记住，每个项目都有自己的源代码库、问题跟踪器和发布节奏。参见[spring.io/projects](https://spring.io/projects)，了解Spring项目的完整列表。

## Design Philosophy

当你了解一个框架时，重要的是不仅要知道它做什么，还要知道它遵循什么原则。下面是Spring框架的指导原则。

- 在每个层面上提供选择。Spring让你尽可能晚地推迟设计决策。例如，你可以在不改变代码的情况下通过配置切换持久化提供者。对于许多其他的基础设施问题和与第三方API的集成也是如此。
- 适应不同的观点。Spring拥有灵活性，对事情应该如何做不持意见。它支持不同角度的广泛的应用需求。
- 保持强大的后向兼容性。Spring的演进是经过精心管理的，在不同的版本之间几乎不存在破坏性的变化。Spring支持一系列精心选择的JDK版本和第三方库，以方便维护依赖Spring的应用程序和库。
- 关心API的设计。Spring团队花了很多心思和时间来制作直观的API，这些API在很多版本和很多年中都能保持良好的效果。
- 为代码质量设定高标准。Spring框架非常强调有意义的、最新的和准确的javadoc。它是为数不多的可以宣称代码结构干净、包与包之间没有循环依赖关系的项目之一。

## Feedback and Contributions

对于如何处理问题或诊断或调试问题，我们建议使用Stack Overflow。点击[这里](https://stackoverflow.com/questions/tagged/spring+or+spring-mvc+or+spring-aop+or+spring-jdbc+or+spring-r2dbc+or+spring-transactions+or+spring-annotations+or+spring-jms+or+spring-el+or+spring-test+or+spring+or+spring-remoting+or+spring-orm+or+spring-jmx+or+spring-cache+or+spring-webflux+or+spring-rsocket?tab=Newest)查看Stack Overflow上建议使用的标签列表。如果你相当确定Spring框架中存在问题，或者想建议一个功能，请使用[GitHub问题](https://github.com/spring-projects/spring-framework/issues)。

如果你有一个解决方案或建议的修复，你可以在[Github](https://github.com/spring-projects/spring-framework)上提交一个pull request。然而，请记住，除了最微不足道的问题，我们希望在issue tracker中提交一个ticket，在那里进行讨论并留下记录供将来参考。

更多的细节，请参见[CONTRIBUTING](https://github.com/spring-projects/spring-framework/tree/main/CONTRIBUTING.md)，顶层项目页面的指导方针。

## Getting Started

如果你刚刚开始使用Spring，你可能想通过创建一个基于[Spring Boot](https://projects.spring.io/spring-boot/)的应用程序来开始使用Spring框架。Spring Boot提供了一种快速（和意见）的方式来创建一个可用于生产的基于Spring的应用程序。它以Spring框架为基础，更倾向于约定俗成的配置，旨在让你尽可能快地启动和运行。

你可以使用[start.spring.io](https://start.spring.io/)来生成一个基本项目，或者按照[ "Getting Started" guides](https://spring.io/guides)来做，比如[开始构建RESTful Web服务](https://spring.io/guides/gs/rest-service/)。这些指南除了更容易消化之外，还非常注重任务，而且大部分都是基于Spring Boot的。它们也涵盖了Spring组合中的其他项目，在解决特定问题时，你可能要考虑这些项目。
