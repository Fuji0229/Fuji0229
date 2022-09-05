---

mindmap-plugin: basic

---

# MVC

## Model 1 ^93856c81-bdff-aed0
- JSP

## Model 2 ^0261b1c7-baef-bbd4
- JavaBena +Servlet +JSP
- M V C
   - M 模型层
      - 特点：封装数据，少量逻辑处理
      - 机制：view先注册到需要的model，监听数据变化
   - V 视图层
      - 特点：主要是页面，没有程序逻辑
      - 机制：view注册到model实现监听
   - C 控制层
      - 特点：根据用户操作选择不通的M 和 V 进行事件的处理和响应

## Spring MVC ^46406556-16b6-e08f
- MVC优点
   - 分层解耦
      - controller
         - 复用 和 M C 的解耦
      - 多文件
   - 架构 职责分离
      - 便于单元测试，集成部署
      - 使程序员更注重架构
      - 有利于软件的生命周期，后期维护
- MVC 的流程
   - DispatcherServlet
   - HandlerMapping
   - Handler
      - HandlerExecutionChain
   - Controller
   - ModelAndVIew
   - 视图解析器