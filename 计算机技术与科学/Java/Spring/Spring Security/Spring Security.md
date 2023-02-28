```java
<dependency>  
   <groupId>org.springframework.boot</groupId>  
   <artifactId>spring-boot-starter-security</artifactId>  
</dependency>
```
Spring Boot Auto Configuration
Spring Boot automatically:
	1.创建一个名为springSecurityFilterChain的servlet Filter
	2.创建一个具有属性user和password的bean-UserDetailService
	3.注册名称为springSecurityFilterChain的bean到Servlet containter（应用于所有请求）

Spring Boot自动装配的配置：
1. 应用的任何交互都需要一个authenticated 用户user
2. 生成默认的登陆表单
3. 可以使用默认的账户和生成的密码，通过表单登录
4. 使用BCrypt加密保护密码
5. 用户可以登出
6. 阻止CSRF攻击
7. -   [Session Fixation](https://en.wikipedia.org/wiki/Session_fixation) protection 会话固定保护
8. Security Header integration
	1. -   [HTTP Strict Transport Security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) for secure requests
	2. -   [X-Content-Type-Options](https://msdn.microsoft.com/en-us/library/ie/gg622941(v=vs.85).aspx) integration
	3. -   Cache Control (can be overridden later by your application to allow caching of your static resources)
	4. -   [X-XSS-Protection](https://msdn.microsoft.com/en-us/library/dd565647(v=vs.85).aspx) integration
	5. -   X-Frame-Options integration to help prevent [Clickjacking](https://en.wikipedia.org/wiki/Clickjacking)
9. Integrate with the following Servlet API methods:
10. Integrate with the following Servlet API methods:
    
    -   [`HttpServletRequest#getRemoteUser()`](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getRemoteUser())
        
    -   [`HttpServletRequest.html#getUserPrincipal()`](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getUserPrincipal())
        
    -   [`HttpServletRequest.html#isUserInRole(java.lang.String)`](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#isUserInRole(java.lang.String))
        
    -   [`HttpServletRequest.html#login(java.lang.String, java.lang.String)`](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#login(java.lang.String,%20java.lang.String))
        
    -   [`HttpServletRequest.html#logout()`](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#logout()

架构：
	过滤器综述：A Review of Filters
	![[附件/Pasted image 20230227164818.png]]
	Spring Security的Servlet基于Servlet的Filters
	一个Servlet至多只能处理一个HttpServletRequest 和 HttpServletResponse，然而不止一个FIlter被用作：
		1. 阻止下游Fiter实例 或者 Servlet 被调用，因此，FIlter通常会写入HttpServletResponse
		2. 修改被下游Filter或Servlet使用的`HttpServletRequest` 或 `HttpServletResponse`
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
	// do something before the rest of the application
    chain.doFilter(request, response); // invoke the rest of the application
    // do something after the rest of the application
}
```
Filter的能力来自于传入的FilterChain
因为Filter只影响下游Filter实例和Servlet，Filter的顺序是至关重要的
## DelegatingFilterProxy
授权过滤器的代理

Spring提供的过滤器实现-DelegatingFilterProxy
允许在Servlet容器的生命周期和Spring的Application Context架起桥梁
因为Servlet容器可以允许通过它自己的标准注册Filter实例，但是无法注册Spring定义的bean
注册DelegatingFilterProxy可以通过标准的Servlet容器机制而不是将所有的工作委托给实现了Filter的Spring bean
![[附件/Pasted image 20230227172448.png]]
从ApplicationContext获取Bean Filter0并invokes
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
	// Lazily get Filter that was registered as a Spring Bean
	// For the example in DelegatingFilterProxy delegate is an instance of Bean Filter0
	Filter delegate = getFilterBean(someBeanName);
	// delegate work to the Spring Bean
	delegate.doFilter(request, response);
}
```
优点：
	1.允许延迟寻找Filter Bean instance
	因为容器需要在启动之前注册Filter实例，然而Spring通常使用ContextLoaderListener 加载SpringBeans，直到需要的Filter instances被注册之后，才会进行该动作。

## FilterChainProxy

Spring Security 提供的特殊Filter
允许通过SercurityFilterChain代理许多Filter instances 
因为FilterChainProxy 是一个Bean，它通常包裹在DelegatingFilterProxy
![[附件/Pasted image 20230227174106.png]]
## SecurityFilterChain
SecurityFilterChain 被 FilterChainProxt用以声明当前请求应该被调用的Spring Security Filter instances。
![[附件/Pasted image 20230227174405.png]]
SpringFilterChain中的Security Filter通常上都是bean，因为他们都通过FilterChain而不是DelegatingFilterChain进行注册。
FilterChain提供大量通过Serlvet容器或是DelegationFilterProxy直接注册的方式。
	1. 为Spring Security 的Servlet支持提供了起点
	   如果你想要故障排查Spring Security的Servlet Support，在FilterChainProxy添加断点是个不错的主意。
	2. 因为FilterChain 是Spring Security的用法，能够执行那些不可见的选项的任务。
	   例如，清空SecurityContext避免内存泄漏
	   也应用Spring Security的HttpFirewall保护应用避免某种类型的攻击。
	3. 此外，它在确定何时应调用SecurityFilterChain时提供了更多的灵活性。
	   在一个Servlet容器中，Filter instance仅会被基于URL的方式调用。然而 FilterChainProxy通过使用RequestMatcher Interface在HttpServletRequest中，可以基于任何方式来确定调用。
	   
	![[附件/Pasted image 20230228100749.png]]
	多个SecurityFilterChain实例
	FilterChainProxy决定哪个Security应该被使用，只有第一个匹配的SecurityFilterChain被调用。
	示例图讲解：
	1. 如果请求的Url为/api/** ，那么即使SecurityFilterChainn也符合匹配规则，也只有SecurityFilterChain0被调用。
	2. 如果请求的url为message，SecurityFilterChain0不满足规则，FilterChain将会继续向下匹配，如果没有其他的匹配，那么只有SecurityFilterChainN被调用
	3. 
