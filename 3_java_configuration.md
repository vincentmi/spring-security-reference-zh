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

下一步是在war里注册 springSecurityFilterChain. 这可以通过Spring在Servlet 3.0+环境中对WebApplicationInitializer的支持进行Java配置，不出所料，Spring Security提供了基本的抽象类```AbstractSecurityWebApplicationInitializer```,这可以确保springSecurityFilterChain被注册。我们使用```AbstractSecurityWebApplicationInitializer```的不同方式取决于你是已经在使用Spring框架还是只使用了Spring Security。

- Section 3.1.2, “AbstractSecurityWebApplicationInitializer 不与Spring一起使用” - 使用这个说明如果你没有使用Spring框架
- Section 3.1.3, “AbstractSecurityWebApplicationInitializer 与Spring一起使用” - 如果你正在使用Spring框架使用这个说明

#### AbstractSecurityWebApplicationInitializer 不与Spring一起使用

如果你没有使用Spring MVC 或Spring , 你需要传递```SecurityConfig```到超类来确保配置被使用，你可以参考下面的例子：

```java
import org.springframework.security.web.context.*;

public class SecurityWebApplicationInitializer
	extends AbstractSecurityWebApplicationInitializer {

	public SecurityWebApplicationInitializer() {
		super(SecurityConfig.class);
	}
}
```

```SecurityWebApplicationInitializer ```将会做下面的事情:

- 自动注册为你的应用程序的每个URL注册```springSecurityFilterChain``` 过滤器
- 添加一个 ```ContextLoaderListener```用来载入```SecurityConfig```.

#### AbstractSecurityWebApplicationInitializer 与Spring一起使用
如果我们在应用程序的其他地方已经使用了Spring,那么我们已经有了一个```WebApplicationInitializer```用来载入Spring的配置。如果我们使用上面的配置将会得到一个错误。所以我们应该使用已经存在的```ApplicationContext```来注册Spring Security.举个例子，如果我们使用Spring MVC我们的```SecurityWebApplicationInitializer```应该看起来和下面的差不多：

```java
import org.springframework.security.web.context.*;

public class SecurityWebApplicationInitializer
	extends AbstractSecurityWebApplicationInitializer {

}
```

这会简单的只为你的应用程序的所有URL注册springSecurityFilterChain过滤器。然后我们需要确保这个Security配置被载入到我们已经存在的```ApplicationInitializer```. 例如, 如果你使用Spring MVC他应该被加入到  getRootConfigClasses()

```java
public class MvcWebApplicationInitializer extends
		AbstractAnnotationConfigDispatcherServletInitializer {

	@Override
	protected Class<?>[] getRootConfigClasses() {
		return new Class[] { SecurityConfig.class };
	}

	// ... other overrides ...
}
```

### HttpSecurity

到目前为止我们的 SecurityConfig 只包含了关于如何验证我们的用户的信息。Spring Security周末知道我们想对所有的用户进行验证？Spring Security周末知道我们需要支持基于表单的验证？原因是```WebSecurityConfigurerAdapter```在 ```configure(HttpSecurity http)``` 方法提供了一个默认的配置，看起来和下面类似：

```java
protected void configure(HttpSecurity http) throws Exception {
	http
		.authorizeRequests()
			.anyRequest().authenticated()
			.and()
		.formLogin()
			.and()
		.httpBasic();
}
```

上面的默认配置:

- 确保我们应用中的所有请求都需要用户被认证
- 允许用户进行基于表单的认证
- 允许用户使用HTTP基本验证进行认证

你可以看到这个配置和下面的XML命名配置相似：

```xml
<http>
	<intercept-url pattern="/**" access="authenticated"/>
	<form-login />
	<http-basic />
</http>
```

Java配置使用and()方法相当于XML标签的关闭，这样允许我们继续配置父类节点。如果你阅读代码他很合理，我想配置请求验证，并使用表单和HTTP基本身份验证进行登录。

