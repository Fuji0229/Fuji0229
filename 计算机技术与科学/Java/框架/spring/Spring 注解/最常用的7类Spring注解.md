Spring 2.5.x版本之后舍弃xml配置，更多的使用注解的方式控制Spring
# 1.核心注解
## 1.@Required
  用在bean的setter方法上，表示此属性是必须的，必须在配置阶段注入，否则会抛出BeanInitializationExcepion。
## 2.@Autowire
  用于bean的field、setter方法上以及构造方法上，显示的声明依赖，根据type进行注入。
  当在field上使用此注解，并且使用属性来传递值时，Spring会自动把值赋给此field。也可以将此注解用于私有属性(不推荐)，如下。
```java
@Component  
public class User {  
    @Autowired                                 
    private Address address;  
}
```
最经常的用法是setter方法,可以在setter方法中自定义代码
```java
@Component  
public class User {  
  
   private Address address;  
  
   @AutoWired  
   public setAddress(Address address) {  
      // custom code  
      this.address=address;  
   }  
}
```
当在构造方法上使用此注解的时候，需要注意的一点就是一个类中只允许有一个构造方法使用此注解。此外，在Spring4.3后，如果一个类仅仅只有一个构造方法，那么即使不使用此注解，那么Spring也会自动注入相关的bean。如下：
```java
@Component  
public class User {  
  
    private Address address;  
  
    public User(Address address) {  
       this.address=address;  
    }  
}  
  
<bean id="user" class="xx.User"/>
```
## 3.@Qulifier
和@Autowire一起使用，在注入过程中有更多的控制
使用场景：
+ 单个构造器
+ 方法的参数上
例子：当上下文有几个相同的bean，使用@Autowire无法区分，此时可以使用@Qulifier指定名称。
单使用@Autowire注解是By Type的方式进行注入
使用@Autowoire+@Qulifier的当时为By Name的方式进行注入，@Qulifier指定名称
```java
@Component  
public class User {  
  
    @Autowired  
    @Qualifier("address1")  
    private Address address;  
  
    ...  
  
}
```