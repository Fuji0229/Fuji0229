1.背景
SpringBoot是对Spring的封装，约定大于配置的思想和自动装配的机制，很多时候只需要引用一个依赖，几乎是零配置就能进行一个技能的装配。
学习自动装配机制，需要先了解spring对于bean的构造生命周期以及各个扩展接口。

2.可扩展的接口启动时序图![[Pasted image 20220911134145.png]]
3.ApplicationContextInitializer
org.springframework.context.ApplicationContextInitializer
在容器刷新前会调用该类的initilize方法，用户可以在此时进行扩展
场景：
	激活配置
	class还没被类加载器加载，进行动态字节码注入
```java
public class TestApplicationContextInitializer implements ApplicationContextInitializer {  
    @Override  
    public void initialize(ConfigurableApplicationContext applicationContext) {  
        System.out.println("[ApplicationContextInitializer]");  
    }  
}
```
三种实现方式：
	-   在启动类中用`springApplication.addInitializers(new TestApplicationContextInitializer())`语句加入
	-   配置文件配置`context.initializer.classes=com.example.demo.TestApplicationContextInitializer`
	-   Spring SPI扩展，在spring.factories中加入`org.springframework.context.ApplicationContextInitializer=com.example.demo.TestApplicationContextInitializer`