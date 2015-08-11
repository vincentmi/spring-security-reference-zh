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
你可以找到一些更新来支持基于JDBC的验证。下面的例子假设你已经在应用程序中定义好了```DateSource``` , jdbc-jc示例提供了一个完整的基于JDBC的验证。

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
你可以找到一些更新来支持基于LDAP的身份验证，ldap-jc提供一个完成的使用基于LDAP的身份验证的示例。

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

上面的例子中使用以下LDIF和嵌入式Apache DS LDAP实例。

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

##多个HttpSecurity
我们可以配置多个HttpSecurity实例，就像我们可以有多个<http>块抑郁。关键在于对```WebSecurityConfigurationAdapter```进行多次扩展。例如下面是一个对```/api/```开头的URL进行的不同的设置。


```java
@EnableWebSecurity
public class MultiHttpSecurityConfig {
	@Autowired
	public void configureGlobal(AuthenticationManagerBuilder auth) { //1
		auth
			.inMemoryAuthentication()
				.withUser("user").password("password").roles("USER").and()
				.withUser("admin").password("password").roles("USER", "ADMIN");
	}

	@Configuration
	@Order(1)  // 2
	public static class ApiWebSecurityConfigurationAdapter extends WebSecurityConfigurerAdapter {
		protected void configure(HttpSecurity http) throws Exception {
			http
				.antMatcher("/api/**")  // 3
				.authorizeRequests()
					.anyRequest().hasRole("ADMIN")
					.and()
				.httpBasic();
		}
	}

	@Configuration // 4
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

1. 配置正常的验证
2. 创建一个 ```WebSecurityConfigurerAdapter``` ，包含一个```@Order```注解，用来指定哪一个```WebSecurityConfigurerAdapter```更优先。
3. ``` http.antMatcher ```指出，这个```HttpSecurity```只应用到以```/api/```开头的URL上。
4. 创建另外一个```WebSecurityConfigurerAdapter```实例。用于不以```/api/```开头的URL，这个配置的顺序在 ```ApiWebSecurityConfigurationAdapter```之后，因为他没有指定```@Order```值 (没有指定```@Order```默认会被放到最后).

##方法安全
从2.0开始Spring Security对服务层的方法的安全有了实质性的改善。他提供对JSR-250的注解安全支持象框架原生的@Secured注解一样好。从3.0开始你也可以使用新的基于表达式的注解。你可以应用安全到单独的bean,使用拦截方法元素去装饰Bean声明，或者你可以在整个服务层使用AspectJ风格的切入点保护多个bean。

### EnableGlobalMethodSecurity
我们可以在任何使用```@Configuration```的实例上，使用```@EnableGlobalMethodSecurity ```注解来启用基于注解的安全性。例如下面会启用Spring 的 @Secured注解.

```java
@EnableGlobalMethodSecurity(securedEnabled = true)
public class MethodSecurityConfig {
// ...
}
```
添加一个注解到一个方法（或者一个类或者接机）会限制对相应方法的访问。Spring Security的原生注解支持定义了一套用于该方法的属性。这些将被传递到```AccessDecisionManager``` 来做实际的决定：

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

使用如下代码启用JSR-250注解的支持

```java
@EnableGlobalMethodSecurity(jsr250Enabled = true)
public class MethodSecurityConfig {
// ...
}
```

这些都是基于标准的，并允许应用简单的基于角色的约束，但是没有Spring Security的本地注解的能力。要使用新的基于表达式的语法，你可以使用：

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig {
// ...
}
```

和响应的Java代码如下：

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
有时候你可能需要执行一些比```@ EnableGlobalMethodSecurity```注解允许的更复杂的操作。对于这些情况，你可以扩展 GlobalMethodSecurityConfiguration确保```@EnableGlobalMethodSecurity ```注解出现在你的子类。例如：如果你想提供一个定制的```MethodSecurityExpressionHander```，你可以使用下面的配置：


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

关于可以被重载的方法的更多信息，请参考GlobalMethodSecurityConfiguration的java文档。

##已配置对象的后续处理

Spring Security的Java配置没有公开每个配置对象的每一个属性，这简化了广大用户的配置。毕竟如果要配置每一个属性，用户可以使用标准的Bean配置。

虽然有一些很好的理由不直接暴露所有属性，用户可能任然需要更多高级配置，为了解决这个Spring Security引入了 ```ObjectPostProcessor``` 概念，用来修改或替换Java配置的对象实例。例如：如果你想在```FilterSecurityInterceptor```里配置```filterSecurityPublishAuthorizationSuccess```属性，你可以像下面一样：


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






