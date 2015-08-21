#应用程序示例

这里有几个可用的网站应用程序示例。为了避免大量的下载。只有"totorial"和"contacts"示例包含到了分发的zip文件。其他的可用按照介绍的描述从源代码构建。你可用很容易的自己构建项，通过[http://spring.io/spring-security/](http://spring.io/spring-security/)网站可用获取更多信息。本章中提到的所有路径是相对于项目的源目录。



## Tutorial 示例

本教程样本是一个让你入门的很好的基础的例子。它完全使用了简单命名空间配置。编译好的应用程序包含在分发的zip包中。随时可以部署到你的Web容器（spring-security-samples-tutorial-3.1.x.war）。基于表单的认证机制结合记住我功能，验证供应商提供使用cookie自动记住登录。

我们建议您从本示例开始，因为XML非常小，易于遵循。最重要的是，你可以把这个XML文件（和它对应的web.xml入口）轻松地添加到现有的应用程序。在这个基本集成成功的时候，我们建议你试着添加方法验证和领域对象安全。


## Contacts 示例

该示例是一个高级的例子，它展示除了基本的应用程序安全领域对象的访问控制列表（ACL）的更强大的功能。本申请提供了一个接口让用户能够管理简单的联系人数据库（域对象）。

拷贝WAR文件从Spring Security 分发包到你自己的容器的webapps目录来部署它。war名字是spring-security-samples-contacts-3.1.x.war （扩展的版本号取决于你的版本号）

开始你的容器后检查应用程序是否可用载入，访问 [http://localhost:8080/contacts ](http://localhost:8080/contacts )(或者其他适合你的容器的URL)

接下来，单击“Debug”。系统将提示您进行身份验证，以及页面提示的一系列用户名和密码。简单验证，显示一个结果页面。它应该包含类似于以下成功消息：

```
Security Debug Information

Authentication object is of type:
org.springframework.security.authentication.UsernamePasswordAuthenticationToken

Authentication object as a String:

org.springframework.security.authentication.UsernamePasswordAuthenticationToken@1f127853:
Principal: org.springframework.security.core.userdetails.User@b07ed00: Username: rod; \
Password: [PROTECTED]; Enabled: true; AccountNonExpired: true;
credentialsNonExpired: true; AccountNonLocked: true; \
Granted Authorities: ROLE_SUPERVISOR, ROLE_USER; \
Password: [PROTECTED]; Authenticated: true; \
Details: org.springframework.security.web.authentication.WebAuthenticationDetails@0: \
RemoteIpAddress: 127.0.0.1; SessionId: 8fkp8t83ohar; \
Granted Authorities: ROLE_SUPERVISOR, ROLE_USER

Authentication object holds the following granted authorities:

ROLE_SUPERVISOR (getAuthority(): ROLE_SUPERVISOR)
ROLE_USER (getAuthority(): ROLE_USER)

Success! Your web filters appear to be properly configured!
```
一旦你成功地收到上述消息，返回到示例应用程序的主页，点击“Manage”。然后，您可以使用这个应用程序。注意只有当前用户的联系人会被显示，并且只有拥有```ROLE_SUPERVISOR```可用授权去删除联系人，在幕后 ```MethodSecurityInterceptor```在保护业务对象。

该应用程序允许您修改与不同的联系人相关联的访问控制列表。一定要试试这个，并了解它是如何工作通过检查应用程序上下文XML文件。


##LDAP 示例

LDAP例子程序提供了一个基本的配置和使用命名空间和使用传统的bean同等设置在同一个应用程序上下文文件。这意味着，其实是在这个应用程序中配置了两个相同的身份验证提供者。


## OpenID 示例

OpenID的示例演示了如何使用命名空间来配置OpenID和如何设置为Google，Yahoo和M OpenID设置属性交换配置（您可以添加其他的供应商，如果你愿意的话）。它使用一个基于JQuery的openid选择框，以提供用户友好的登录页面，它允许用户很容易地选择一个验证提供者，而不是输入OpenID标识符。

应用程序的不同之处在于它允许任何用户访问该站点正常认证情景（只要它们的OpenID身份验证是成功的）。当您第一次登录时，你会得到一个“Welcome [your name]”“的消息。如果您注销并重新登录（以相同OpenID身份），那么这应更改为”欢迎回来“，这是通过使用一个实现自定义的```UserDetailsService```它分配一个标准角色给的任何用户，并在内部存储的身份的map。显然，一个真正的应用程序将使用一个数据库替换他。看一看源代码了解更多信息，此类还考虑到，不同供应商返回不同属性，并建立相应用户名。

## CAS 示例

CAS示例要求您同时运行CAS服务器和CAS客户端。它不包含在发行包，所以你应该按照描述签出目代码。你会发现sample/cas目录下的相关文件。还有在那里一个readme.txt文件，这也解释了如何直接从源代码运行服务器和客户端，完全支持SSL。


##JAAS 示例

该JAAS是很简单的如何用Spring Security使用JAAS LoginModule的例子。所提供的```LoginModule```将成功地验证用户，如果用户名和密码相等，否则抛出一个```LoginException```异常。在本实例中使用的的```AuthorityGranter```总是授予角色ROLE_USER。示例应用程序还演示了如何通过设置```LoginModule ```的```jaas-api-provision```等于```true```返回JAAS Subject 来运行(*_*)。

## Pre-Authentication 示例

此示例应用程序演示了如何绑定bean从预认证框架从Java EE容器使用的登录信息。用户名和角色是由容器设置的。

代码是 samples/preauth.