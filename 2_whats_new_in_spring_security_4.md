#Spring Security 4 新特性

Spring Security 4.0 release解决了[175+的 tickets](http://goo.gl/ui9GCl) 。

## 功能
下面Spring Security 4.0的新功能的亮点：

- Web Socket 支持
- Test 支持
- Spring Data 集成
- CSRF Token 参数解析器
- 更多安全默认值
- 角色方法不需要包含 ROLE_ 例如 之前需要在XML配置中包含以下内容:

```xml
<intercept-url pattern="/**" access="hasRole('ROLE_USER')"/>
```
现在你可以选择忽略ROLE_ 前缀. 我们这样做是为了消除重复. 具体地，由于hasRole表达式已将值定义为一个角色，如果它不存在会自动添加前缀。下面的配置和前面的配置效果一样:

```xml
<intercept-url pattern="/**" access="hasRole('USER')"/>
```

类似的下面的配置

```xml
@PreAuthorize("hasRole('ROLE_USER')")
```

和下面更简洁的配置是相同的

```xml
@PreAuthorize("hasRole('USER')")
```

- 很多集成测试添加到示例中
- 弃用@EnableWebMvcSecurity - 通过更新最小Spring版本，我们现在默认使用@EnableWebSecurity 和MVC集成，仍然允许它被覆盖。

##从 3.x 迁移到 4.x

Spring Security为了应对应用漏洞的发展，作为一个主要版本，Spring Security团队趁机做出一些非被动的变化，这集中在：

- 确保默认配置下更安全
- 尽量避免信息泄露
- 移除废弃的API

查看完成的从Spring Security 3到4 请参考以下指南：

[Migrating from Spring Security 3.x to 4.x (XML Configuration)](http://docs.spring.io/spring-security/site/migrate/current/3-to-4/html5/migrate-3-to-4-xml.html)  
[Migrating from Spring Security 3.x to 4.x (Java Configuration)](http://docs.spring.io/spring-security/site/migrate/current/3-to-4/html5/migrate-3-to-4-jc.html)
