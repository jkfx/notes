本文带你遍历使用Spring创建一个“Hello, World”RESTful网络服务的过程。

## What You Will Build

你将会构建一个接受以`http://localhost:8080/greeting`的HTTP GET请求服务。

它将会以JSON的形式响应问候（greeting）表示，正如下面展示的一样。

```json
{"id":1,"content":"Hello, World!"}
```

在查询字符串里，你可以以`name`为可选参数自定义问候消息，正如下所展示的一样。

```
http://localhost:8080/greeting?name=User
```

`name`参数值重写了`World`的默认值并且被反射进响应中，如下所示。

```json
{"id":1,"content":"Hello, User!"}
```

## What You Need

- 大约15分钟
- 你喜欢的文本编辑器或IDE
- JDK 1.8 以上
- Graddle 4+ 或 Maven 3.2+
- 你也可以直接将代码导入进你的IDE
  - Spring Tool Suite (STS)
  - InteelliJ IDEA

## How to complete this guide

正如Spring大多数指南一样，你可以从头开始并且完成每一个步骤或者你可以绕开你已经熟悉的基本设置。无论哪种方式，你最终都是完成代码。

为了从头开始，移步到`Starting with Spring Initializr`。

为了跳过基础，做如下步骤：
- [下载](https://github.com/spring-guides/gs-rest-service/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-rest-service.git`
- 进入（cd）`gs-rest-service/initial`
- 移步到`Create a resource representation class`

当你完成以后，你可以再次检查你的`gs-rest-service/complete`的代码。

## Starting with Spring Initializr

你可以使用[预初始化项目](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.5.5&packaging=jar&jvmVersion=11&groupId=com.example&artifactId=rest-service&name=rest-service&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.rest-service&dependencies=web)点击Generate来下载ZIP文件。这一项目被配置为适合本篇指南。

为了手动初始化项目：

1. 浏览[https://start.spring.io/](https://start.spring.io/)。这个服务拉取了你的应用所有需要的依赖并且已经为了做了多数配置。
2. 选择Gradle或Maven与你想使用的语言。本文假设你选择Java。
3. 点击**Dependencies**选择**Spring Web**。
4. 点击**Generate**。
5. 下载ZIP文件，这是一个为你的选择配置好的网络应用文件。

> 如果你的IDE整合了Spring Initializr，你可以从你的IDE完成此步骤

> 你也可以从GitHub中fork此项目使用你的IDE或者编辑器打开。

## Create a resource representation Class

现在，你已经设置了项目与构建系统，你可以创建你的网络服务了。

通过思考服务交互开始。

此服务将会处理对于`/greeting`的`GET`请求，以查询字符里可选参数`name`。`GET`请求应该返回一个以JSON形式的问候表示主体的`200 OK`的响应。它应该类似于如下输出：

```json
{
    "id": 1,
    "content": "Hello, World!"
}
```

`id`字段是一个问候的唯一标识符，`contont`是问候的文本表示。

建模问候表示，创建一个资源表示类。为了这么做，提供一个带有字段、构造器、`id`与`contont`数据的访问器的朴素的老Java对象，如下所示（来自`src/main/java/com/example/restservice/Greeting.java`）：

```java
package com.example.restservice;

public class Greeting {

	private final long id;
	private final String content;

	public Greeting(long id, String content) {
		this.id = id;
		this.content = content;
	}

	public long getId() {
		return id;
	}

	public String getContent() {
		return content;
	}
}
```

> 此应用使用[Jackson JSON](https://github.com/FasterXML/jackson)库来自动调度`Greeting`类型的实例进JSON。Jackson默认包含在web启动器。

## Create a Resource Representation Class

在Spring的构造RESTful网路应用的方法中，HTTP请求通过一个控制器进行处理。这一组成部分被`@RestController`注解识别，如下`GreetingController`（来自`src/main/java/com/example/restservice/GreetingController.java`）展示通过返回一个`Greeting`类的新实例来处理`/greeting`的`GET`请求：

```java
package com.example.restservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

	private static final String template = "Hello, %s!";
	private final AtomicLong counter = new AtomicLong();

	@GetMapping("/greeting")
	public Greeting greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
		return new Greeting(counter.incrementAndGet(), String.format(template, name));
	}
}
```

这个控制器是简单明了的，但是仍然有很多事情需要处理。我们将一步步的进行拆解。

`@GetMapping`注解确保了HTTP请求到`/greeting`被映射到`greeting()`方法。

> 对于HTTP动作有很多类似的注解（例如`@PostMapping`用于POST）。他们都来源于`@RequestMapping`注解，使用`@RequestMapping(method=GET)`具有相同的作用。

`@RequestParam`将查询字符中的`name`参数值绑定到`greeting()`方法的`name`参数。如果请求中`name`参数缺失，那么`defaultValue`的值`World`就会被使用。

此方法体的实现了创建并返回一个新建的`Greeting`对象，分别带有`id`和`content`属性，基于计数器`counter`的下一个数值与使用`name`格式化使用问候`template`。

关于传统的MVC控制器与RESTful网络服务控制的一个关键区别就是HTTP响应体被创建的方式。RESTful网络服务控制器填充并返回`Greeting`对象，而不是依赖一个视图（view）技术在服务端将问候数据渲染进HTML。对象数据将会被作为JSON直接写进HTTP响应中。

此代码使用Spring`@RestController`注解，它将一个类标记为一个控制器，每个方法返回一个域对象（domain object）而不是一个视图（view）。它是包含`@Controller`和`@ResponseBody`二者的简写。

`Greeting`对象必须被转换为JSON。得益于Spring的HTTP消息转换器的支持，你不需要手动地做这个转换操作。因为[Jackson 2](https://github.com/FasterXML/jackson)在类路径（class path），Spring的`MappingJackson2HttpMessageConverter`被自动选为转换`Greeting`实例为JSON。

`@SpringBootApplication`是一个添加了如下展示的所有内容的方便注解：
- `@Configuration`：对于应用上下文（context），将类标记为bean定义的来源。
- `@EnableAutoConfiguration`：告诉Spring Boot开始根据类路径设置添加bean，其它bean，以及各种属性设置。例如，如果`spring-webmvc`在类路径中，这一注解将应用程序标记为web应用以及激活关键行为，例如设置`DispatcherServlet`。
- `@ComponentScan`：告诉Spring寻找`com.example`包下其它组件、配置以及服务，使其找到控制器。

`main()`方法使用Spring Boot的`SpringApplication.run()`方法来启动一个应用程序。你是否注意到没有一行的XML？也同样没有`web.xml`文件。这一个应用程序是100%纯Java并且你并没有必须处理配置任何一个plumbing或infrastructure。

## Build an executable JAR

你可以在命令行以Gradle或Maven运行应用程序。你也可以构建一个单一可执行的JAR包，其中包含所有必须的依赖、类以及资源并且运行。构建一个可执行jar可以易于传输、版本和部署服务作为应用，贯穿至整个开发周期，穿插于不同环境等等。

如果你使用Gradle，你可以通过使用`./gradlew bootRun`来运行应用程序。或者说，你可以使用`./gradlew build`来构建JAR包，然后运行JAR包，如下命令：

```
java -jar build/libs/gs-rest-service-0.1.0.jar
```

如果你使用Maven，你可以通过`./mvnw spring-boot:run`来运行应用程序。或者说，你可以使用`./mvnw clean package`来构建JAR包，通过如下命令执行JAR包。

```
java -jar target/gs-rest-service-0.1.0.jar
```

> 此处步骤描述了创建一个可执行JAR包。你也可以[构造一个经典的WAR文件](https://spring.io/guides/gs/convert-jar-to-war/)。

日志输出被输出。该服务应在几秒钟内启动和运行。

## Test the Service

现在服务已经启动，访问`http://localhost:8080/greeting`，你将会看到：

```json
{"id":1,"content":"Hello, World!"}
```

通过访问`http://localhost:8080/greeting?name=User`提供`name`查询字符参数。注意`content`属性的值如何从`Hello World!`改变为`Hello, User!`，如下所示：

```json
{"id":2,"content":"Hello, User!"}
```

这一改变阐述了`@RequestParam`安排进`GreetingController`是如期工作的。`name`参数被给定`World`默认值，但是可以通过查询字符显式地重写。

注意到`id`属性如何从`1`变为`2`的。这证明了在多次请求中你正在工作在相同的`GreetingController`实例并且它的`counter`字段在每次调用中如期被增加。