然而，Java配置有不同的默认URL和参数，当你自定义用户登录页时需要牢记这一点。让我们的URL更加RESTful ,另外不要那么明显的观察出我们在使用Spring Security这样帮助我们避免信息泄露。比如：


```java
protected void configure(HttpSecurity http) throws Exception {
	http
		.authorizeRequests()
			.anyRequest().authenticated()
			.and()
		.formLogin()
			.loginPage("/login") //注1
			.permitAll();        //注2
}
```
 1. 指定登陆页的路径 
 2. 我们必须允许所有用户访问我们的登录页 (例如未验证的用户) ，这个formLogin().permitAll() 方法允许基于表单登录的所有URL的所有用户的访问。

一个我们当前配置使用的JSP实现的页面如下：

 
 >下面这个登陆页是我们的当前配置，如果不符合我们的要求我们可以很容易的更新我们的配置。
 >
 
 
 
```jsp
	 <c:url value="/login" var="loginUrl"/>
	<form action="${loginUrl}" method="post">   <!-- 1 -->
		<c:if test="${param.error != null}">     <!-- 2  -->
			<p>
				Invalid username and password.
			</p>
		</c:if>
		<c:if test="${param.logout != null}">    <!-- 3  -->
			<p>
				You have been logged out.
			</p>
		</c:if>
		<p>
			<label for="username">Username</label>
			<input type="text" id="username" name="username"/><!-- 4  -->
		</p>
		<p>
			<label for="password">Password</label>
			<input type="password" id="password" name="password"/><!-- 5  -->	</p>
		<input type="hidden"  
			name="${_csrf.parameterName}"
			value="${_csrf.token}"/><!-- 6  -->
		<button type="submit" class="btn">Log in</button>
	</form>  
```


 1. 一个POST请求到/login用来验证用户
 2. 如果参数有错误，验证尝试失败
 3. 如果请求参数logout存在则登出
 4. 登录名参数必须被命名为username
 5. 密码参数必须被命名为password
 6. CSRF参数，了解更多查阅 后续“包含CSRF Token” 和 "跨站请求伪造(CSRF)"相关章节


## 验证请求

我们的例子中要求用户进行身份验证并且在我们应用程序的每个URL这样做。我们可以通过给```http.authorizeRequests()```添加多个子节点来指定多个定制需求到我们的URL。例如：

```java
protected void configure(HttpSecurity http) throws Exception {
	http
		.authorizeRequests()  //1
			.antMatchers("/resources/**", "/signup", "/about").permitAll() //2
			.antMatchers("/admin/**").hasRole("ADMIN") //3
			.antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')")            4
			.anyRequest().authenticated()  //5
			.and()
		// ...
		.formLogin();
}

```

1. ```http.authorizeRequests() ```方法有多个子节点，每个macher按照他们的声明顺序执行。
2. 我们指定任何用户都可以访问的多个URL模式。任何用户都可以访问URL以 "/resources/",开头的URL ,以及"/signup",  "/about".
3. 以"/admin/" 开头的URL只能由拥有 "ROLE_ADMIN"角色的用户访问. 请注意我们使用HasRole方法，没有使用ROLE_前缀。
4. 任何以/db/开头的URL需要用户同时具有"ROLE_ADMIN" 和 "ROLE_DBA". 和上面一样我们的hasRole方法也没有使用ROLE_前缀。
5. 尚未匹配的任何URL要求用户进行身份验证

## 处理登出

当使用```WebSecurityConfigurerAdapter```时注销功能会被自动运用上，默认是通过访问```/logout```这个URL将用户登出，该操作执行以下动作：

- 使Session无效
- 清除所有已经配置的RememberMe认证
- 清除```SecurityContextHolder```
- 跳转到``` /login?success```

和登录功能类似，你也有不同的选项来定制你的注销功能。

