此篇指南将带你遍历在Spring访问关系型数据库的步骤。

## What You Will build

你将使用Spring的`JdbcTemplate`来访问关系型数据库中的数据。

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
- [下载](https://github.com/spring-guides/gs-relational-data-access/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-relational-data-access.git`
- 进入（cd）`gs-relational-data-access/initial`
- 移步到`Create a Customer Object`

当你完成以后，你可以再次检查你的`gs-relational-data-access/complete`的代码。

## Starting with Spring Initializr

你可以使用[预初始化项目](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.5.5&packaging=jar&jvmVersion=11&groupId=com.example&artifactId=relational-data-access&name=relational-data-access&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.relational-data-access&dependencies=jdbc,h2)点击Generate来下载ZIP文件。这一项目被配置为适合本篇指南。

为了手动初始化项目：

1. 浏览[https://start.spring.io/](https://start.spring.io/)。这个服务拉取了你的应用所有需要的依赖并且已经为了做了多数配置。
2. 选择Gradle或Maven与你想使用的语言。本文假设你选择Java。
3. 点击**Dependencies**选择**JDBC API**和**H2 Database**。
4. 点击**Generate**。
5. 下载ZIP文件，这是一个为你的选择配置好的网络应用文件。

> 如果你的IDE整合了Spring Initializr，你可以从你的IDE完成此步骤

> 你也可以从GitHub中fork此项目使用你的IDE或者编辑器打开。

## Create a `Customer` Object

你将以管理客户姓名的工作简单的数据存储逻辑。为了表示应用层面的数据，创建一个`Customer`类，如下所示：

`src/main/java/com/example/relationaldataaccess/Customer.java`
```java
package com.example.relationaldataaccess;

public class Customer {
  private long id;
  private String firstName, lastName;

  public Customer(long id, String firstName, String lastName) {
    this.id = id;
    this.firstName = firstName;
    this.lastName = lastName;
  }

  @Override
  public String toString() {
    return String.format(
        "Customer[id=%d, firstName='%s', lastName='%s']",
        id, firstName, lastName);
  }

  // getters & setters omitted for brevity
}
```

## Store and Retrieve Data

Spring提供了一个模板类称之为`JdbcTemplate`，它可以使得与SQL关系型数据库和JDBC轻松运作。大多数JDBC代码都在资源获取、连接管理、异常处理和与代码所要实现的目标完全无关的常规错误检查深陷泥潭。`JdbcTemplate`为你照顾了所有事情。你所需要做的事情就是关注手头上的任务。如下所示，展示了一个可以在JDBC上存储和检索数据的类：

`src/main/java/com/example/relationaldataaccess/RelationalDataAccessApplication.java`
```java
package com.example.relationaldataaccess;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.jdbc.core.JdbcTemplate;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

@SpringBootApplication
public class RelationalDataAccessApplication implements CommandLineRunner {

  private static final Logger log = LoggerFactory.getLogger(RelationalDataAccessApplication.class);

  public static void main(String args[]) {
    SpringApplication.run(RelationalDataAccessApplication.class, args);
  }

  @Autowired
  JdbcTemplate jdbcTemplate;

  @Override
  public void run(String... strings) throws Exception {

    log.info("Creating tables");

    jdbcTemplate.execute("DROP TABLE customers IF EXISTS");
    jdbcTemplate.execute("CREATE TABLE customers(" +
        "id SERIAL, first_name VARCHAR(255), last_name VARCHAR(255))");

    // Split up the array of whole names into an array of first/last names
    List<Object[]> splitUpNames = Arrays.asList("John Woo", "Jeff Dean", "Josh Bloch", "Josh Long").stream()
        .map(name -> name.split(" "))
        .collect(Collectors.toList());

    // Use a Java 8 stream to print out each tuple of the list
    splitUpNames.forEach(name -> log.info(String.format("Inserting customer record for %s %s", name[0], name[1])));

    // Uses JdbcTemplate's batchUpdate operation to bulk load data
    jdbcTemplate.batchUpdate("INSERT INTO customers(first_name, last_name) VALUES (?,?)", splitUpNames);

    log.info("Querying for customer records where first_name = 'Josh':");
    jdbcTemplate.query(
        "SELECT id, first_name, last_name FROM customers WHERE first_name = ?", new Object[] { "Josh" },
        (rs, rowNum) -> new Customer(rs.getLong("id"), rs.getString("first_name"), rs.getString("last_name"))
    ).forEach(customer -> log.info(customer.toString()));
  }
}
```

`@SpringBootApplication`是一个添加了如下展示的所有内容的方便注解：
- `@Configuration`：对于应用上下文（context），将类标记为bean定义的来源。
- `@EnableAutoConfiguration`：告诉Spring Boot开始根据类路径设置添加bean，其它bean，以及各种属性设置。例如，如果`spring-webmvc`在类路径中，这一注解将应用程序标记为web应用以及激活关键行为，例如设置`DispatcherServlet`。
- `@ComponentScan`：告诉Spring寻找`com.example`包下其它组件、配置以及服务，使其找到控制器。

`main()`方法使用Spring Boot的`SpringApplication.run()`方法来启动一个应用程序。

Spring Boot支持H2（一个内存内的关系数据库引擎）和自动创建连接。因为我们使用`spring-jdbc`，Spring Boot自动创建一个`JdbcTemplate`。`@Autowired JdbcTemplate`字段自动加载它并且使其可用。

`Application`类实现了Spring Boot的`CommandLineRunner`，这意味着在应用上下文（context）被加载之后，它将执行`run()`方法。

首先，通过执行`JdbcTemplate`的`execute`方法安装一些DDL。

其次，通过使用Java 8流，将字符串的列表分割为Java数组的名、姓对。

之后，通过使用`JdbcTemplate`的`batchUpdate`方法插入一些记录到你刚刚新建的表中。方法调用的第一个参数是查询字符。最后一个参数（`Object`实例的数组）将变量替换为查询字符中`?`所在的位置。

> 对于单条插入语句，`JdbcTemplate`的`insert`方法就可以。但是对于多条插入，最好使用`batchUpdate`。

> 通过指示JDBC来绑定变量，使用`?`作为参数可以避免[SQL 注入攻击](https://en.wikipedia.org/wiki/SQL_injection)。

最后，使用`query`方法查询你的表中符合准则的记录。你再次使用了`?`参数来创建用于查询的参数，当你调用时，把实际值传入。最后一个参数是Java 8的Lambda表达式，用于将每一个结果行转换为一个新`Customer`对象。

> Java 8的Lambda可以很好地映射到单一方法接口上，例如SPring的`RowMapper`。如果你是要Java 7或之前的版本，你可以插入一个匿名接口实现，方法体与Lambda表达式体相同。

## Build an executable JAR

你可以在命令行以Gradle或Maven运行应用程序。你也可以构建一个单一可执行的JAR包，其中包含所有必须的依赖、类以及资源并且运行。构建一个可执行jar可以易于传输、版本和部署服务作为应用，贯穿至整个开发周期，穿插于不同环境等等。

如果你使用Gradle，你可以通过使用`./gradlew bootRun`来运行应用程序。或者说，你可以使用`./gradlew build`来构建JAR包，然后运行JAR包，如下命令：

```
java -jar build/libs/gs-relational-data-access-0.1.0.jar
```

如果你使用Maven，你可以通过`./mvnw spring-boot:run`来运行应用程序。或者说，你可以使用`./mvnw clean package`来构建JAR包，通过如下命令执行JAR包。

```
java -jar target/gs-relational-data-access-0.1.0.jar
```

> 此处步骤描述了创建一个可执行JAR包。你也可以[构造一个经典的WAR文件](https://spring.io/guides/gs/convert-jar-to-war/)。

你应该看到如下输出：

```
2022-04-29 18:03:50.562  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : Creating tables
2022-04-29 18:03:50.564  INFO 7018 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2022-04-29 18:03:50.622  INFO 7018 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2022-04-29 18:03:50.634  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : inserting customer record for John Woo
2022-04-29 18:03:50.634  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : inserting customer record for Jeff Dean
2022-04-29 18:03:50.635  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : inserting customer record for Josh Bloch
2022-04-29 18:03:50.635  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : inserting customer record for Josh Long
2022-04-29 18:03:50.674  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : Querying for customer records where first_name = 'Josh':
2022-04-29 18:03:50.682  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : Customer{id=3, firstName='Josh', lastName='Bloch'}
2022-04-29 18:03:50.682  INFO 7018 --- [           main] c.e.r.RelationalDataAccessApplication    : Customer{id=4, firstName='Josh', lastName='Long'}
```
