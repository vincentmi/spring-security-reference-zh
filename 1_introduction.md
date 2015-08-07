# 简介

##Spring Security是什么?

Spring Security提供了基于Java EE的企业应用软件全面的安全服务。这里特别强调支持使用Spring框架构建的项目，Spring框架是企业软件开发Java EE方案的领导者 . 如果你还没有使用Spring来开发企业应用程序，我们热忱的鼓励你仔细的看一看。熟悉Spring特别是依赖注入原理将帮助你更快更方便的使用Spring Secirity.

人们使用Spring Security的原因有很多，但大部分都发现了Java EE的Servlet规范或EJB规范中的安全功能缺乏典型企业应用场景所需的深度。提到这些规范，重要的是要认识到他们在WAR或EAR级别无法移植。因此如果你更换服务器环境，这里有典型的大量工作去重新配置你的应用程序员安全到新的目标环境。使用Spring Security解决了这些问题，也为你提供许多其他有用的，可定制的安全功能。

正如你可能知道的两个应用程序的两个主要区域是“认证”和“授权”（或者访问控制）。这两个主要区域是Spring Security的两个目标。“认证”，是建立一个他声称的主体的过程（一个“主体”一般是指用户，设备或一些可以在你的应用程序中执行动作的其他系统）。“授权”指确定一个主体是否允许在你的应用程序执行一个动作的过程。为了抵达需要授权的点，主体的身份已经有认证过程建立。这个概念是通用的而不只在Spring Security中。

在身份验证层，Spring Security的支持多种认证模式。这些验证模型绝大多数都是要么由第三方提供，或由相关的标准组织，如互联网工程任务组开发。另外 Spring Security 提供自己的一组认证功能。具体而言，Spring Security目前支持所有这些技术集成的身份验证：

- HTTP BASIC 认证头(基于 IETF RFC-based 标准)
- HTTP Digest 认证头 (基于 IETF RFC-based 标准)
- HTTP X.509 客户端证书交换 (IETF RFC-based 标准)
- LDAP (一个非常常见的方法来跨平台认证需要，尤其是在大型环境)
- Form-based authentication (用于简单的用户界面)
- OpenID 认证
- Authentication based on pre-established request headers (such as Computer - Associates Siteminder)根据预先建立的请求头进行验证
- JA-SIG Central Authentication Service ( CAS, 一个开源的SSO系统)
- Transparent authentication context propagation for Remote Method Invocation (RMI) and HttpInvoker (Spring远程协议)
- Automatic "remember-me" authentication (所以你可以勾选一个框以避免预定的时间段再认证)
- Anonymous authentication (让每一个未经验证的访问自动假设为一个特定的安全标识)
- Run-as authentication (在一个访问应该使用不同的安全标识时非常有用)
- Java Authentication and Authorization Service (JAAS)
- JEE container autentication (所以如果愿意你可以任然使用容器管理的认证s)
- Kerberos
- Java Open Source Single Sign On (JOSSO) *
- OpenNMS Network Management Platform *
- AppFuse *
- AndroMDA *
- Mule ESB *
- Direct Web Request (DWR) *
- Grails *
- Tapestry *
- JTrac *
- Jasypt *
- Roller *
- Elastic Path *
- Atlassian Crowd *
- 你自己的认证 (看下面)

*表示由第三方提供

很多独立软件供应商，因为灵活的身份验证模式而选择Spring Security。这样做允许他们快速的集成到他们的终端客户需求的解决方案而不用进行大量工程或者改变客户的环境。如果上面的验证机制不符合你的需求，Spring Security 是一个开放的平台，要实现你自己的验证机制非常检查。Spring Security的许多企业用户需要与不遵循任何特定安全标准的“遗留”系统集成，Spring Security可以很好的与这类系统集成。

无论何种身份验证机制， Spring Security提供一套的授权功能。这里有三个主要的热点区域，授权web请求、授权方法是否可以被调用和授权访问单个域对象的实例。为了帮助让你分别了解这些差异，认识在Servlet规范网络模式安全的授权功能，EJB容器管理的安全性和文件系统的安全。Spring Security在这些重要的区域提供授权功能，我们将在手册的后面进行介绍。


## 历史
Spring Security以“The Acegi Security System for Spring”的名字始于2013年晚些时候。一个问题提交到Spring开发者的邮件列表，询问是否已经有考虑一个基于Spring的安全性实现。那时候Spring 的社区相对较小（相对现在）。实际上Spring自己在2013年只是一个存在于SourceForge中的项目，这个问题的回答是，这是一个值得研究的领域，虽然目前时间的缺乏阻止了我们对它的探索。

考虑到这一点，一个简单的安全实现建成但是并没有发布。几周后，Spring社区的其他成员询问了安全性，这次这个代码被发送给他们。其他几个请求也跟随而来。到2014年1月大约有20人在使用这个代码。
. These pioneering users were joined by others who suggested a SourceForge project was in order, which was duly established in March 2004.

