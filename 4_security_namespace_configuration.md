#安全命名空间配置

##简介
命名空间配置在Spring框架的2.0版本就可以使用了，他允许你通过额外的XML架构元素补充传统的Spring bean应用程序上下文。你可以从Spring的参考文档找到更多信息。命名空间元素可以简单的允许配置单个bean,或者更强大的，定义一个可选的配置语法，这样更贴近问题域并且对用户隐藏背后的复杂性。一个简单的元素可以隐藏多个bean 和添加到应用程序上下文的多个处理步骤。例如：从安全命令空间添加后面的元素到应用程序上下文将开始一个LDAP服务到应用程序内用于测试：

```xml
<security:ldap-server />
```


这比配置一个Apache目录服务器bean简单得多。最常见的替代配置需求是 ```ldap-server```元素的属性支持，用户不用担心他们要创建的bean的属性名称（注：你可以从[Chapter 26, LDAP 身份验证](#LDAP_Authentication)找到如何使用```ldap-server``` 元素的更多信息 ）。当编辑应用的Context文件时良好的XML编辑器应该可以提供可用的属性和元素的信息。我们建议你尝试使用[SpringSource Tool Suite](http://www.springsource.com/products/sts)，它具有与标准Spring 命名空间工作的特别的功能。

为了在你的应用程序Context中使用安全命名空间，你需要将```spring-security-config ``` jar包含到你的classpath中。然后在你的Context文件中加入以下的结构声明：


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
在很多示例（包括示例应用程序）中你将会看到，我们经常使用```security```作为默认的命名空间而不是使用```beans```,这样我们可以在所有安全命名空间中忽略前缀，使得内容更加容易阅读。如果你的的应用程序Context被分割成单独的文件，大部分的安全配置被放到一个文件中，你可能也想这样做。你的安全应用Context文件应该像下面的一样：

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

我们假设在这一章节我们都使用这种语法。