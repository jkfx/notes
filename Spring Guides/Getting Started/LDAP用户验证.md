此篇指南带你遍历创建一个应用并且使用[Spring Security](https://projects.spring.io/spring-security/) LADP模块加强安全。

## What You Will build

你将构建一个简单的web应用，它由Spring Security的基于Java的嵌入LDAP服务器提供安全保护。你将加载包含一群用户的数据文件LADP服务器。

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

为了从头开始，移步到`Starting with Spring Initializr。

为了跳过基础，做如下步骤：
- [下载](https://github.com/spring-guides/gs-authenticating-ldap/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-authenticating-ldap.git`
- 进入（cd）`gs-authenticating-ldap/initial`
- 移步到`Create a Simple Web Controller`

当你完成以后，你可以再次检查你的`gs-authenticating-ldap/complete`的代码。

## Starting with Spring Initializr

你可以使用[预初始化项目](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.5.5&packaging=jar&jvmVersion=11&groupId=com.example&artifactId=authenticating-ldap&name=authenticating-ldap&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.authenticating-ldap&dependencies=web)点击Generate来下载ZIP文件。这一项目被配置为适合本篇指南。

为了手动初始化项目：

1. 浏览[https://start.spring.io/](https://start.spring.io/)。这个服务拉取了你的应用所有需要的依赖并且已经为了做了多数配置。
2. 选择Gradle或Maven与你想使用的语言。本文假设你选择Java。
3. 点击**Dependencies**选择**Spring Web**。
4. 点击**Generate**。
5. 下载ZIP文件，这是一个为你的选择配置好的网络应用文件。

> 如果你的IDE整合了Spring Initializr，你可以从你的IDE完成此步骤

> 你也可以从GitHub中fork此项目使用你的IDE或者编辑器打开。

## Create a Simple Web Controller

在SPring，REST终端是Spring MVC控制器。如下所示的Spring MVC控制器处理一个`GET /`请求，返回一个简单的消息：

`src/main/java/com/example/authenticatingldap/HomeController.java`
```java
package com.example.authenticatingldap;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HomeController {

  @GetMapping("/")
  public String index() {
    return "Welcome to the home page!";
  }

}
```

整个类由`@RestController`标记，所以Spring MVC可以自动监测控制器（通过使用内建扫描特性）并且自动配置必需的web路径。

`@RestController`也通知Spring MVC直接写入本文到HTTP响应体，因为没有视图（view）。的确，当你访问这个页面，你将在浏览器得到一个简单的消息（因为此篇指南关注于使用LDAP防护页面）。

## Build the Unsecured Web Application

在你防护web应用之前，你应该验证它是可以运作的。为了这么做，你需要定义某些关键bean，你可以通过创建一个`Application`类来这么做。如下所示：

`src/main/java/com/example/authenticatingldap/AuthenticatingLdapApplication.java`
```java
package com.example.authenticatingldap;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class AuthenticatingLdapApplication {

  public static void main(String[] args) {
    SpringApplication.run(AuthenticatingLdapApplication.class, args);
  }

}
```

`@SpringBootApplication`是一个添加了如下展示的所有内容的方便注解：
- `@Configuration`：对于应用上下文（context），将类标记为bean定义的来源。
- `@EnableAutoConfiguration`：告诉Spring Boot开始根据类路径设置添加bean，其它bean，以及各种属性设置。例如，如果`spring-webmvc`在类路径中，这一注解将应用程序标记为web应用以及激活关键行为，例如设置`DispatcherServlet`。
- `@ComponentScan`：告诉Spring寻找`com.example`包下其它组件、配置以及服务，使其找到控制器。

`main()`方法使用Spring Boot的`SpringApplication.run()`方法来启动一个应用程序。你是否注意到没有一行的XML？也同样没有`web.xml`文件。这一个应用程序是100%纯Java并且你并没有必须处理配置任何一个plumbing或infrastructure。

## Build an executable JAR

你可以在命令行以Gradle或Maven运行应用程序。你也可以构建一个单一可执行的JAR包，其中包含所有必须的依赖、类以及资源并且运行。构建一个可执行jar可以易于传输、版本和部署服务作为应用，贯穿至整个开发周期，穿插于不同环境等等。

如果你使用Gradle，你可以通过使用`./gradlew bootRun`来运行应用程序。或者说，你可以使用`./gradlew build`来构建JAR包，然后运行JAR包，如下命令：

```
java -jar build/libs/gs-authenticating-ldap-0.1.0.jar
```

如果你使用Maven，你可以通过`./mvnw spring-boot:run`来运行应用程序。或者说，你可以使用`./mvnw clean package`来构建JAR包，通过如下命令执行JAR包。

```
java -jar target/gs-authenticating-ldap-0.1.0.jar
```

> 此处步骤描述了创建一个可执行JAR包。你也可以[构造一个经典的WAR文件](https://spring.io/guides/gs/convert-jar-to-war/)。


如果你打开你的浏览器访问[http://localhost:8080/](http://localhost:8080/)，你应该看到如下平凡的文本：

```
Welcome to the home page!
```

## Set up Spring Security

为了配置Spring Security，你首先需要添加一些额外的依赖在你的构建中。

对于基于Gradle构建，添加如下依赖到`build.gradle`文件：

```gradle
implementation("org.springframework.boot:spring-boot-starter-security")
implementation("org.springframework.ldap:spring-ldap-core")
implementation("org.springframework.security:spring-security-ldap")
implementation("com.unboundid:unboundid-ldapsdk")
```

> 由于Gradle的一个artifact resolution问题，**spring-tx**必须被引入。否则，Gradle将获取一个无法运作的更老的版本。

对于基于Maven的构建，添加如下依赖到`pom.xml`文件：

```xml
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
		<groupId>org.springframework.ldap</groupId>
		<artifactId>spring-ldap-core</artifactId>
</dependency>
<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-ldap</artifactId>
</dependency>
<dependency>
		<groupId>com.unboundid</groupId>
		<artifactId>unboundid-ldapsdk</artifactId>
</dependency>
```

这些依赖添加了Spring Security和UnboundId，一个开源的LDAP服务器。随着这些依赖的配置，你可以使用纯Java来配置你的安全策略，如下所示：

`src/main/java/com/example/authenticatingldap/WebSecurityConfig.java`
```java
package com.example.authenticatingldap;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http
      .authorizeRequests()
        .anyRequest().fullyAuthenticated()
        .and()
      .formLogin();
  }

  @Override
  public void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
      .ldapAuthentication()
        .userDnPatterns("uid={0},ou=people")
        .groupSearchBase("ou=groups")
        .contextSource()
          .url("ldap://localhost:8389/dc=springframework,dc=org")
          .and()
        .passwordCompare()
          .passwordEncoder(new BCryptPasswordEncoder())
          .passwordAttribute("userPassword");
  }

}
```

你可以使用`WebSecurityConfigurer`来自定义安全设置。在上面的示例中，这是由实现了`WebSecurityConfigurer`接口的`WebSecurityConfigurerAdapter`类，通过重写方法来完成的。

你也需要一个LDAP服务器。对于一个由纯Java编写的嵌入式服务器，Spring Boot提供自动配置，在此篇指南被使用。`ldapAuthentication()`方法配置，由于用户名在登录表单被插入到`{0}`，以至于它在LDAP服务器搜索`uid={0},ou=people,dc=springframework,dc=org`。另外，`passwordCompare()`方法配置编码器和密码属性的名称。

## Set up User Data

LDAP服务器可以使用LDIF（LDAP Data Interchange Format）文件交换用户数据。在`application.properties`内的`spring.ldap.embedded.ldif`属性使得Spring Boot拉入一个LDIF数据文件。这使得其可以轻松预加载演示数据。如下所示了`application.properties`配置项和一个LDIF文件工作样例：

`src/main/resources/application.properties`
```
spring.ldap.embedded.port=8389
spring.ldap.embedded.ldif=classpath:test-server.ldif
spring.ldap.embedded.base-dn=dc=springframework,dc=org
```

`src/main/resources/test-server.ldif`
```
dn: dc=springframework,dc=org
objectclass: top
objectclass: domain
objectclass: extensibleObject
dc: springframework

dn: ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: groups

dn: ou=subgroups,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: subgroups

dn: ou=people,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: people

dn: ou=space cadets,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: space cadets

dn: ou=\"quoted people\",dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: "quoted people"

dn: ou=otherpeople,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: otherpeople

dn: uid=ben,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Ben Alex
sn: Alex
uid: ben
userPassword: $2a$10$c6bSeWPhg06xB1lvmaWNNe4NROmZiSpYhlocU/98HNr2MhIOiSt36

dn: uid=bob,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Bob Hamilton
sn: Hamilton
uid: bob
userPassword: bobspassword

dn: uid=joe,ou=otherpeople,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Joe Smeth
sn: Smeth
uid: joe
userPassword: joespassword

dn: cn=mouse\, jerry,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Mouse, Jerry
sn: Mouse
uid: jerry
userPassword: jerryspassword

dn: cn=slash/guy,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: slash/guy
sn: Slash
uid: slashguy
userPassword: slashguyspassword

dn: cn=quote\"guy,ou=\"quoted people\",dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: quote\"guy
sn: Quote
uid: quoteguy
userPassword: quoteguyspassword

dn: uid=space cadet,ou=space cadets,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Space Cadet
sn: Cadet
uid: space cadet
userPassword: spacecadetspassword



dn: cn=developers,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfUniqueNames
cn: developers
ou: developer
uniqueMember: uid=ben,ou=people,dc=springframework,dc=org
uniqueMember: uid=bob,ou=people,dc=springframework,dc=org

dn: cn=managers,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfUniqueNames
cn: managers
ou: manager
uniqueMember: uid=ben,ou=people,dc=springframework,dc=org
uniqueMember: cn=mouse\, jerry,ou=people,dc=springframework,dc=org

dn: cn=submanagers,ou=subgroups,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfUniqueNames
cn: submanagers
ou: submanager
uniqueMember: uid=ben,ou=people,dc=springframework,dc=org
```

> 对于生产系统，使用LDIF文件并不是标准配置。但是，它对于测试或指导是有用的。

如果你访问[http://localhost:8080/](http://localhost:8080/)，你应该被重定向到一个由Spring Security提供的登录页面。

输入用户名`ben`和密码`benspassword`。你应该看到如下消息：

```
Welcome to the home page!
```
