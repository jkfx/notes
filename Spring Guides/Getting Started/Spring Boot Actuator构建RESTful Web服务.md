[Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready)是Spring Boot的一个子项目。它在你的应用程序中增加了几个生产级的服务，而你只需做很少的工作。在本指南中，你将建立一个应用程序，然后看看如何添加这些服务。

## What You Will build

本指南将带领您使用Spring Boot Actuator创建一个"Hello, world "RESTful Web服务。你将建立一个接受以下HTTP GET请求的服务。

```
$ curl http://localhost:9000/hello-world
```

它响应的是以下JSON。

```json
{"id":1,"content":"Hello, World!"}
```

还有许多功能添加到你的应用程序中，以便在生产（或其他）环境中管理服务。你所构建的服务的业务功能与[Building a RESTful Web Service](https://spring.io/guides/gs/rest-service)]中的相同。你不需要使用那个指南来利用这个指南，尽管比较一下结果可能很有趣。

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
- [下载](https://github.com/spring-guides/gs-actuator-service/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-actuator-service.git`
- 进入（cd）`gs-actuator-service/initial`
- 移步到`Create a Representation Class`

当你完成以后，你可以再次检查你的`gs-actuator-service/complete`的代码。

## Starting with Spring Initializr

你可以使用[预初始化项目](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.5.5&packaging=jar&jvmVersion=11&groupId=com.example&artifactId=actuator-service&name=actuator-service&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.actuator-service&dependencies=web,actuator)点击Generate来下载ZIP文件。这一项目被配置为适合本篇指南。

为了手动初始化项目：

1. 浏览[https://start.spring.io/](https://start.spring.io/)。这个服务拉取了你的应用所有需要的依赖并且已经为了做了多数配置。
2. 选择Gradle或Maven与你想使用的语言。本文假设你选择Java。
3. 点击**Dependencies**选择**Spring Web**和**Spring Boot Actuator**。
4. 点击**Generate**。
5. 下载ZIP文件，这是一个为你的选择配置好的网络应用文件。

> 如果你的IDE整合了Spring Initializr，你可以从你的IDE完成此步骤

> 你也可以从GitHub中fork此项目使用你的IDE或者编辑器打开。

## Run the Empty Service

Spring Initializr创建了一个空的应用程序，你可以用它来入门。下面的例子（来自初始目录中的`src/main/java/com/example/actuatorservice/ActuatorServiceApplication`）展示了Spring Initializr创建的类。

```java
package com.example.actuatorservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ActuatorServiceApplication {

  public static void main(String[] args) {
    SpringApplication.run(ActuatorServiceApplication.class, args);
  }

}
```

`@SpringBootApplication`注解根据你的classpath和其他东西的内容，提供了一系列的默认值（如嵌入式servlet容器）。它还开启了Spring MVC的`@EnableWebMvc`注解，激活了Web端点。

在这个应用程序中没有定义端点，但有足够的东西来启动东西，并看到Actuator的一些功能。`SpringApplication.run()`命令知道如何启动Web应用程序。你所需要做的就是运行下面的命令。

```
$ ./gradlew clean build && java -jar build/libs/gs-actuator-service-0.1.0.jar
```

你还没有写任何代码，那么发生了什么？要想知道答案，请等待服务器启动，打开另一个终端，并尝试以下命令（显示其输出）。

```
$ curl localhost:8080
{"timestamp":1384788106983,"error":"Not Found","status":404,"message":""}
```

前面命令的输出表明，服务器正在运行，但你还没有定义任何业务端点。你看到的不是默认的容器生成的HTML错误响应，而是一个来自Actuator `/error`端点的通用JSON响应。你可以从服务器启动时的控制台日志中看到哪些端点是开箱即用的。你可以尝试这些端点中的几个，包括`/health`端点。下面的例子显示了如何做到这一点。

```
$ curl localhost:8080/actuator/health
{"status":"UP"}
```

状态是`UP`，所以执行器（actuator）服务正在运行。

更多细节请参见Spring Boot的[Actuator Project](https://github.com/spring-projects/spring-boot/tree/main/spring-boot-project/spring-boot-actuator)。

## Create a Representation Class

首先，你需要考虑一下你的API会是什么样子。

你想处理对`/hello-world`的GET请求，可以选择使用一个名称查询参数。在响应这样的请求时，你想送回JSON，代表一个问候语，它看起来像下面的内容。

```json
{
    "id": 1,
    "content": "Hello, World!"
}
```

`id`字段是问候语的唯一标识符，`content`包含问候语的文本表述。

为了给问候表示法建模，创建一个表示法类。下面的列表（来自`src/main/java/com/example/actuatorservice/Greeting.java`）显示了问候类。

```java
package com.example.actuatorservice;

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

现在，你需要创建端点控制器，为表示类提供服务。

## Create a Resource Controller

在Spring中，REST端点就是Spring MVC控制器。下面的Spring MVC控制器（来自`src/main/java/com/example/actuatorservice/HelloWorldController.java`）处理对`/hello-world`端点的GET请求并返回`Greeting`资源。

```java
package com.example.actuatorservice;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloWorldController {

  private static final String template = "Hello, %s!";
  private final AtomicLong counter = new AtomicLong();

  @GetMapping("/hello-world")
  @ResponseBody
  public Greeting sayHello(@RequestParam(name="name", required=false, defaultValue="Stranger") String name) {
    return new Greeting(counter.incrementAndGet(), String.format(template, name));
  }

}
```

面向人的控制器和REST端点控制器的关键区别在于如何创建响应。端点控制器不是依靠视图（如JSP）来渲染HTML中的模型数据，而是返回数据，直接写入响应的主体。

`@ResponseBody`注解告诉Spring MVC不要将模型渲染到视图中，而是将返回的对象写入响应体中。它通过使用Spring的一个消息转换器来做到这一点。因为Jackson 2在classpath中，`MappingJackson2HttpMessageConverter`将处理`Greeting`对象到JSON的转换，如果请求头的`Accept`指定JSON应该被返回。

> 你怎么知道Jackson 2在classpath上？运行`mvn dependency:tree`或`./gradlew dependencies`，你会得到一个包括Jackson 2.x的详细依赖树。你也可以看到它来自[/spring-boot-starter-json](https://github.com/spring-projects/spring-boot/tree/main/spring-boot-project/spring-boot-starters/spring-boot-starter-json)，它本身被[spring-boot-starter-web](https://github.com/spring-projects/spring-boot/blob/main/spring-boot-starters/spring-boot-starter-web/pom.xml)导入。

## Run the Application

你可以从一个自定义的主类或直接从一个配置类中运行应用程序。对于这个简单的例子，你可以使用`SpringApplication`辅助类。注意，这是Spring Initializr为你创建的应用类，你甚至不需要修改它就可以为这个简单的应用工作。下面的列表（来自`src/main/java/com/example/actuatorservice/HelloWorldApplication.java`）显示了这个应用类。

```java
package com.example.actuatorservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldApplication {

  public static void main(String[] args) {
    SpringApplication.run(HelloWorldApplication.class, args);
  }

}
```

在传统的Spring MVC应用中，你会添加`@EnableWebMvc`来开启关键行为，包括配置`DispatcherServlet`。但是Spring Boot在检测到classpath上的**spring-webmvc**时，会自动打开这个注解。这为你在接下来的步骤中建立一个控制器做了准备。

`@SpringBootApplication`注解还引入了一个`@ComponentScan`注解，它告诉Spring扫描`com.example.actuatorservice`包中的那些控制器（以及任何其他注解的组件类）。

## Build an executable JAR

你可以在命令行以Gradle或Maven运行应用程序。你也可以构建一个单一可执行的JAR包，其中包含所有必须的依赖、类以及资源并且运行。构建一个可执行jar可以易于传输、版本和部署服务作为应用，贯穿至整个开发周期，穿插于不同环境等等。

如果你使用Gradle，你可以通过使用`./gradlew bootRun`来运行应用程序。或者说，你可以使用`./gradlew build`来构建JAR包，然后运行JAR包，如下命令：

```
java -jar build/libs/gs-actuator-service-0.1.0.jar
```

如果你使用Maven，你可以通过`./mvnw spring-boot:run`来运行应用程序。或者说，你可以使用`./mvnw clean package`来构建JAR包，通过如下命令执行JAR包。

```
java -jar target/gs-actuator-service-0.1.0.jar
```

> 此处步骤描述了创建一个可执行JAR包。你也可以[构造一个经典的WAR文件](https://spring.io/guides/gs/convert-jar-to-war/)。

一旦服务运行（因为你在终端中运行了`spring-boot:run`），你可以在单独的终端中运行以下命令来测试它。

```
$ curl localhost:8080/hello-world
{"id":1,"content":"Hello, Stranger!"}
```

## Switch to a Different Server Port

Spring Boot Actuator的默认运行端口为8080。通过添加`application.properties`文件，你可以覆盖这一设置。下面的列表（来自`src/main/resources/application.properties`）显示了经过必要修改的该文件。

```
server.port: 9000
management.server.port: 9001
management.server.address: 127.0.0.1
```

通过在终端运行以下命令再次运行服务器。

```
$ ./gradlew clean build && java -jar build/libs/gs-actuator-service-0.1.0.jar
```

该服务现在在9000端口启动。

你可以通过在终端运行以下命令来测试它是否在9000端口工作。

```
$ curl localhost:8080/hello-world
curl: (52) Empty reply from server
$ curl localhost:9000/hello-world
{"id":1,"content":"Hello, Stranger!"}
$ curl localhost:9001/actuator/health
{"status":"UP"}
```

## Test Your Application

为了检查你的应用程序是否工作，你应该为你的应用程序编写单元和集成测试。`src/test/java/com/example/actuatorservice/HelloWorldApplicationTests.java`中的测试类可以确保
- 你的控制器是有反应的。
- 你的管理端点是有反应的。

请注意，这些测试在一个随机的端口上启动应用程序。下面的列出显示了测试类。

```java
package com.example.actuatorservice;

import java.util.Map;

import org.junit.jupiter.api.Test;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.TestPropertySource;

import static org.assertj.core.api.BDDAssertions.then;

/**
 * Basic integration tests for service demo application.
 *
 * @author Dave Syer
 */
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@TestPropertySource(properties = {"management.port=0"})
public class HelloWorldApplicationTests {

  @LocalServerPort
  private int port;

  @Value("${local.management.port}")
  private int mgt;

  @Autowired
  private TestRestTemplate testRestTemplate;

  @Test
  public void shouldReturn200WhenSendingRequestToController() throws Exception {
    @SuppressWarnings("rawtypes")
    ResponseEntity<Map> entity = this.testRestTemplate.getForEntity(
        "http://localhost:" + this.port + "/hello-world", Map.class);

    then(entity.getStatusCode()).isEqualTo(HttpStatus.OK);
  }

  @Test
  public void shouldReturn200WhenSendingRequestToManagementEndpoint() throws Exception {
    @SuppressWarnings("rawtypes")
    ResponseEntity<Map> entity = this.testRestTemplate.getForEntity(
        "http://localhost:" + this.mgt + "/actuator", Map.class);
    then(entity.getStatusCode()).isEqualTo(HttpStatus.OK);
  }

}
```