在早些时候，这个项目没有任何自己的验证模块，身份验证过程依赖于容器管理的安全性和Acegi安全性。而不是专注于授权。开始的时候这很适合，但是越来越多的用户请求额外的容器支持。 the fundamental limitation of container-specific authentication realm interfaces became clear. There was also a related issue of adding new JARs to the container’s classpath, which was a common source of end user confusion and misconfiguration.

Acegi Security-specific authentication services were subsequently introduced. Around a year later, Acegi Security became an official Spring Framework subproject. The 1.0.0 final release was published in May 2006 - after more than two and a half years of active use in numerous production software projects and many hundreds of improvements and community contributions.

Acegi Security became an official Spring Portfolio project towards the end of 2007 and was rebranded as "Spring Security".

Today Spring Security enjoys a strong and active open source community. There are thousands of messages about Spring Security on the support forums. There is an active core of developers who work on the code itself and an active community which also regularly share patches and support their peers.


##发布版本号
了解Spring Security 发布版本号如何工作是很有用的，他可以帮助你识别出工作（或缺乏的功能）涉及到参与迁移到项目的未来版本，每个发布使用3个整数，MAJOR.MINOR.PATCH(主版本.次要版本.补丁版本). 这样做的目的是主版本是不兼容的，API大范围的升级。次要版本应该保留大部分源代码和二进制兼容旧版本的次要版本，认为可能有一些设计变更和不兼容的更新。补丁版本应该向前向后完美兼容.包含一些bug和缺陷修复这些意外的改变.

在某种程度上，你受到变化的影响取决于你的代码是如何紧密集成的。如果你正在做大量定制，你更可能受到比简单的命名空间配置更大的影响。


你应该总是推出一个新版本之前彻底测试你的应用程序。

##获取 Spring Security

你可以通过几种方式获取Spring Security。 你可以从Spring Security页面下载一个分发包。从Maven库下载分离的jar文件。另外你也可以从源代码自己编译。

### 使用Maven

一个最小的Spring Security Maven依赖通常和下面的的类似:

pom.xml. 

```xml
<dependencies>
<!-- ... 其他依赖元素.. -->
<dependency>
	<groupId>org.springframework.security</groupId>
	<artifactId>spring-security-web</artifactId>
	<version>4.0.2.RELEASE</version>
</dependency>
<dependency>
	<groupId>org.springframework.security</groupId>
	<artifactId>spring-security-config</artifactId>
	<version>4.0.2.RELEASE</version>
</dependency>
</dependencies>

```

如果你使用了额外的功能比如 LDAP,OpenID,等等，你需要包含适当的模块，查阅  Section 1.4.3, “Project Modules”.

###Maven 仓库

所有GA发布版本 (版本号以 .RELEASE结尾) 都被部署到Maven Central, 所以不需要在你的 pom里设置额外的库

如果你使用了一个 SNAPSHOT 版本, 你需要确认你设置了Snapshot库，如下:

pom.xml. 

```xml
<repositories>
<!-- ... 其他仓库元素 ... -->
<repository>
	<id>spring-snapshot</id>
	<name>Spring Snapshot Repository</name>
	<url>http://repo.springsource.org/snapshot</url>
</repository>
</repositories>
```

如果你正在使用一个里程碑或者发布候选版本，你需要确保你所定义的Spring里程碑库，如下图所示：

pom.xml. 

```xml
<repositories>
<!-- ... 其他仓库元素 ... -->
<repository>
	<id>spring-milestone</id>
	<name>Spring Milestone Repository</name>
	<url>http://repo.springsource.org/milestone</url>
</repository>
</repositories>
```


##Spring 框架 Bom

Spring Security是针对Spring框架 4.1.6.RELEASE建立的，但是应该快要与4.0.x正常工作。很多用户都会有这个问题，Spring Security传递依赖解析到Spring框架4.1.6.RELEASE这可能会导致奇怪的classpath问题。

规避这个问题的一种方式是在你的pom文件的```<depencyManagement>```部分包含所有的Spring框架模块。另一种方法是像下面这样在```pom.xml```中包含```spring-framework-bom```:

pom.xml. 

```xml
<dependencyManagement>
	<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-framework-bom</artifactId>
		<version>4.1.6.RELEASE</version>
		<type>pom</type>
		<scope>import</scope>
	</dependency>
	</dependencies>
</dependencyManagement>
```

这样将确保Spring Security传递的所有依赖都使用Spring 4.16.RELEASE的模块。

>这种方式使用了Maven的 "bill of materials" (BOM) 概念， Maven 2.0.9+可用.如果想了解Maven如何解析依赖请参考Maven的依赖机制文档的介绍。 
>

###使用Gradle

最小的Gradle一组典型的依赖如下:

