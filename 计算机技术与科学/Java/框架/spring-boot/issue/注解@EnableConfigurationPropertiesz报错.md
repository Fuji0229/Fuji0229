Not registered via @EnableConfigurationProperties, marked as Spring component, or scanned via @ConfigurationPropertiesScan.

错误原因：  
当使用@ConfigurationProperties时IDEA顶部出现这样的提示：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200821105351392.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5NTY3OA==,size_16,color_FFFFFF,t_70#pic_center)  
解决方法：  
@ConfigurationProperties使用spring-boot-configuration-processorjar 轻松地从带有注释的项目中生成自己的配置元数据文件 。该jar包含一个Java注释处理器，在项目被编译时会被调用。要使用处理器，请包含对的依赖 spring-boot-configuration-processor。  
1>在配置文件pom.xml中添加依赖：

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

2>回到自定义的bean Person中，添加注解@Component，声明将这个组件添加至容器中，这样才可以被使用  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200821110105295.png#pic_center)  
执行完以上2步，问题解决