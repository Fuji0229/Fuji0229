1. 什么是mvc？
   m: model 模型 （JavaBean = 领域模型（JavaBean）+业务层+持久层）
   v:view 视图层（html，jsp 等页面）
   c:controller （控制层 接受请求 转发请求）
   
   Model 1时代：
   **JSP**
   例如jsp这种可能会将数据层代码写在视图层的方式，有时甚至需要在视图层嵌入业务逻辑（jsp <% 代码脚本）导致前后端耦合严重。这种架构比较简单，一般适用于小型项目。
   流程图：
   ![[Pasted image 20220905092158.png]]
   
   Model 2时代
   **JavaBean + JSP + Servlet**
   ![[Pasted image 20220905092558.png]]
   
   M：model 数据模型层
   + 职责：负责数据的封装和一些逻辑处理
   + 权限：可以直接访问数据层
   + 特点：和其他层（V C 用户访问）无关联
   + 刷新机制：view注册在model上，view可以观测到model上数据的变化
   
	V：view 视图层
	+ 职责：数据模型的展示，一般为用户界面
	+ 特点：一般无程序逻辑
	+ 监听：需要注册到model上

	C：controller 控制层
	+ 职责：不同层面间的组织作用，用于控制程序的流程，用户行为和数据模型model的响应者

	MVC模式的好处：
	+ 一个model可以被多个view共享（复用，解耦）同样的model层数据不需要因为视图层是Internet或wap而不同。
	+ controller提高了程序的灵活性和可配置性
		+ controller可以通过连接可复用的不同的model和view，来满足用户需求
	+ 低耦合的架构，MVC三个模块相互独立，不受其他模块的影响。
	+ 职责分离，
		+ 产生多文件
		+ 现代软件工程带要求的重要特性：可测试性，各个部门可以独立行使单元测试，有利于企业内部的自动化测试，持续集成和持续发型流程集成，减少应用改版部署所需的时间
	+ 要求开发者思考应用程序的架构（Application Architecture），而非采用大杂烩的方式开发应用程序，对于应用程序的生命周期和后续的可扩展性和可维护性有相当正面的帮助
**Spring MVC 初始化流程**
+ 浏览器发起请求（url）
+ DispatcherServlet接受请求并通过调用HandlerMapping(处理器映射器)拿到对应的Handler，并生成处理器执行链HandlerExecutionChain(包括处理器对象处理器拦截器)，并返回给DispatcherServlet
+ 也就是通过下面这行代码拿到对应的Controller

```xml
    <!--注册一个 Controller供其映射-->
    <bean id="/hello" class="com.molu.controller.MvcController"/>
```
+ DispatcherServlet 根据处理器，得到对应的处理器适配器（HandlerAdapter）。
+ 适配器会按照特定的规则执行Handler，Handler将执行具体的Controller。
+ Cotroller会将执行的结果（比如 ModelAndView）交给HandelerAdapter
+ HandlerAdapter将其结果返回至DispatcherServlet。
+ DispatcherServlet会将该返回结果（MAV）传给视图解析器(ViewReslover)。
+ 视图解析器则会解析MAV（ModelAndView）中的数据，拿到我们封装在MAV中的视图名称
	+ modelAndView.setViewName("hello");
+ 视图解析器对该名称进行拼接操作，并将拼接好的具体视图交给DispatcherServlet
	+ /WEB-INF/jsp/hello.jsp
	+ <!--视图解析器--> 
	+ <bean id="resolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"> 
	+ <!--解析器拼接的视图前缀--> 
	+ <property name="prefix" value="/WEB-INF/jsp/"/> 
	+ <!--解析器拼接的视图后缀--> 
	+ <property name="suffix" value=".jsp"/> </bean>
+ -   DispatcherServlet会对具体的视图进行渲染操作，也就是将模型数据model填充至视图中。
+ -   DispatcherServlet将渲染好的视图返回给客户端（也就是我们看到的浏览器页面）。