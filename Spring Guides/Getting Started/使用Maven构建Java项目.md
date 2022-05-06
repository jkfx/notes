本篇指南带你走进使用Maven构建Java项目的步骤。

## What you’ll build

你将创建一个提供一天中的时间应用并且之后使用Maven构建。

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

为了从头开始，移步到`Set up the project`。

为了跳过基础，做如下步骤：
- [下载](https://github.com/spring-guides/gs-scheduling-tasks/archive/main.zip)并解压这篇指南的原仓库，或者使用GIt克隆`git clone https://github.com/spring-guides/gs-maven.git`
- 进入（cd）`gs-maven/initial`
- 移步到`initial`

当你完成以后，你可以对照`gs-maven/complete`代码检查你的结果。

## Set up the project

首先你需要设置一个为Maven构建的Java项目。关了保持对Maven的关注，目前使得这个项目越简单越好。在你选择的项目文件夹中创建这个结构。

### Create the directory structure

在你选择的项目目录中，创建如下所示的子目录结构，例如，在Linux系统上使用`mkdir -p src/main/java/hello`命令：

```
└── src
    └── main
        └── java
            └── hello
```

在`src/main/java/hello`目录内，你可以创建任意的Java类。为了维护与剩下的指南的一致性，创建如下两个类：`HelloWorld.java`和`Greeter.java`。

`src/main/java/hello/HelloWorld.java`
```java
package hello;

public class HelloWorld {
  public static void main(String[] args) {
    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```

`src/main/java/hello/Greeter.java`
```java
package hello;

public class Greeter {
  public String sayHello() {
    return "Hello world!";
  }
}
```

现在你已经有了准备使用Maven构建的项目了，下一步是安装Maven。

Maven作为一个zip文件在[https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi)是可以下载的。仅需要执行文件，所以寻找apache-maven-{version}-bin.zip或maven-{version}-bin.tar.gz的链接。

一旦你下载zip文件之后，解压缩到你的计算机中。之后将*bin*目录添加到你的系统变量中。

为了测试Maven的安装，从命令行运行`mvn`：

```
mvn -v
```

如果一切正常，你应该看到一些关于Maven安装信息的输出。它类似于如下所示：

```
Apache Maven 3.8.5 (3599d3414f046de2324203b78ddcf9b5e4388aa0)
Maven home: /opt/apache-maven-3.8.5
Java version: 1.8.0_331, vendor: Oracle Corporation, runtime: /opt/jdk1.8.0/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "5.13.0-40-generic", arch: "amd64", family: "unix"
```

> 你可以考虑使用[Maven wrapper](https://github.com/takari/maven-wrapper)来使你的开发者不需要Maven正确的版本，或是根本不需要安装它。从[Spring Initializr](https://start.spring.io/)下载的项目已经包含了wrapper。它显示为一个在你的项目顶层的脚本`mvnw`，可以使用它来代替`mvn`。

## Define a simple Maven build

现在，Maven已被安装好，你需要创建一个Maven项目定义。Maven项目使用名为*pom.xml*的XML文件定义项目。在其它方面，这个文件给定了项目名、版本和它有关于外部库的依赖。

在项目的根目录创建一个名为*pom.xml*的文件并添加如下内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-maven</artifactId>
    <packaging>jar</packaging>
    <version>0.1.0</version>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.4</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>hello.HelloWorld</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

除了可选的`<packaging>`元素之外，这是构建一个Java项目所需的最简单的*pom.xml*文件。它包括项目配置的以下细节：

- `<modelVersion>` POM模型版本（总是4.0.0）
- `<goupId>` 项目所属的组织。通常表现为倒置的域名。
- `<artifactId>` 赋予项目库工件（artifact）的名称（例如，它的JAR或WAR文件的名称）。
- `<version>` 正在构建的项目版本。
- `<packaging>` 项目如何被打包。默认是“jar”，也可以使用“war”。

> 在选择版本管理方案时，Spring推荐[语义版本](https://semver.org/)管理方法。

至此，你已经定义了一个最小但有能力的Maven项目。

## Build Java code

Maven现在已经准备构建项目。你可以现在使用Maven执行几个构建lifecycle goal，包括编译项目源代码的goal、创建一个库包（library package）（例如一个JAR文件），并且安装库到本地Maven依赖仓库。

为了尝试构建，在命令行输入：

```
mvn compile
```

这将会运行Maven，通知它该执行编译（*compile*）goal。当它完成时，你应该在*target/classed*目录找到编译好的*.class*文件。

由于你不太可能想直接发布或处理.class文件，你将可能想要运行打包（*package*）goal：

```
mvn package
```

打包（*package*）goal将编译你的Java代码，运行存在的测试，通过将源代码打包进一个JAR文件并放到*target*结束。JAR文件的名称将基于项目的`<artifactId>`和`<version>`。例如，之前给定的最小的*pom.xml*文件，JAR文件将被命名为*gs-maven-0.1.0.jar*。

为了执行JAR文件，运行：

```
java -jar target/gs-maven-0.1.0.jar
```

> 如果你把`<packaging>`的值从 "jar"改为 "war"，结果将是*target*目录下的WAR文件而不是JAR文件。

为了快速访问项目依赖，Maven在你本地机器上（通常是在你的home目录的*.m2/repository*目录）维护了一个依赖仓库。如果你想安装你的项目JAR文件到本地仓库，你应该调用`install`goal：

```
mvn install
```

安装（*install*）goal将编译、测试并且打包你的项目代码并复制它到本地依赖库，以备为其他项目引用作为依赖。

## Declare Dependencies

简单的Hello World样例是完全独立的，不依赖任何额外的库。然而，大多数应用程序都依赖外部库来处理常见和复杂的功能。

例如，假设除了打印"Hello World!"之外，你还想让应用程序打印当前的日期和时间。虽然你可以使用本地Java库中的日期和时间工具，但你可以通过使用Joda Time库使事情变得更有趣。

首先，修改HelloWorld.java为：

`src/main/java/hello/HelloWorld.java`
```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
  public static void main(String[] args) {
    LocalTime currentTime = new LocalTime();
    System.out.println("The current local time is: " + currentTime);
    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```

这里`HelloWorld`使用Joda Time的`LocalTime`类来获取和打印当前时间。

如果你现在运行`mvn compile`来构建该项目，构建会失败，因为你没有在构建中声明Joda Time为编译依赖。你可以通过在*pom.xml*中添加以下几行来解决这个问题（在`<project>`元素中）。

```xml
<dependencies>
		<dependency>
			<groupId>joda-time</groupId>
			<artifactId>joda-time</artifactId>
			<version>2.9.2</version>
		</dependency>
</dependencies>
```

这块XML声明了项目的依赖性列表。具体来说，它声明了Joda Time库的一个依赖关系。在`<dependency>`元素中，依赖关系由三个子元素定义。

- `<groupId>` 依赖所属的组织
- `<artifactId>` 所需要的库
- `<version>` 指定所需要的库的版本

默认情况下，所有的依赖都是作为`compile`依赖的范围。也就是说，它们应该在编译时可用（如果你正在构建一个WAR文件，包括在WAR的*/WEB-INF/libs*文件夹中）。此外，你可以指定一个`<scope>`元素来指定以下范围之一：

- `provided` 编译项目代码所需的依赖，但这些依赖性将在运行代码的容器中提供（例如Java Servlet API）。
- `test` 对于编译和运行测试所需要的依赖，但并不在构建或运行项目运行时的代码所需。

现在，如果你运行`mvn compile`或`mvn package`，Maven应从Maven中心仓库解析Joda Time的依赖关系，构建就会成功。

## Write a Test

首先在测试域添加JUnit作为依赖到你的pom.xml文件：

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<scope>test</scope>
</dependency>
```

之后创建一个如下的测试类：

`src/test/java/hello/GreeterTest.java`
```java
package hello;

import static org.hamcrest.CoreMatchers.containsString;
import static org.junit.Assert.*;

import org.junit.Test;

public class GreeterTest {
  
  private Greeter greeter = new Greeter();

  @Test
  public void greeterSaysHello() {
    assertThat(greeter.sayHello(), containsString("Hello"));
  }

}
```

Maven使用一个名为“surefire”的插件运行单元测试。这个插件默认配置编译和运行所有在`src/test/java`下命名匹配`*Test`的类。你可以运行如下命令进行测试：

```
mvn test
```

或是使用前面展示的`mvn install`步骤（有lifecycle定义“test”被包含进“install”的一个步骤）。

这里有一个完整的`pom.xml`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	
	<groupId>org.springframework</groupId>
	<artifactId>gs-maven</artifactId>
	<packaging>jar</packaging>
	<version>0.1.0</version>

	<properties>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>
		<!-- tag::joda[] -->
		<dependency>
			<groupId>joda-time</groupId>
			<artifactId>joda-time</artifactId>
			<version>2.9.2</version>
		</dependency>
		<!-- end::joda[] -->
		<!-- tag::junit[] -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		<!-- end::junit[] -->
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
				<version>3.2.4</version>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
							<transformers>
								<transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
									<mainClass>hello.HelloWorld</mainClass>
								</transformer>
							</transformers>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

</project>
```

> 完整的**pom.xml**文件使用了[Maven Shade插件](https://maven.apache.org/plugins/maven-shade-plugin/)，以达到使JAR文件可执行的简单便利。本指南的重点是开始使用Maven，而不是使用这个特定插件。
