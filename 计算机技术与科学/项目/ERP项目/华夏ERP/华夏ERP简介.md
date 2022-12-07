华夏ERP 开源的ERP项目

# 技术框架：
- 核心框架 Spring boot 2.0.0
- 持久层框架：Mybatis 1.3.2
- 日志管理：SLF4J 1.7
- 前端框架 Vue 2.6.10
- UI框架：Ant-Design-Vue 1.5.2
- 项目管理框架 Maven 3.2.3

# 开发环境：
* IDE: IntelliJ IDEA 2019.2+和JetBrains WebStorm 2019.3+  
* DB: Mysql 5.7.33  
* JDK: JDK 1.8  
* Node: Node 16.16.0  
* Maven: Maven 3.2.3+  
* Redis: 6.2.1  
* Nginx: 1.12.2

# 服务器环境  
* 数据库：Mysql5.7.33  
* JAVA平台：JRE1.8  
* Redis库：redis6.2.1  
* Nginx代理：nginx1.12.2  
* 操作系统：Windows、Linux等

# 后端项目结构
![[Pasted image 20220905155138.png]]
- config 配置类
- constants 常量
- controller 控制层
- datasource 数据层
	- entitie 实体类
	- mappers mybatis接口
	- vo （Value Object）值对象 用于业务层之间的数据交换
- exception 异常处理
- filter 过滤器
- services 业务层
- utils 工具

![[Pasted image 20220905155733.png]]
mybatis项目的xml映射文件
通过动态代理和xml文件创建mapper接口的实现类