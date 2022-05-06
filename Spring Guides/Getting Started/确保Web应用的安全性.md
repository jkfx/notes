本指南将引导你完成创建一个简单的Web应用程序的过程，其中的资源受到Spring Security的保护。

## What You Will Build

你将建立一个Spring MVC应用程序，用一个固定的用户列表支持的登录表单来保护页面。

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
- [下载](https://github.com/spring-guides/gs-securing-web/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-securing-web.git`
- 进入（cd）`gs-securing-web/initial`
- 移步到`Create an Unsecured Web Application`

当你完成以后，你可以再次检查你的`gs-securing-web/complete`的代码。

## Starting with Spring Initializr

你可以使用[预初始化项目](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.5.5&packaging=jar&jvmVersion=11&groupId=com.example&artifactId=securing-web&name=securing-web&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.securing-web&dependencies=web,thymeleaf)点击Generate来下载ZIP文件。这一项目被配置为适合本篇指南。

为了手动初始化项目：

1. 浏览[https://start.spring.io/](https://start.spring.io/)。这个服务拉取了你的应用所有需要的依赖并且已经为了做了多数配置。
2. 选择Gradle或Maven与你想使用的语言。本文假设你选择Java。
3. 点击**Dependencies**选择**Spring Web**和**Thymeleaf**。
4. 点击**Generate**。
5. 下载ZIP文件，这是一个为你的选择配置好的网络应用文件。

> 如果你的IDE整合了Spring Initializr，你可以从你的IDE完成此步骤

> 你也可以从GitHub中fork此项目使用你的IDE或者编辑器打开。

## Create an Unsecured Web Application

在你对网络应用程序应用安全之前，你需要一个网络应用程序来保证安全。本节将引导你创建一个简单的Web应用。然后你将在下一节中用Spring Security保护它。

该Web应用程序包括两个简单的视图：一个主页和一个"Hello, World"页面。主页被定义在以下Thymeleaf模板中（来自`src/main/resources/templates/home.html`）。

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="https://www.thymeleaf.org" xmlns:sec="https://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Spring Security Example</title>
    </head>
    <body>
        <h1>Welcome!</h1>
        
        <p>Click <a th:href="@{/hello}">here</a> to see a greeting.</p>
    </body>
</html>
```

这个简单的视图包括一个指向`/hello`页面的链接，它被定义在以下Thymeleaf模板中（来自`src/main/resources/templates/hello.html`）。

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="https://www.thymeleaf.org"
      xmlns:sec="https://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Hello World!</title>
    </head>
    <body>
        <h1>Hello world!</h1>
    </body>
</html>
```

该Web应用程序是基于Spring MVC的。因此，你需要配置Spring MVC并设置视图控制器来暴露这些模板。下面的列表（来自`src/main/java/com/example/securingweb/MvcConfig.java`）显示了一个在应用程序中配置Spring MVC的类。

```java
package com.example.securingweb;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MvcConfig implements WebMvcConfigurer {

	public void addViewControllers(ViewControllerRegistry registry) {
		registry.addViewController("/home").setViewName("home");
		registry.addViewController("/").setViewName("home");
		registry.addViewController("/hello").setViewName("hello");
		registry.addViewController("/login").setViewName("login");
	}

}
```

`addViewControllers()`方法（它覆盖了`WebMvcConfigurer`中的同名方法）添加了四个视图控制器。其中两个视图控制器引用名称为`home`的视图（定义在`home.html`中），另一个引用名为`hello`的视图（定义在`hello.html`中）。第四个视图控制器引用了另一个名为`login`的视图。你将在下一节中创建该视图。

在这一点上，你可以跳到"Run the Application"，无需登录任何东西就可以运行应用程序。

现在你有了一个不安全的网络应用程序，你可以给它增加安全性。

## Set up Spring Security

假设你想防止未经授权的用户查看`/hello`的问候语页面。现在的情况是，如果访问者点击主页上的链接，他们就会看到问候语，而没有任何障碍来阻止他们。你需要添加一个屏障，迫使访问者在看到该页面之前先登录。

你可以通过在应用程序中配置Spring Security来做到这一点。如果Spring Security在classpath上，Spring Boot会[自动用"基本"认证](https://docs.spring.io/spring-boot/docs/2.4.3/reference/htmlsingle/#boot-features-security)来保护所有HTTP端点。但是，你可以进一步定制安全设置。你需要做的第一件事是将Spring Security添加到classpath中。

使用Gradle，你需要在`build.gradle`的`dependencies`闭合中添加两行（一行用于应用程序，另一行用于测试），如下列表所示。

```
implementation 'org.springframework.boot:spring-boot-starter-security'
implementation 'org.springframework.security:spring-security-test'
```

下面的列表显示了完成的`build.gradle`文件。

```
plugins {
	id 'org.springframework.boot' version '2.6.3'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-security'
	implementation 'org.springframework.security:spring-security-test'
	testImplementation('org.springframework.boot:spring-boot-starter-test')
}

test {
	useJUnitPlatform()
}
```

使用Maven，你需要在`pom.xml`中的`<dependencies>`元素中增加两个条目（一个用于应用程序，一个用于测试），如下列表所示。

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-test</artifactId>
  <scope>test</scope>
</dependency>
```

下面的列表显示了完成的`pom.xml`文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>securing-web-complete</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>securing-web-complete</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

下面的安全配置（来自`src/main/java/com/example/securingweb/WebSecurityConfig.java`）确保只有经过认证的用户才能看到秘密问候语。

```java
package com.example.securingweb;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
				.antMatchers("/", "/home").permitAll()
				.anyRequest().authenticated()
				.and()
			.formLogin()
				.loginPage("/login")
				.permitAll()
				.and()
			.logout()
				.permitAll();
	}

	@Bean
	@Override
	public UserDetailsService userDetailsService() {
		UserDetails user =
			 User.withDefaultPasswordEncoder()
				.username("user")
				.password("password")
				.roles("USER")
				.build();

		return new InMemoryUserDetailsManager(user);
	}
}
```

`WebSecurityConfig`类被注解为`@EnableWebSecurity`，以启用Spring Security的Web安全支持并提供Spring MVC集成。它还扩展了`WebSecurityConfigurerAdapter`，并重写了它的几个方法来设置Web安全配置的一些细节。

`configure(HttpSecurity)`方法定义了哪些URL路径应该被保护，哪些不应该。具体来说，`/`和`/home`路径被配置为不需要任何认证。所有其他路径必须经过认证。

当用户成功登录后，他们会被重定向到之前请求的需要认证的页面。有一个自定义的`/login`页面（由`loginPage()`指定），每个人都被允许查看它。

`userDetailsService()`方法为一个用户建立了一个内存中的用户存储。该用户被赋予用户名为`user`，密码为`password`，以及角色为`USER`。

现在你需要创建登录页面。已经有一个登录视图的视图控制器，所以你只需要创建登录视图本身，正如下面的列表（来自`src/main/resources/templates/login.html`）所示。

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="https://www.thymeleaf.org"
      xmlns:sec="https://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Spring Security Example </title>
    </head>
    <body>
        <div th:if="${param.error}">
            Invalid username and password.
        </div>
        <div th:if="${param.logout}">
            You have been logged out.
        </div>
        <form th:action="@{/login}" method="post">
            <div><label> User Name : <input type="text" name="username"/> </label></div>
            <div><label> Password: <input type="password" name="password"/> </label></div>
            <div><input type="submit" value="Sign In"/></div>
        </form>
    </body>
</html>
```

这个Thymeleaf模板展示了一个捕捉用户名和密码并将其post到`/login`的表单。按照配置，Spring Security提供了一个过滤器，拦截该请求并验证用户身份。如果用户认证失败，页面会被重定向到`/login?error`，你的页面会显示适当的错误信息。在成功签出后，你的应用程序被发送到`/login?logout`，你的页面显示适当的成功消息。

最后，你需要为访问者提供一种显示当前用户名和注销的方法。要做到这一点，请更新`hello.html`，向当前用户问好，并包含一个注销表单，正如下面的列表（来自`src/main/resources/templates/hello.html`）所示。

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="https://www.thymeleaf.org"
      xmlns:sec="https://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Hello World!</title>
    </head>
    <body>
        <h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
        <form th:action="@{/logout}" method="post">
            <input type="submit" value="Sign Out"/>
        </form>
    </body>
</html>
```

我们通过使用Spring Security与`HttpServletRequest#getRemoteUser()`的整合来显示用户名。“注销”表单向`/logout`提交一个POST。成功注销后，它将用户重定向到`/login?logout`。

## Run the Application

Spring Initializr为你创建一个应用类。在这种情况下，你不需要修改这个类。下面的列表（来自`src/main/java/com/example/securingweb/SecuringWebApplication.java`）显示了该应用类。

```java
package com.example.securingweb;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SecuringWebApplication {

	public static void main(String[] args) throws Throwable {
		SpringApplication.run(SecuringWebApplication.class, args);
	}

}
```

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

一旦该应用程序启动，将你的浏览器指向`http://localhost:8080`。你应该看到主页，如下图所示。

![image](https://tvax3.sinaimg.cn/large/006VTcCxly1h1xdfopqzhj30ic0cjt9p.jpg)

当你点击这个链接时，它试图把你带到`/hello`的问候页面。然而，由于该页面是安全的，而且你还没有登录，它把你带到了登录页面，如下图所示。

![image](https://tvax1.sinaimg.cn/large/006VTcCxly1h1xdgo9pqtj30ic0cjab1.jpg)

> 如果你用不安全的版本跳到这里，你看不到登录页面。你应该退回并编写其余的基于安全的代码。

在登录页面，通过在用户名和密码字段中分别输入用户和密码，作为测试用户登录。一旦你提交了登录表，你就会被认证，然后被带到问候页面，如下图所示。

![image](https://tvax4.sinaimg.cn/large/006VTcCxly1h1xdhk158dj30ic0codgp.jpg)

如果你点击退出按钮，你的认证就会被撤销，你会被退回到登录页面，并有消息表明你已经退出了。

![image](https://tva1.sinaimg.cn/large/006VTcCxly1h1xdi9i4ifj30qn0d077t.jpg)
