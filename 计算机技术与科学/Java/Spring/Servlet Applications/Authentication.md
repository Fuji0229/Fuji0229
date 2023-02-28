## Authentication Mechanisms

-   [Username and Password](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/index.html#servlet-authentication-unpwd) - how to authenticate with a username/password
    
-   [OAuth 2.0 Login](https://docs.spring.io/spring-security/reference/servlet/oauth2/login/index.html#oauth2login) - OAuth 2.0 Log In with OpenID Connect and non-standard OAuth 2.0 Login (i.e. GitHub)
    
-   [SAML 2.0 Login](https://docs.spring.io/spring-security/reference/servlet/saml2/index.html#servlet-saml2) - SAML 2.0 Log In
    
-   [Remember Me](https://docs.spring.io/spring-security/reference/servlet/authentication/rememberme.html#servlet-rememberme) - how to remember a user past session expiration
    
-   [JAAS Authentication](https://docs.spring.io/spring-security/reference/servlet/authentication/jaas.html#servlet-jaas) - authenticate with JAAS
    
-   [Pre-Authentication Scenarios](https://docs.spring.io/spring-security/reference/servlet/authentication/preauth.html#servlet-preauth) - authenticate with an external mechanism such as [SiteMinder](https://www.siteminder.com/) or Java EE security but still use Spring Security for authorization and protection against common exploits.
    
-   [X509 Authentication](https://docs.spring.io/spring-security/reference/servlet/authentication/x509.html#servlet-x509) - X509 Authentication

Servlet Authentication Architecture
-   [SecurityContextHolder](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-securitycontextholder) - The `SecurityContextHolder` is where Spring Security stores the details of who is [authenticated](https://docs.spring.io/spring-security/reference/features/authentication/index.html#authentication).
    
-   [SecurityContext](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-securitycontext) - is obtained from the `SecurityContextHolder` and contains the `Authentication` of the currently authenticated user.
    
-   [Authentication](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-authentication) - Can be the input to `AuthenticationManager` to provide the credentials a user has provided to authenticate or the current user from the `SecurityContext`.
    
-   [GrantedAuthority](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-granted-authority) - An authority that is granted to the principal on the `Authentication` (i.e. roles, scopes, etc.)
    
-   [AuthenticationManager](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-authenticationmanager) - the API that defines how Spring Security’s Filters perform [authentication](https://docs.spring.io/spring-security/reference/features/authentication/index.html#authentication).
    
-   [ProviderManager](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-providermanager) - the most common implementation of `AuthenticationManager`.
    
-   [AuthenticationProvider](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-authenticationprovider) - used by `ProviderManager` to perform a specific type of authentication.
    
-   [Request Credentials with `AuthenticationEntryPoint`](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-authenticationentrypoint) - used for requesting credentials from a client (i.e. redirecting to a log in page, sending a `WWW-Authenticate` response, etc.)
    
-   [AbstractAuthenticationProcessingFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-abstractprocessingfilter) - a base `Filter` used for authentication. This also gives a good idea of the high level flow of authentication and how pieces work together. 
## SecurityContextHolder
Spring Security 认证模式的心脏--SecurityContextHolder
![[附件/Pasted image 20230228140553.png]]
用处：存储通过身份验证的用户详细信息

Example 1. Setting SecurityContextHolder
```java
SecurityContext context = SecurityContextHolder.createEmptyContext();
Authentication authentication =
    new TestingAuthenticationToken("username", "password", "ROLE_USER");
context.setAuthentication(authentication);

SecurityContextHolder.setContext(context);
```

1.多线程环境中避免资源竞争
使用 SecurityContextHolder.getContext().setAuthentication(authentication)

通过SecurityContextHolder获取授权主体的信息
```java
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
String username = authentication.getName();
Object principal = authentication.getPrincipal();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```
SecurityContextHolder在多线程环境下：
	使用SecurityContextHolder.MODE_GLOBAL 策略
	1. 通过设置系统属性
	2. 调用SecurityContextHolder上的静态方法

## SecurityContext
The SecurityContext is obtained from the SecurityContextHolder. The SecurityContext contains an Authentication object.

## Authentication
作用：
	1. 传入AuthenticationManager，提供用户成功认证的证明。
	2. 代表当前授权用户，可以用SecurityContext中获取
The Authentication contains:

- principal: Identifies the user. When authenticating with a username/password this is often an instance of UserDetails.

- credentials: Often a password. In many cases, this is cleared after the user is authenticated, to ensure that it is not leaked.

- authorities: The GrantedAuthority instances are high-level permissions the user is granted. Two examples are roles and scopes.

## GrantedAuthority
授予给主体的权限，通常是ROLE_ADMINISTRATOR 或 ROLE_HR_SUPERVISOR，这些角色将配置为web授权、方法授权和领域对象授权。
作为应用程序范围的权限，不太可能用它表示Emploee对象54的权限，如果有这样成千上万的权限，那么将耗尽机器的内存空间（或者应用花费很长的时间进行用户验证），spring Security 明确设计用于处理这种常见的要求，但是应该使用项目的领域对象的安全新实现此目的。

## AuthenticationManager
定义Spring Security的过滤器如何进行权限验证的API
Spring Security 的过滤器实例调用AuthenticationManager，并且返回Authentication并set至SecurityContextHolder中。
如果想集成Spring Security 的过滤器实例，可以直接set SecurityContextHolder 而无需使用AuthenticationManager。
最常用的实现类是 ProviderManager

## ProviderManager
![[附件/Pasted image 20230228151900.png]]