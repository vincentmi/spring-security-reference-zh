#Java配置

Java配置的支持主要在Spring框架的3.1加入。Spring Security 从3.2开始加入java配置支持，这让用户而不用任何XML更简单的配置Spring Security.

如果你熟悉第4章，安全命名空间配置，你应该找到它和Java的安全配置支持之间的相当多的相似之处。


>Spring Security 提供大量的示例应用，使用```-jc`` 参数演示使用Java 配置Spring Security

## 基础的网站安全 Java配置

第一步是创建我们的Java配置。这个配置在你的应用程序中创建一个springSecurityFilterChain 的Servlet的过滤器，负责所有安全（例如 保护应用程序的URL,验证提交的用户名和密码，重定向到登陆的表单等等）。你可以在下面找到大部分Java配置项的例子：

```java
import org.springframework.beans.factory.annotation.Autowired;

import org.springframework.context.annotation.*;
import org.springframework.security.config.annotation.authentication.builders.*;
import org.springframework.security.config.annotation.web.configuration.*;

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
		auth.inMemoryAuthentication()
			.withUser("user").password("password").roles("USER");
	}
}
```

> configureGlobal 方法的名字不重要，然而，重要的是只在一个被@EnableWebSecurity, @EnableGlobalMethodSecurity或者@EnableGlobalAuthentication注解的类中配置AuthenticationManagerBuilder，否则会有不可预知的后果。


 真的是没有太多的这种配置，但它确实很多功能。你可以在下面的找到功能摘要：

- 在你的应用程序中对每个URL进行验证
- 为你生成一个登陆表单
- 允许用户使用用户名user和密码password使用验证表单进行验证。
- 允许用户登出
- CSRF 攻击防范
- Session保护
- 安全 Header 集成
 - 对安全要求严格的HTTP传输安全
 - X-Content-Type-Options 集成
 - 缓存控制(稍后可以允许你缓存静态资源)
 - X-XSS-Protection集成
 - X-Frame-Options集成防止点击劫持
- 和以下 Servlet API 方法集成
 - HttpServletRequest#getRemoteUser()
 - HttpServletRequest.html#getUserPrincipal()
 - HttpServletRequest.html#isUserInRole(java.lang.String)
 - HttpServletRequest.html#login(java.lang.String, java.lang.String)
 - HttpServletRequest.html#logout()

### AbstractSecurityWebApplicationInitializer

下一步是在war里注册 springSecurityFilterChain. This can be done in Java Configuration with Spring’s WebApplicationInitializer support in a Servlet 3.0+ environment. Not suprisingly, Spring Security provides a base class AbstractSecurityWebApplicationInitializer that will ensure the springSecurityFilterChain gets registered for you. The way in which we use AbstractSecurityWebApplicationInitializer differs depending on if we are already using Spring or if Spring Security is the only Spring component in our application.

- Section 3.1.2, “AbstractSecurityWebApplicationInitializer without Existing Spring” - Use these instructions if you are not using Spring already
- Section 3.1.3, “AbstractSecurityWebApplicationInitializer with Spring MVC” - Use these instructions if you are already using Spring
