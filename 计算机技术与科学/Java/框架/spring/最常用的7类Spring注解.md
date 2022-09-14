Spring 2.5.x版本之后舍弃xml配置，更多的使用注解的方式控制Spring
- 1.核心注解
	- 1.@Required
	  用在bean的setter方法上，表示此属性是必须的，必须在配置阶段注入，否则会抛出BeanInitializationExcepion。
	- 2.@Autowire
	  用于bean的field、setter方法上以及构造方法上，显示的声明依赖，根据type进行注入。
	  当在field上使用此注解，并且使用属性来传递值时，Spring会自动把值赋给此field。也可以将此注解用于私有属性(不推荐)，如下。
```java
	  @Component  
public class User {  
    @Autowired                                 
    private Address address;  
}
```
	最经常的用法