```java
protected void configure(HttpSecurity http) throws Exception {
	http
		.logout()  // 1
			.logoutUrl("/my/logout")// 2
			.logoutSuccessUrl("/my/index") // 3
			.logoutSuccessHandler(logoutSuccessHandler)//4
			.invalidateHttpSession(true)  // 5
			.addLogoutHandler(logoutHandler) // 6
			.deleteCookies(cookieNamesToClear) //7
			.and()
		...
}
```


 1. 提供注销支持，使用```WebSecurityConfigurerAdapter. ```是会自动被应用。
 2. 设置触发注销操作的URL(默认是logout),如果CSRF保护被启用（默认是启用的）的话这个请求的方式被限定为POST.请查阅javaDoc相关信息。
 3. 注销之后跳转的URL.默认是```/login?logout```,具体查看JaveDoc文档
 4. 让你设置定制的```LogoutSuccessHandler```,如果指定了这个选项那么```logoutSuccessUrl()```的设置会被忽略，请查阅JavaDoc文档
 5. 指定是否在注销时让HttpSession无效。默认设置为true,在内部配置```SecurityContextLogoutHandler```选项。请参阅JavaDoc
 6. 添加一个```logoutHandler```,默认```SecurityContextLogoutHandler```会被添加为最后一个logoutHandler.
 7. 允许指定在注销成功时将移除的cookie.这是一个显示的添加一个```CookieClearingLogoutHandler```的快捷方式。

 >注销也可以通过XML命名空间进行配置，请参阅Spring Security XML命令空间相关文档获取更多细节。 
 
 一般来说，为了定制注销功能，你可以添加```LogoutHandler```以及```LogoutSuccessHandler```的实现。对于许多常见场景，当使用流式API时，这些处理器会在幕后进行添加。
 
 ### LogoutHandler
 一般来说，```LogoutHandler```的实现类可以参与到注销处理中。他们被用来执行必要的清理，因而他们不应该抛出错误，我们提供各种实现：
 
- [PersistentTokenBasedRememberMeServices](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/authentication/rememberme/PersistentTokenBasedRememberMeServices.html)
- [TokenBasedRememberMeServices](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/authentication/rememberme/TokenBasedRememberMeServices.html)
- [CookieClearingLogoutHandler](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/authentication/logout/CookieClearingLogoutHandler.html)
- [CsrfLogoutHandler](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/csrf/CsrfLogoutHandler.html)
- [SecurityContextLogoutHandler](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/authentication/logout/SecurityContextLogoutHandler.html)

