# 1.定义配置文件
统一管理变量，使用yml文件
![[Pasted image 20220906152201.png]]
用 @ConfigurationProperties 代替 @Value
``` java
@Data  
// 指定前缀  
@ConfigurationProperties(prefix = "developer")  
@Component  
public class DeveloperProperty {  
    private String name;  
    private String website;  
    private String qq;  
    private String phoneNumber;  
}

@Data  
// 指定前缀  
@ConfigurationProperties(prefix = "developer")  
@Component  
public class DeveloperProperty {  
    private String name;  
    private String website;  
    private String qq;  
    private String phoneNumber;  
}

//使用时注入这个bean
@RestController  
@RequiredArgsConstructor  
public class PropertyController {  
   
    final DeveloperProperty developerProperty;  
   
    @GetMapping("/property")  
    public Object index() {  
       return developerProperty.getName();  
    }  
}
```
# 2.用@RequiredArgsConstructor代替@Autowired
spring注入一个Bean有三种方式
+ set注入
+ 构造器注入
+ 注解
Spring则推荐使用构造器注入，使用断言来强制依赖
![[Pasted image 20220906160343.png]]
原因是@Autowire可能会导致NPE
```java
public class Car {

    @Autowired
    private Wheel wheel;

    public void run() {
        wheel.roll();
    }
}

//测试代码

Car car = new Car();
car.run();// -> NullPointerException

```
Car允许创建无状态的对象，在构建Car时允许Wheel为空
构造注入
```java
public class Car {

    private final Wheel wheel;

    public Car(Wheel wheel) {
        Assert.notNull(wheel,"Wheel must not be null");
        this.wheel = wheel;
    }

    public void run() {
        wheel.roll();
    }

}
```
构造注入的优点：
+ 1.创建Car时，强制依赖Wheel对象，确保创建Car时每个对象都是有状态的
+ 2.构造器中可以添加对象初始化的逻辑
+ 3.可以清楚的区分对象是通过setter注入的非final对象，还是非强制注入的（final）对象
构造注入代码变得臃肿？
1. 依赖的对象较多时，构造器参数就会很多（考虑单一职责原则，将类拆分为多个类）
2. 使用@Autowire的自动装配会值得依赖对象变得容易，使用构造注入，构造器中参数过多就会有bad smell，需要拆分或者重构代码
3. @Autowire注入的对象无法使用final关键字，必须在构造器初始化才能使用
单元测试
使用@Autowire的单元测试问题
```java
        Wheel wheel = Mock(Wheel);
        Car car = new Car();
        //通过反射来将wheel对象注入到Car对象里
        car.run();
```
通过反射注入到Car对象，单元测试就会显得很繁琐。或者在Car对象里提供Wheel的setter方法。

构造注入的单元测试
```Java
        Wheel wheel = Mock(Wheel);
        Car car = new Car(wheel);
        car.run();
```
单元测试代码简洁，而且在后续开发的过程中，如果没Car对象添加了强制依赖的对象，单元测试也不会出现没有设置的强制依赖项。

Spring的DI设计模式，是将依赖关系的创建和类本身分离，将依赖关系创建的职责交给类注入器做，允许程序设计的松耦合，并遵循单一职责原则和依赖反转职责。因此使用@Autowire自动装配的字段在Spring容器之外无法使用（不包含通过反射设置对象的方式）

构造注入可以在受影响的类中轻松的表明对象的依赖关系，但是@Autowire对象实际对外隐藏了这些关系。需要在对应的类中查看代码才能明确依赖。

# 3. 代码模块化