build.gradle. 

```gradle
dependencies {
	compile 'org.springframework.security:spring-security-web:4.0.2.RELEASE'
	compile 'org.springframework.security:spring-security-config:4.0.2.RELEASE'
}
```

如果你使用了LDAP,OpenID等等，你需要包含相应的模块，参阅 1.4.3, “Project Modules”.

###Gradle 仓库

所有的GA发布版本（以.RELEASE结尾）都部署到了Maven Central库，所以使用mavenCentral()就足够了。

build.gradle. 

```gradle
repositories {
	mavenCentral()
}
```


如果你正在使用了SNAPSHOT 版本, 你需要确保你使用了Spring的Snapshot库，定义如下:

build.gradle. 

```gradle
repositories {
	maven { url 'https://repo.spring.io/snapshot' }
}
```

如果你正在使用里程碑或者发布候选版。你需要确认你使用了Spring的里程碑仓库，定义如下:

build.gradle. 

```gradle
repositories {
	maven { url 'https://repo.spring.io/milestone' }
}
```

### 使用Spring 4.0.x 和Gradle

解析依赖传递时Gradle默认会使用最新的版本。这意味着当在Spring 4.1.6.RELEASE下使用Spring Security 4.0.2.RELEASE我们不需要做额外的工作。然而有时出现一些问题，最好使用Gradle的ResolutionStrategy 按照下面的方式来处理：

build.gradle. 

```gradle
configurations.all {
	resolutionStrategy.eachDependency { DependencyResolveDetails details ->
		if (details.requested.group == 'org.springframework') {
			details.useVersion '4.1.6.RELEASE'
		}
	}
}
```
这样将确保所有Spring Security传递的依赖使用Spring 4.1.6.RELEASE 的模块.

>这个例子使用Gradle 1.9，但可能需要在gradle的未来版本中进行修改，因为这是在一个Gradle的孵化功能。
>

###项目模块

在Spring Security 3.0,代码库被分割到单独的jar，这样可以更清楚的分隔不同功能区域和第三方依赖。如果你使用Maven构建你的项目，那么这些都是需要你添加到你的pom.xml中的。甚至如果你没有使用Maven,我们建议你请求pom.xml文件来获取第三方依赖和版本。另一个好办法是检查示例应用程序的库。

####核心模块   spring-security-core.jar

包含核心的验证和访问控制类和接口，远程支持和基本的配置API.任何使用Spring Security的应用程序都需要这个模块。支持独立应用程序、远程客户端、服务层方法安全和JDBC用户配置。包含以下顶层包：

```java
 org.springframework.security.core
 org.springframework.security.access
 org.springframework.security.authentication
 org.springframework.security.provisioning
```

####远程调用 - spring-security-remoting.jar

提供与Spring Remoting的集成. 通常你不需要这个模块，除非你正使用Spring Remoting编写远程客户端。主要的包是 ```org.springframework.security.remoting```.

####网页 spring-security-web.jar

包括过滤器和网站安全相关的基础代码。使用Servlet API的任何应用依赖他。如果你需要Spring Security网页验证服务和基于URL的访问控制你需要这个模块。主包名为```org.springframework.security.web```.

####配置 spring-security-config.jar

包含安全命令空间的解析代码。如果你使用Spring Security XML命令空间进行配置你需要包含这个模块。主包名```org.springframework.security.config```. 没有类需要被应用程序直接使用。

####LDAP  spring-security-ldap.jar

LDAP 验证和配置代码. 如果你需要使用LDAP验证和管理LDAP用户实体，你需要这个模块。主包名```org.springframework.security.ldap```.

ACL 访问控制列表- spring-security-acl.jar

ACL专门的领域对象的实现. 用来在你的应用程序中应用安全到特定的领域对象实例。主包名为```org.springframework.security.acls```.

CAS  spring-security-cas.jar

Spring Security的CAS 客户端集成. 如果你想用CAS的SSO服务器使用Spring Security 网页验证 需要该模块。顶部的包是 ```org.springframework.security.cas```.

OpenID   spring-security-openid.jar

OpenID 网页验证支持. 使用外部的OpenID 服务器验证用户. ```org.springframework.security.openid```. 需要 OpenID4Java.

###签出源代码

因为Spring Security是一个开源项目，我们强烈建议您使用Git签出源代码。这可以让你完全访问所有示例应用程序，你可以轻松地构建最先进的最新版本的项目。 有了项目的源代码对debug有非常大的帮助. 异常堆栈跟踪不再是模糊的黑盒问题，你可以沿着他找出到底发生了什么. 源代码是终极文档，通常也是最简答的找出项目实际是如何工作的地方。


要获取项目源代码，请使用以下git命令：

```shell
git clone https://github.com/spring-projects/spring-security.git
```

这可以让你在本地机器上访问到整个项目的历史记录（包括所有版本和分支）。