请查看 ["Remember-Me接口和实现"](#Remember-Me Interfaces_and_Implementations)获取详情。

流式API提供了调用相应的```LogoutHandler```实现的快捷方式，而不用直接提供```LogoutHandler```的实现。例如：```deleteCookies()``` 允许指定注销成功时要删除的一个或者多个cookie.这是一个添加```CookieClearingLogoutHandler```的快捷方式。

###LogoutSuccessHandler

```LogoutSuccessHandler```被```LogoutFiler```在成功注销后调用，用来进行重定向或者转发相应的目的地。注意这个接口与LogoutHandler几乎一样，但是可以抛出异常。


下面是 提供的一些实现：

- [SimpleUrlLogoutSuccessHandler](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/authentication/logout/SimpleUrlLogoutSuccessHandler.html)
- HttpStatusReturningLogoutSuccessHandler


和前面提到的一样，你不需要直接指定```SimpleUrlLogoutSuccessHandler```.而使用流式API通过设置```logoutSuccessUrl()```快捷的进行设置。这样会隐式的设置```SimpleUrlLogoutSuccessHandler```. 注销成功后将重定向到设置的URL地址。默认的地址是```/login?logout```.

在REST API场景中```HttpStatusReturningLogoutSuccessHandler``` 会进行一些有趣的改变。```LogoutSuccessHandler```允许你设置一个返回给客户端的HTTP状态码（默认返回200）来替换重定向到URL这个动作.

###进一步的注销相关的参考(TODO)

- [处理注销](#Logout_Handling)
- [测试注销](#Testing_Logout)
- [HttpServletRequest.logout()](#HttpServletRequest.logout)
- [章节 “Remember-Me 接口和实现”](#Remember-Me Interfaces_and_Implementations)
- [注销的CSRF说明](#Logging_Out)
- [单点注销(CAS协议)](#Single_Logout)
- [注销的XML命名空间章节](#logout_element )

##验证
到现在为止我们只看了一下基本的验证配置，让给我们看看一些稍微高级点的身份验证配置选项。

###内存中的身份验证
我们已经看到了一个单用户配置到内存验证的示例，下面是配置多个用户的例子：

```java
@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
	auth
		.inMemoryAuthentication()
			.withUser("user").password("password").roles("USER").and()
			.withUser("admin").password("password").roles("USER", "ADMIN");
}
```

###JDBC 验证

You can find the updates to suppport JDBC based authentication. The example below assumes that you have already defined a DataSource within your application. The jdbc-jc sample provides a complete example of using JDBC based authentication.

```java
@Autowired
private DataSource dataSource;

@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
	auth
		.jdbcAuthentication()
			.dataSource(dataSource)
			.withDefaultSchema()
			.withUser("user").password("password").roles("USER").and()
			.withUser("admin").password("password").roles("USER", "ADMIN");
}
```

### LDAP 验证
You can find the updates to suppport LDAP based authentication. The ldap-jc sample provides a complete example of using LDAP based authentication.

```java
@Autowired
private DataSource dataSource;

@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
	auth
		.ldapAuthentication()
			.userDnPatterns("uid={0},ou=people")
			.groupSearchBase("ou=groups");
}
```

the example above uses the following LDIF and an embedded Apache DS LDAP instance.

users.ldif. 

```
dn: ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: groups

dn: ou=people,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: people

dn: uid=admin,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Rod Johnson
sn: Johnson
uid: admin
userPassword: password

dn: uid=user,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Dianne Emu
sn: Emu
uid: user
userPassword: password

dn: cn=user,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfNames
cn: user
uniqueMember: uid=admin,ou=people,dc=springframework,dc=org
uniqueMember: uid=user,ou=people,dc=springframework,dc=org

dn: cn=admin,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfNames
cn: admin
uniqueMember: uid=admin,ou=people,dc=springframework,dc=org
```

##Multiple HttpSecurity

We can configure multiple HttpSecurity instances just as we can have multiple <http> blocks. The key is to extend the WebSecurityConfigurationAdapter multiple times. For example, the following is an example of having a different configuration for URL’s that start with /api/.

```java
@EnableWebSecurity
public class MultiHttpSecurityConfig {
	@Autowired
	public void configureGlobal(AuthenticationManagerBuilder auth) { 1
		auth
			.inMemoryAuthentication()
				.withUser("user").password("password").roles("USER").and()
				.withUser("admin").password("password").roles("USER", "ADMIN");
	}

	@Configuration
	@Order(1)                                                        2
	public static class ApiWebSecurityConfigurationAdapter extends WebSecurityConfigurerAdapter {
		protected void configure(HttpSecurity http) throws Exception {
			http
				.antMatcher("/api/**")                               3
				.authorizeRequests()
					.anyRequest().hasRole("ADMIN")
					.and()
				.httpBasic();
		}
	}

	@Configuration                                                   4
	public static class FormLoginWebSecurityConfigurerAdapter extends WebSecurityConfigurerAdapter {

		@Override
		protected void configure(HttpSecurity http) throws Exception {
			http
				.authorizeRequests()
					.anyRequest().authenticated()
					.and()
				.formLogin();
		}
	}
}
```

1
Configure Authentication as normal
2
Create an instance of WebSecurityConfigurerAdapter that contains @Order to specify which WebSecurityConfigurerAdapter should be considered first.
3
The http.antMatcher states that this HttpSecurity will only be applicable to URLs that start with /api/
4
Create another instance of WebSecurityConfigurerAdapter. If the URL does not start with /api/ this configuration will be used. This configuration is considered after ApiWebSecurityConfigurationAdapter since it has an @Order value after 1 (no @Order defaults to last).

##Method Security

From version 2.0 onwards Spring Security has improved support substantially for adding security to your service layer methods. It provides support for JSR-250 annotation security as well as the framework���s original @Secured annotation. From 3.0 you can also make use of new expression-based annotations. You can apply security to a single bean, using the intercept-methods element to decorate the bean declaration, or you can secure multiple beans across the entire service layer using the AspectJ style pointcuts.

### EnableGlobalMethodSecurity
We can enable annotation-based security using the @EnableGlobalMethodSecurity annotation on any @Configuration instance. For example, the following would enable Spring Security’s @Secured annotation.

```java
@EnableGlobalMethodSecurity(securedEnabled = true)
public class MethodSecurityConfig {
// ...
}
```

Adding an annotation to a method (on an class or interface) would then limit the access to that method accordingly. Spring Security’s native annotation support defines a set of attributes for the method. These will be passed to the AccessDecisionManager for it to make the actual decision:

```java
public interface BankService {

@Secured("IS_AUTHENTICATED_ANONYMOUSLY")
public Account readAccount(Long id);

@Secured("IS_AUTHENTICATED_ANONYMOUSLY")
public Account[] findAccounts();

@Secured("ROLE_TELLER")
public Account post(Account account, double amount);
}
```

Support for JSR-250 annotations can be enabled using

```java
@EnableGlobalMethodSecurity(jsr250Enabled = true)
public class MethodSecurityConfig {
// ...
}
```

These are standards-based and allow simple role-based constraints to be applied but do not have the power Spring Security’s native annotations. To use the new expression-based syntax, you would use

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig {
// ...
}
```

and the equivalent Java code would be

```java
public interface BankService {

@PreAuthorize("isAnonymous()")
public Account readAccount(Long id);

@PreAuthorize("isAnonymous()")
public Account[] findAccounts();

@PreAuthorize("hasAuthority('ROLE_TELLER')")
public Account post(Account account, double amount);
}
```

### GlobalMethodSecurityConfiguration
Sometimes you may need to perform operations that are more complicated than are possible with the @EnableGlobalMethodSecurity annotation allow. For these instances, you can extend the GlobalMethodSecurityConfiguration ensuring that the @EnableGlobalMethodSecurity annotation is present on your subclass. For example, if you wanted to provide a custom MethodSecurityExpressionHander, you could use the following configuration:

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
	@Override
	protected MethodSecurityExpressionHandler createExpressionHandler() {
		// ... create and return custom MethodSecurityExpressionHandler ...
		return expressionHander;
	}
}
```


For additional information about methods that can be overriden, refer to the GlobalMethodSecurityConfiguration Javadoc.

##Post Processing Configured Objects

Spring Security’s Java Configuration does not expose every property of every object that it configures. This simplifies the configuration for a majority of users. Afterall, if every property was exposed, users could use standard bean configuration.

While there are good reasons to not directly expose every property, users may still need more advanced configuration options. To address this Spring Security introduces the concept of an ObjectPostProcessor which can used to modify or replace many of the Object instances created by the Java Configuration. For example, if you wanted to configure the filterSecurityPublishAuthorizationSuccess property on FilterSecurityInterceptor you could use the following:

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
	http
		.authorizeRequests()
			.anyRequest().authenticated()
			.withObjectPostProcessor(new ObjectPostProcessor<FilterSecurityInterceptor>() {
				public <O extends FilterSecurityInterceptor> O postProcess(
						O fsi) {
					fsi.setPublishAuthorizationSuccess(true);
					return fsi;
				}
			});
}
```






