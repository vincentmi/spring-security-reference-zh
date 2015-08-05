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

Many independent software vendors (ISVs) adopt Spring Security because of this significant choice of flexible authentication models. Doing so allows them to quickly integrate their solutions with whatever their end clients need, without undertaking a lot of engineering or requiring the client to change their environment. If none of the above authentication mechanisms suit your needs, Spring Security is an open platform and it is quite simple to write your own authentication mechanism. Many corporate users of Spring Security need to integrate with "legacy" systems that don’t follow any particular security standards, and Spring Security is happy to "play nicely" with such systems.

Irrespective of the authentication mechanism, Spring Security provides a deep set of authorization capabilities. There are three main areas of interest - authorizing web requests, authorizing whether methods can be invoked, and authorizing access to individual domain object instances. To help you understand the differences, consider the authorization capabilities found in the Servlet Specification web pattern security, EJB Container Managed Security and file system security respectively. Spring Security provides deep capabilities in all of these important areas, which we’ll explore later in this reference guide.


## History
Spring Security began in late 2003 as "The Acegi Security System for Spring". A question was posed on the Spring Developers' mailing list asking whether there had been any consideration given to a Spring-based security implementation. At the time the Spring community was relatively small (especially compared with the size today!), and indeed Spring itself had only existed as a SourceForge project from early 2003. The response to the question was that it was a worthwhile area, although a lack of time currently prevented its exploration.

With that in mind, a simple security implementation was built and not released. A few weeks later another member of the Spring community inquired about security, and at the time this code was offered to them. Several other requests followed, and by January 2004 around twenty people were using the code. These pioneering users were joined by others who suggested a SourceForge project was in order, which was duly established in March 2004.

In those early days, the project didn’t have any of its own authentication modules. Container Managed Security was relied upon for the authentication process, with Acegi Security instead focusing on authorization. This was suitable at first, but as more and more users requested additional container support, the fundamental limitation of container-specific authentication realm interfaces became clear. There was also a related issue of adding new JARs to the container’s classpath, which was a common source of end user confusion and misconfiguration.

Acegi Security-specific authentication services were subsequently introduced. Around a year later, Acegi Security became an official Spring Framework subproject. The 1.0.0 final release was published in May 2006 - after more than two and a half years of active use in numerous production software projects and many hundreds of improvements and community contributions.

Acegi Security became an official Spring Portfolio project towards the end of 2007 and was rebranded as "Spring Security".

Today Spring Security enjoys a strong and active open source community. There are thousands of messages about Spring Security on the support forums. There is an active core of developers who work on the code itself and an active community which also regularly share patches and support their peers.


##Release Numbering
It is useful to understand how Spring Security release numbers work, as it will help you identify the effort (or lack thereof) involved in migrating to future releases of the project. Each release uses a standard triplet of integers: MAJOR.MINOR.PATCH. The intent is that MAJOR versions are incompatible, large-scale upgrades of the API. MINOR versions should largely retain source and binary compatibility with older minor versions, thought there may be some design changes and incompatible updates. PATCH level should be perfectly compatible, forwards and backwards, with the possible exception of changes which are to fix bugs and defects.

The extent to which you are affected by changes will depend on how tightly integrated your code is. If you are doing a lot of customization you are more likely to be affected than if you are using a simple namespace configuration.

You should always test your application thoroughly before rolling out a new version.

##Getting Spring Security
You can get hold of Spring Security in several ways. You can download a packaged distribution from the main Spring Security page, download individual jars from the Maven Central repository (or a SpringSource Maven repository for snapshot and milestone releases) or, alternatively, you can build the project from source yourself.

### Usage with Maven

A minimal Spring Security Maven set of dependencies typically looks like the following:
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
If you are using additional features like LDAP, OpenID, etc. you will need to also include the appropriate Section 1.4.3, “Project Modules”.

###Maven Repositories

All GA releases (i.e. versions ending in .RELEASE) are deployed to Maven Central, so no additional Maven repositories need to be declared in your pom.

If you are using a SNAPSHOT version, you will need to ensure you have the Spring Snapshot repository defined as shown below:

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

