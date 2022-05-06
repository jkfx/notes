此篇指南带你走进创建一个consume一个RESTful web服务应用的步骤。

## What You Will Build

你将使用Spring的`RestTemplate`构建一个应用来获得一个在`https://quoters.apps.pcfone.io/api/random`中随机的Spring Boot引用。

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
- [下载](https://github.com/spring-guides/gs-consuming-rest/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-consuming-rest.git`
- 进入（cd）`gs-consuming-rest/initial`
- 移步到`Fetching a REST Resource`

当你完成以后，你可以对照`gs-consuming-rest/complete`代码检查你的结果。

## Starting with Spring Initializr

你可以使用[预初始化项目](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.5.5&packaging=jar&jvmVersion=11&groupId=com.example&artifactId=consuming-rest&name=consuming-rest&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.consuming-rest&dependencies=web)点击Generate来下载ZIP文件。这一项目被配置为适合本篇指南。

为了手动初始化项目：

1. 浏览[https://start.spring.io/](https://start.spring.io/)。这个服务拉取了你的应用所有需要的依赖并且已经为了做了多数配置。
1. 选择Gradle或Maven与你想使用的语言。本文假设你选择Java。
1. 点击`Dependencies`并选择`Spring Web`。
1. 点击**Generate**。
1. 下载ZIP文件，这是一个为你的选择配置好的网络应用文件。

> 如果你的IDE整合了Spring Initializr，你可以从你的IDE完成此步骤

> 你也可以从GitHub中fork此项目使用你的IDE或者编辑器打开。

## Fetching a REST Resource

随着项目设置完成之后，你可以创建一个单一应用：consumes a RESTful 服务。

一个RESTful服务已经被建立在[https://quoters.apps.pcfone.io/api/random](https://quoters.apps.pcfone.io/api/random)。它随机地抓取关于Spring Boot的引用并且以JSON文档返回。

如果你通过浏览器或curl请求这个URL，你会受到一个JSON文档，如下所示：

```json
{
   type: "success",
   value: {
      id: 10,
      quote: "Really loving Spring Boot, makes stand alone Spring apps easy."
   }
}
```

当通过浏览器或curl抓取，这足够简单但是并没有什么用。

一个非常有用的consume REST web服务方式是以编程方式进行的。为了帮助你完成这个任务，Spring提供了一个方便的模板类，称之为`RestTemplate`。`RestTemplate`使得与大多数RESTful服务的交互成为一个单行的咒语。并且它甚至可以绑定这些数据与自定义域类型（custom domain types）。

首先，你需要创建一个域类（domain class）来包含你需要的数据。如下所示，你可以使用`Quote`类（`src/main/java/com/example/consumingrest/Quote.java`）作为你的域类（domain class）。

```java
package com.example.consumingrest;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public class Quote {

  private String type;
  private Value value;

  public Quote() {
  }

  public String getType() {
    return type;
  }

  public void setType(String type) {
    this.type = type;
  }

  public Value getValue() {
    return value;
  }

  public void setValue(Value value) {
    this.value = value;
  }

  @Override
  public String toString() {
    return "Quote{" +
        "type='" + type + '\'' +
        ", value=" + value +
        '}';
  }
}
```

这个简单的Java类有少量属性以及对应的getter方法。使用来自于Jackson的JSON库的`@JsonIgnoreProperties`注解来表明不在此类型的属性应该被忽略。

为了直接将你的数据和你的自定义类型绑定，你需要指定变量名与从API返回的JSON文档里的key一致。如果你的变量名和JSON文档中的key不一致，你可以使用`@JsonProperty`注解来指定JSON文档中的key。（此篇示例每一个变量名与JSON key一致，所以你不需要在此注解。）

你还需要一个额外的类，嵌入进引用本身内部。`Value`类`src/main/java/com/example/consumingrest/Value.java`如下所示：

```java
package com.example.consumingrest;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public class Value {

  private Long id;
  private String quote;

  public Value() {
  }

  public Long getId() {
    return this.id;
  }

  public String getQuote() {
    return this.quote;
  }

  public void setId(Long id) {
    this.id = id;
  }

  public void setQuote(String quote) {
    this.quote = quote;
  }

  @Override
  public String toString() {
    return "Value{" +
        "id=" + id +
        ", quote='" + quote + '\'' +
        '}';
  }
}
```

这使用了相同的注解但是映射到其它数据字段。

## Finishing the Application

Initializr创建了一个带有`main()`方法的类（`src/main/java/com/example/consumingrest/ConsumingRestApplication.java`），如下所示：

```java
package com.example.consumingrest;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ConsumingRestApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConsumingRestApplication.class, args);
	}

}
```

现在，你需要添加几个其它事物到`ConsumingRestApplication`类，来使其显示来自Spring RESTful源的引用。你需要添加：

- 日志器（logger）：将日志发送输出（在本文是输出到终端）。
- `RestTemplate`使用Jackson库处理输入的数据。
- `CommandLineRunner`用来启动运行`RestTemplate`（获取引语）。

如下展示了完成后的`ConsumingRestApplication`类（`src/main/java/com/example/consumingrest/ConsumingRestApplication.java`）：

```java
package com.example.consumingrest;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class ConsumingRestApplication {

	private static final Logger log = LoggerFactory.getLogger(ConsumingRestApplication.class);

	public static void main(String[] args) {
		SpringApplication.run(ConsumingRestApplication.class, args);
	}

	@Bean
	public RestTemplate restTemplate(RestTemplateBuilder builder) {
		return builder.build();
	}

	@Bean
	public CommandLineRunner run(RestTemplate restTemplate) throws Exception {
		return args -> {
			Quote quote = restTemplate.getForObject(
					"https://quoters.apps.pcfone.io/api/random", Quote.class);
			log.info(quote.toString());
		};
	}
}
```

## Running the Application

你可以在命令行以Gradle或Maven运行应用程序。你也可以构建一个单一可执行的JAR包，其中包含所有必须的依赖、类以及资源并且运行。构建一个可执行jar可以易于传输、版本和部署服务作为应用，贯穿至整个开发周期，穿插于不同环境等等。

如果你使用Gradle，你可以通过使用`./gradlew bootRun`来运行应用程序。或者说，你可以使用`./gradlew build`来构建JAR包，然后运行JAR包，如下命令：

```
java -jar build/libs/gs-consuming-rest-0.1.0.jar
```

如果你使用Maven，你可以通过`./mvnw spring-boot:run`来运行应用程序。或者说，你可以使用`./mvnw clean package`来构建JAR包，通过如下命令执行JAR包。

```
java -jar target/gs-consuming-rest-0.1.0.jar
```

> 此处步骤描述了创建一个可执行JAR包。你也可以[构造一个经典的WAR文件](https://spring.io/guides/gs/convert-jar-to-war/)。

你应该看到类似如下输出的随机的引语：

```
2019-08-22 14:06:46.506  INFO 42940 --- [           main] c.e.c.ConsumingRestApplication           : Quote{type='success', value=Value{id=1, quote='Working with Spring Boot is like pair-programming with the Spring developers.'}}
```

> 如果你看到了`Could not extract response: no suitable HttpMessageConverter found for response type [class com.example.consumingrest.Quote]`错误，有一种可能就是你正在一个无法连接后端服务（发送JSON）的环境。也许你正在一个公司代理中。尝试设置`http.proxyHost`和`http.proxyPort`适合你的环境值的系统变量。