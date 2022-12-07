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

# 3. 代模块化
阿里巴巴手册：每个方法的代码不要超过50
实际开发中善于对接口或方法进行拆分，做到一个方法只处理一个逻辑。
![[Pasted image 20220906194611.png]]
# 4. 抛异常而不是返回
反例
![[Pasted image 20220906194654.png]]
正例
![[Pasted image 20220906194720.png]]
# 5.减少不必要的DB
尽可能减少对数据库的擦查询
反例
先根据id查询数据库，再删除
![[Pasted image 20220906194935.png]]
正例
![[Pasted image 20220906195012.png]]
# 6.不要返回nul
正例
![[Pasted image 20220906195545.png]]
反例
![[Pasted image 20220906195549.png]]
避免别处调用时出现空指针
# 7.if else
策略模式代替
# 8.减少controller业务代码
反例
![[Pasted image 20220906195849.png]]
正例
![[Pasted image 20220906195857.png]]
# 9.利用好ide
IDE会对我们的代码进行判断，提出合理的建议
![[Pasted image 20220906200016.png]]
![[Pasted image 20220906200023.png]]
# 10.阅读源码
养成阅读源码的好习惯，包括优秀的开源项目Github上star>1000
学习知识，代码的设计模式，高级的API
# 11.设计模式
23种设计模式，尝试用代码中运用设计模式
# 12.拥抱新知识
学习认知之外的知识，不能每天只做CRUD，有机会就用难点的知识，没有机会就下班自己练习
# 基础问题
map遍历
```java
HashMap<String, String> map = new HashMap<>();  
map.put("name", "du");  
for (String key : map.keySet()) {  
    String value = map.get(key);  
}  
  
map.forEach((k, v) -> {  
});  
  
// 推荐  
for (Map.Entry<String, String> entry : map.entrySet()) {  
}
```
optional判空
```java
//获取子目录列表  
public List<CatalogueTreeNode> getChild(String pid) {  
            if (V.isEmpty(pid)) {  
            pid = BasicDic.TEMPORARY_DIRECTORY_ROOT;  
        }  
        CatalogueTreeNode node = treeNodeMap.get(pid);  
        return Optional.ofNullable(node)  
                .map(CatalogueTreeNode::getChild)  
                .orElse(Collections.emptyList());  
    }
```
递归
大数据的递归时，避免在递归方法里面new对象，可以尝试把对象当作方法参数进行传递使用
注释：类 接口 注解 较复杂的方法 注释要写清楚

# 14.判断元素是否存在
hashset而不是list，list判断一个元素是否存在的代码
```java
ArrayList<String> list = new ArrayList<>();  
   
// 判断a是否在list中  
   
for (int i = 0; i < list.size(); i++)  
       if ("a".equals(elementData[i]))  
          return i;
```
复杂度为o(n)
hashset底层采用hashmap作为数据结构存储元素都放到map的key（即链表）
```java
HashSet<String> set = new HashSet<>();  
// 判断a是否在set中  
int index = hash(a);  
return getNode(index) != null
```
复杂度为o(1)