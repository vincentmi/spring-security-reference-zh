#安全命名空间配置

##简介
命名空间配置在Spring框架的2.0版本就可以使用了，他允许你通过额外的XML架构元素补充传统的Spring bean应用程序上下文。你可以从Spring的参考文档找到更多信息。命名空间元素可以简单的允许配置单个bean,或者更强大的，定义一个可选的配置语法，这样更贴近问题域并且对用户隐藏背后的复杂性。一个简单的元素可以隐藏多个bean 和添加到应用程序上下文的多个处理步骤。例如：从安全命令空间添加后面的元素到应用程序上下文将开始一个LDAP服务到应用程序内用于测试：

```xml
<security:ldap-server />
```


这比配置一个Apache目录服务器bean简单得多。

The most common alternative configuration requirements are supported by attributes on the ldap-server element and the user is isolated from worrying about which beans they need to create and what the bean property names are. [1]. Use of a good XML editor while editing the application context file should provide information on the attributes and elements that are available. We would recommend that you try out the SpringSource Tool Suite as it has special features for working with standard Spring namespaces.

To start using the security namespace in your application context, you need to have the spring-security-config jar on your classpath. Then all you need to do is add the schema declaration to your application context file:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:security="http://www.springframework.org/schema/security"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/security
		http://www.springframework.org/schema/security/spring-security.xsd">
	...
</beans>
```


In many of the examples you will see (and in the sample) applications, we will often use "security" as the default namespace rather than "beans", which means we can omit the prefix on all the security namespace elements, making the content easier to read. You may also want to do this if you have your application context divided up into separate files and have most of your security configuration in one of them. Your security application context file would then start like this

```xml
<beans:beans xmlns="http://www.springframework.org/schema/security"
xmlns:beans="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/security
		http://www.springframework.org/schema/security/spring-security.xsd">
	...
</beans:beans>
```


We’ll assume this syntax is being used from now on in this chapter.