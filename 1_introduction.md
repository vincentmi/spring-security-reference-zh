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

It is useful to understand how Spring Security release numbers work, as it will help you identify the effort (or lack thereof) involved in migrating to future releases of the project. Each release uses a standard triplet of integers: MAJOR.MINOR.PATCH. The intent is that MAJOR versions are incompatible, large-scale upgrades of the API. MINOR versions should largely retain source and binary compatibility with older minor versions, thought there may be some design changes and incompatible updates. PATCH level should be perfectly compatible, forwards and backwards, with the possible exception of changes which are to fix bugs and defects.

The extent to which you are affected by changes will depend on how tightly integrated your code is. If you are doing a lot of customization you are more likely to be affected than if you are using a simple namespace configuration.

You should always test your application thoroughly before rolling out a new version.

##获取 Spring Security

你可以通过几种方式获取Spring Security。 你可以从Spring Security页面下载一个分发包。从Maven库下载分离的jar文件。另外你也可以从源代码自己编译。

### 使用Maven

一个最小的Spring Security Maven依赖通常和下面的的类似:

pom.xml. 

```xml
<dependencies>
<!-- ... other dependency elements ... -->
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

###Maven Repositories

所有GA发布版本 (版本号以 .RELEASE结尾) 都被部署到Maven Central, 所以不需要在你的 pom里设置额外的库

如果你使用了一个 SNAPSHOT 版本, 你需要确认你设置了Snapshot库，如下:

pom.xml. 

```xml
<repositories>
<!-- ... possibly other repository elements ... -->
<repository>
	<id>spring-snapshot</id>
	<name>Spring Snapshot Repository</name>
	<url>http://repo.springsource.org/snapshot</url>
</repository>
</repositories>
```

If you are using a milestone or release candidate version, you will need to ensure you have the Spring Milestone repository defined as shown below:

pom.xml. 

```xml
<repositories>
<!-- ... possibly other repository elements ... -->
<repository>
	<id>spring-milestone</id>
	<name>Spring Milestone Repository</name>
	<url>http://repo.springsource.org/milestone</url>
</repository>
</repositories>
```

