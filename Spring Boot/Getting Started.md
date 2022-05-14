- [1. Introducing Spring Boot](#1-introducing-spring-boot)
- [2. System Requirements](#2-system-requirements)
  - [2.1. Servlet Containers](#21-servlet-containers)
- [3. Installing Spring Boot](#3-installing-spring-boot)
  - [3.1. Installation Instructions for the Java Developer](#31-installation-instructions-for-the-java-developer)
    - [3.1.1. Maven Installation](#311-maven-installation)
    - [3.1.2. Gradle Installation](#312-gradle-installation)
  - [3.2. Installing the Spring Boot CLI](#32-installing-the-spring-boot-cli)
    - [3.2.1. Manual Installation](#321-manual-installation)
    - [3.2.2. Installation with SDKMAN!](#322-installation-with-sdkman)
    - [3.2.3. OSX Homebrew Installation](#323-osx-homebrew-installation)
    - [3.2.4. MacPorts Installation](#324-macports-installation)
    - [3.2.5. Command-line Completion](#325-command-line-completion)
    - [3.2.6. Windows Scoop Installation](#326-windows-scoop-installation)
    - [3.2.7. Quick-start Spring CLI Example](#327-quick-start-spring-cli-example)
- [4. Developing Your First Spring Boot Application](#4-developing-your-first-spring-boot-application)

如果你正开始使用Spring Boot，或一般的 "Spring"，请从阅读本节开始。它回答了 "什么？"、"如何？"和 "为什么？"等基本问题。它包括对Spring Boot的介绍，以及安装说明。然后，我们将引导您构建您的第一个Spring Boot 应用，并在此过程中讨论了一些核心原则。

# 1. Introducing Spring Boot

Spring Boot帮助你创建独立的、基于Spring的生产级应用程序，你可以运行这些应用程序。可以运行。我们对Spring平台和第三方库有自己的看法，这样你就能以最少的麻烦开始工作。大多数Spring Boot应用程序只需要很少的Spring 配置。

你可以使用Spring Boot来创建Java应用程序，可以通过使用`java -jar`或更传统的war部署来启动。我们还提供了一个运行"spring scripts"的命令行工具。

我们的主要目标是。
- 为所有的Spring开发提供一个根本性的更快和更广泛的入门体验。
- 有开箱即用的主见，但当需求开始偏离默认值时，要迅速退出。
- 提供一系列大类项目常见的非功能特性（如嵌入式服务器、安全、度量、健康检查和外部化配置）。
- 绝对没有代码生成，也不需要XML配置。

# 2. System Requirements

Spring Boot 2.6.7需要Java 8，并兼容到Java 17（包括Java 17）。还需要Spring Framework 5.3.19或以上版本。

为以下构建工具提供了明确的构建支持。

| **Build Tool** | **Version**           |
| -------------- | --------------------- |
| Maven          | 3.5+                  |
| Gradle         | 6.8.x, 6.9.x, and 7.x |

## 2.1. Servlet Containers

Spring Boot支持以下嵌入式Servlet容器。

| **Name**     | **Servlet Version** |
| ------------ | ------------------- |
| Tomcat 9.0   | 4.0                 |
| Jetty 9.4    | 3.1                 |
| Jetty 10.0   | 4.0                 |
| Undertow 2.0 | 4.0                 |

你也可以将Spring Boot应用部署到任何兼容servlet 3.1+的容器中。

# 3. Installing Spring Boot

Spring Boot可以使用"经典的"Java开发工具，也可以作为命令行工具安装。无论哪种方式，你都需要[Java SDK v1.8](https://www.java.com/)或更高版本。在你开始之前，你应该使用以下命令检查你当前的Java安装。

```shell
$ java -version
```

如果你是Java开发的新手，或者你想尝试使用Spring Boot，你可能想先试试[Spring Boot CLI](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html#getting-started.installing.cli)（命令行界面）。否则，请继续阅读"经典的"安装说明。

## 3.1. Installation Instructions for the Java Developer

你可以以与任何标准Java库相同的方式使用Spring Boot。要做到这一点，在你的classpath上包括适当的`spring-boot-*.jar`文件。Spring Boot不需要任何特殊的工具集成，所以你可以使用任何IDE或文本编辑器。另外，Spring Boot应用程序没有任何特殊之处，所以你可以像运行其他Java程序一样运行和调试Spring Boot应用程序。

虽然你可以复制Spring Boot的jar包，但我们一般建议你使用支持依赖性管理的构建工具（如Maven或Gradle）。

### 3.1.1. Maven Installation

Spring Boot与Apache Maven 3.3或以上版本兼容。如果你还没有安装Maven，可以按照[maven.apache.org](https://maven.apache.org/)上的说明进行。

> 在许多操作系统上，Maven可以用软件包管理器安装。如果你使用OSX的Homebrew，可以试试`brew install maven`。Ubuntu用户可以运行`sudo apt-get install maven`。使用[Chocolatey](https://chocolatey.org/)的Windows用户可以在elevated (administrator) prompt下运行`choco install maven`。

Spring Boot的依赖项使用`org.springframework.boot`为`groupId`。通常，你的Maven POM文件继承自`spring-boot-starter-parent`项目，并声明一个或多个["Starter"](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters)的依赖。Spring Boot还提供了一个可选的[Maven插件](https://docs.spring.io/spring-boot/docs/current/reference/html/build-tool-plugins.html#build-tool-plugins.maven)来创建可执行的jar。

关于开始使用Spring Boot和Maven的更多细节，可以在Maven插件参考指南的[入门部分](https://docs.spring.io/spring-boot/docs/2.6.7/maven-plugin/reference/htmlsingle/#getting-started)找到。

### 3.1.2. Gradle Installation

Spring Boot与Gradle 6.8、6.9和7.x兼容。如果你还没有安装Gradle，你可以按照[gradle.org](https://gradle.org/)的说明进行操作。

Spring Boot的依赖项可以通过使用`org.springframework.boot`的`group`来声明。一般来说，你的项目会声明对一个或多个["Starter"](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters)的依赖。Spring Boot提供了一个有用的[Gradle插件](https://docs.spring.io/spring-boot/docs/current/reference/html/build-tool-plugins.html#build-tool-plugins.gradle)，可以用来简化依赖性声明和创建可执行的jar。

> **Gradle Wrapper**
> 当你需要构建一个项目时，Gradle Wrapper提供了一种 "获得 "Gradle的好方法。它是一个小脚本和库，你可以在你的代码旁边提交，以引导构建过程。详情见[docs.gradle.org/current/userguide/gradle_wrapper.html](https://docs.gradle.org/current/userguide/gradle_wrapper.html)。

关于开始使用Spring Boot和Gradle的更多细节，可以在Gradle插件参考指南的[入门部分](https://docs.spring.io/spring-boot/docs/2.6.7/gradle-plugin/reference/htmlsingle/#getting-started)找到。

## 3.2. Installing the Spring Boot CLI

Spring Boot CLI（命令行界面）是一个命令行工具，你可以用它来快速建立Spring的原型（prototype）。它允许你运行[Groovy](https://groovy-lang.org/)脚本，这意味着你有一个熟悉的类似Java的语法，而没有那么多的模板代码。

你不需要使用CLI来使用Spring Boot，但它是一种快速的方法，可以在没有IDE的情况下让Spring应用落地。

### 3.2.1. Manual Installation

你可以从Spring软件仓库下载Spring CLI发行版。

- [spring-boot-cli-2.6.7-bin.zip](https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.6.7/spring-boot-cli-2.6.7-bin.zip)
- [spring-boot-cli-2.6.7-bin.tar.gz](https://repo.spring.io/release/org/springframework/boot/spring-boot-cli/2.6.7/spring-boot-cli-2.6.7-bin.tar.gz)

尖端的[快照发行版](https://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/)也是可用的。

下载后，按照解压后的档案中的[INSTALL.txt](https://raw.githubusercontent.com/spring-projects/spring-boot/v2.6.7/spring-boot-project/spring-boot-cli/src/main/content/INSTALL.txt)说明进行操作。总之，在`.zip`文件的`bin/`目录下有一个`spring`脚本（Windows下为`spring.bat`）。或者，你可以使用`java -jar`与`.jar`文件（该脚本帮助你确定classpath设置正确）。

### 3.2.2. Installation with SDKMAN!

SDKMAN! (The Software Development Kit Manager)可用于管理各种二进制SDK的多个版本，包括Groovy和Spring Boot CLI。从[sdkman.io](https://sdkman.io/)获取SDKMAN！，并通过使用以下命令安装Spring Boot。

```shell
$ sdk install springboot
$ spring --version
Spring CLI v2.6.7
```

如果你为CLI开发功能，并希望访问你建立的版本，请使用以下命令。

```shell
$ sdk install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-2.6.7-bin/spring-2.6.7/
$ sdk default springboot dev
$ spring --version
Spring CLI v2.6.7
```

前面的说明安装了一个本地的`spring`实例，称为`dev`实例。它指向你的目标构建位置，所以每次你重建Spring Boot时，`spring`都是最新的。

你可以通过运行以下命令看到它。

```shell
$ sdk ls springboot

================================================================================
Available Springboot Versions
================================================================================
> + dev
* 2.6.7

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```

### 3.2.3. OSX Homebrew Installation

如果你是在Mac上并使用[Homebrew](https://brew.sh/)，你可以通过使用以下命令来安装Spring Boot CLI。

```shell
$ brew tap spring-io/tap
$ brew install spring-boot
```

Homebrew将spring安装到`/usr/local/bin`。

> 如果你没有看到这个formula，你安装的brew可能已经过时了。在这种情况下，运行 brew update 并再试一次。

### 3.2.4. MacPorts Installation

如果你是在Mac上并且使用[MacPorts](https://www.macports.org/)，你可以通过使用以下命令来安装Spring Boot CLI。

```shell
$ sudo port install spring-boot-cli
```

### 3.2.5. Command-line Completion

Spring Boot CLI包括为[BASH](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29)和[zsh](https://en.wikipedia.org/wiki/Z_shell) shells提供命令完成的脚本。你可以在任何一个shell中`source`该脚本（也被命名为`spring`），或者把它放在你个人或system-wide的bash完成初始化中。在Debian系统中，system-wide的脚本在`/shell-completion/bash`中，当一个新的shell启动时，该目录中的所有脚本都会被执行。例如，如果你是通过使用SDKMAN！安装的，要手动运行该脚本，请使用以下命令。

```shell
$ . ~/.sdkman/candidates/springboot/current/shell-completion/bash/spring
$ spring <HIT TAB HERE>
  grab  help  jar  run  test  version
```

> 如果你通过使用Homebrew或MacPorts安装Spring Boot CLI，命令行完成脚本会自动注册到你的shell中。

### 3.2.6. Windows Scoop Installation

如果你是在Windows上并使用[Scoop](https://scoop.sh/)，你可以通过使用以下命令来安装Spring Boot CLI。

```shell
> scoop bucket add extras
> scoop install springboot
```

Scoop将spring安装到`~/scoop/apps/springboot/current/bin`。

> 如果你没有看到应用程序manifest，你安装的 scoop 可能已经过期。在这种情况下，请运行 `scoop update` 并再次尝试。

### 3.2.7. Quick-start Spring CLI Example

你可以使用下面的网络应用程序来测试你的安装。首先，创建一个名为`app.groovy`的文件，如下所示。

```groovy
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```

然后从shell中运行它，如下所示。

```shell
$ spring run app.groovy
```

> 你的应用程序的第一次运行很慢，因为要下载依赖。随后的运行会快得多。

在你喜欢的网络浏览器中打开 localhost:8080。你应该看到以下输出。

```
Hello World!
```

# 4. Developing Your First Spring Boot Application

本节介绍如何开发一个小型的"Hello World!"网络应用，突出Spring Boot的一些关键特性。我们使用Maven来构建这个项目，因为大多数IDE都支持它。

> [spring.io](https://spring.io/)网站上有许多使用Spring Boot的[入门指南](https://spring.io/guides)。如果你需要解决一个特定的问题，请先在那里查看。
> 
> 你可以通过进入[start.spring.io](https://start.spring.io/)并从Dependencies搜索器中选择"Web"启动器来缩短下面的步骤。这样做会生成一个新的项目结构，这样你就可以马上开始编码了。查看[start.spring.io用户指南](https://github.com/spring-io/start.spring.io/blob/main/USING.adoc)以了解更多细节。

在我们开始之前，请打开终端并运行以下命令，以确保你安装了有效的Java和Maven版本。

```shell
$ java -version
java version "1.8.0_331"
Java(TM) SE Runtime Environment (build 1.8.0_331-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.331-b09, mixed mode)
```

```shell
$ mvn -v
Maven home: /opt/apache-maven-3.8.5
Java version: 1.8.0_331, vendor: Oracle Corporation, runtime: /opt/jdk1.8.0/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "5.13.0-40-generic", arch: "amd64", family: "unix"
```

> 这一示例需要在它自己的目录中创建。后面的说明假定你已经创建了一个合适的目录，并且它是你的当前目录。

