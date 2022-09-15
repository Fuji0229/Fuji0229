为什么使用Spring Security？
1.日常开发的业务系统或多或少有安全性的技术要求，最常用的就是用户认证和访问授权。
2.Spring Security在日常开发过程中可以和Spring boot项目无缝继承，而且是Spring cloud 等综合性开发框架的底层基础框架之一。

Spring Security提供的场景
1.用户信息管理
2.用户认证
3.权限控制
4.跨站点请求伪造保护
5.跨域支持
6.全局安全方法
7.单点登录

Spring Security与单体应用
资源：
	 对外暴露的HTTP端点
认证：
	你是谁？确认用户信息（根据账号密码等信息）
授权：
	根据用户信息获取的角色信息，判断用户是否具有资源的操作权限

Spring Security与微服务架构
资源服务器
客户端
授权中心
过程：
	授权中心获取用户信息并生成token（具有过期时间和权限），此时资源服务器携带token访问资源服务器，资源服务器则通过对token的解析判断是否能够访问该资源。
加入生成和验证令牌的授权中心，需要引入Oauth2.0协议，在客户端和资源服务器之间加入了授权中心，保证令牌能在不通微服务之间进行传递。

Spring Security与响应式编程
	Spring 5的发布，迎来了核心响应式编程（Reactive Programming），Spring Boot从2.x开始也依赖于Spring 5。在Spring Security中，用户体系的建立，用户的认证和授权、方法级别的安全访问、Oauth2.0协议等传统开发模式下具备的安全性功能也都具备对应的响应式编程。
Spring Security配置体系
继承WebSecurityConfigurationAdapter类，重写configure()方法进行配置