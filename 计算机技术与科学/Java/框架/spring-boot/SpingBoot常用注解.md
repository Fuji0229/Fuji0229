
1. @SpringBootApplication
	包含的注解：
	1. @configuration 允许在spring上下文注入额外的bean或导入其他配置类
	2. @EnableAutoConfiguration 启用spring的自动配置机制
	3. @ComponentScan   扫描被`@Component` (`@Repository`,`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类
2. Spring Bean 相关
	1. @Autowire
		自动导入类到对象中，被注入的类同样要被spring容器管理
	2. `@Component`,`@Repository`,`@Service`, `@Controller`
	我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：

-   `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
-   `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
-   `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
-   `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。
3. `@RestController`
`@RestController`注解是`@Controller`和`@ResponseBody`的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入 HTTP 响应体中,是 REST 风格的控制器。
单独使用 `@Controller` 不加 `@ResponseBody`的话一般是用在要返回一个视图的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况。`@Controller` +`@ResponseBody` 返回 JSON 或 XML 形式数据
4. `@Scope`
**四种常见的 Spring Bean 的作用域：**

-   singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
-   prototype : 每次请求都会创建一个新的 bean 实例。
-   request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
-   session : 每一个 HTTP Session 会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效
5. `@Configuration`
一般用来声明配置类，可以使用 `@Component`注解替代，不过使用`@Configuration`注解声明配置类更加语义化。
### 3. 处理常见的 HTTP 请求类型

**5 种常见的请求类型:**

-   **GET** ：请求从服务器获取特定资源。举个例子：`GET /users`（获取所有学生）
-   **POST** ：在服务器上创建一个新的资源。举个例子：`POST /users`（创建学生）
-   **PUT** ：更新服务器上的资源（客户端提供更新后的整个资源）。举个例子：`PUT /users/12`（更新编号为 12 的学生）
-   **DELETE** ：从服务器删除特定的资源。举个例子：`DELETE /users/12`（删除编号为 12 的学生）
-   **PATCH** ：更新服务器上的资源（客户端提供更改的属性，可以看做作是部分更新），使用的比较少，这里就不举例子了
#### 3.1. GET 请求

`@GetMapping("users")` 等价于`@RequestMapping(value="/users",method=RequestMethod.GET)`

```
@GetMapping("/users")
public ResponseEntity<List<User>> getAllUsers() {
 return userRepository.findAll();
}
```



 [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_3-2-post-%E8%AF%B7%E6%B1%82)3.2. POST 请求
`@PostMapping("users")` 等价于`@RequestMapping(value="/users",method=RequestMethod.POST)`
关于`@RequestBody`注解的使用，在下面的“前后端传值”这块会讲到。

```
@PostMapping("/users")
public ResponseEntity<User> createUser(@Valid @RequestBody UserCreateRequest userCreateRequest) {
 return userRespository.save(userCreateRequest);
}
```

 [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_3-3-put-%E8%AF%B7%E6%B1%82)3.3. PUT 请求

`@PutMapping("/users/{userId}")` 等价于`@RequestMapping(value="/users/{userId}",method=RequestMethod.PUT)`

```
@PutMapping("/users/{userId}")
public ResponseEntity<User> updateUser(@PathVariable(value = "userId") Long userId,
  @Valid @RequestBody UserUpdateRequest userUpdateRequest) {
  ......
}
```

 [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_3-4-delete-%E8%AF%B7%E6%B1%82)3.4. **DELETE 请求**

`@DeleteMapping("/users/{userId}")`等价于`@RequestMapping(value="/users/{userId}",method=RequestMethod.DELETE)`

```
@DeleteMapping("/users/{userId}")
public ResponseEntity deleteUser(@PathVariable(value = "userId") Long userId){
  ......
}
```

 [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_3-5-patch-%E8%AF%B7%E6%B1%82)3.5. **PATCH 请求**

一般实际项目中，我们都是 PUT 不够用了之后才用 PATCH 请求去更新数据。

```
  @PatchMapping("/profile")
  public ResponseEntity updateStudent(@RequestBody StudentUpdateRequest studentUpdateRequest) {
        studentRepository.updateDetail(studentUpdateRequest);
        return ResponseEntity.ok().build();
    }
```

### 4. 前后端传值

**掌握前后端传值的正确姿势，是你开始 CRUD 的第一步！**

#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_4-1-pathvariable-%E5%92%8C-requestparam)4.1. `@PathVariable` 和 `@RequestParam`

`@PathVariable`用于获取路径参数，`@RequestParam`用于获取查询参数。

举个简单的例子：

```
@GetMapping("/klasses/{klassId}/teachers")
public List<Teacher> getKlassRelatedTeachers(
         @PathVariable("klassId") Long klassId,
         @RequestParam(value = "type", required = false) String type ) {
...
}
```

如果我们请求的 url 是：`/klasses/123456/teachers?type=web`

那么我们服务获取到的数据就是：`klassId=123456,type=web`。

#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_4-2-requestbody)4.2. `@RequestBody`

用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且**Content-Type 为 application/json** 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。系统会使用`HttpMessageConverter`或者自定义的`HttpMessageConverter`将请求的 body 中的 json 字符串转换为 java 对象。

我用一个简单的例子来给演示一下基本使用！

我们有一个注册的接口：

```
@PostMapping("/sign-up")
public ResponseEntity signUp(@RequestBody @Valid UserRegisterRequest userRegisterRequest) {
  userService.save(userRegisterRequest);
  return ResponseEntity.ok().build();
}
```


`UserRegisterRequest`对象：

```
@Data
@AllArgsConstructor
@NoArgsConstructor
public class UserRegisterRequest {
    @NotBlank
    private String userName;
    @NotBlank
    private String password;
    @NotBlank
    private String fullName;
}
```

我们发送 post 请求到这个接口，并且 body 携带 JSON 数据：

```
{"userName":"coder","fullName":"shuangkou","password":"123456"}
```

1  

这样我们的后端就可以直接把 json 格式的数据映射到我们的 `UserRegisterRequest` 类上。

![](https://javaguide.cn/assets/@RequestBody.84a28a13.png)

👉 需要注意的是：**一个请求方法只可以有一个`@RequestBody`，但是可以有多个`@RequestParam`和`@PathVariable`**。 如果你的方法必须要用两个 `@RequestBody`来接受数据的话，大概率是你的数据库设计或者系统设计出问题了！

 [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_5-%E8%AF%BB%E5%8F%96%E9%85%8D%E7%BD%AE%E4%BF%A1%E6%81%AF)5. 读取配置信息

**很多时候我们需要将一些常用的配置信息比如阿里云 oss、发送短信、微信认证的相关配置信息等等放到配置文件中。**

**下面我们来看一下 Spring 为我们提供了哪些方式帮助我们从配置文件中读取这些配置信息。**

我们的数据源`application.yml`内容如下：

```
wuhan2020: 2020年初武汉爆发了新型冠状病毒，疫情严重，但是，我相信一切都会过去！武汉加油！中国加油！

my-profile:
  name: Guide哥
  email: koushuangbwcx@163.com

library:
  location: 湖北武汉加油中国加油
  books:
    - name: 天才基本法
      description: 二十二岁的林朝夕在父亲确诊阿尔茨海默病这天，得知自己暗恋多年的校园男神裴之即将出国深造的消息——对方考取的学校，恰是父亲当年为她放弃的那所。
    - name: 时间的秩序
      description: 为什么我们记得过去，而非未来？时间“流逝”意味着什么？是我们存在于时间之内，还是时间存在于我们之中？卡洛·罗韦利用诗意的文字，邀请我们思考这一亘古难题——时间的本质。
    - name: 了不起的我
      description: 如何养成一个新习惯？如何让心智变得更成熟？如何拥有高质量的关系？ 如何走出人生的艰难时刻？
```


#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_5-1-value-%E5%B8%B8%E7%94%A8)5.1. `@Value`(常用)

使用 `@Value("${property}")` 读取比较简单的配置信息：

```
@Value("${wuhan2020}")
String wuhan2020;
```

#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_5-2-configurationproperties-%E5%B8%B8%E7%94%A8)5.2. `@ConfigurationProperties`(常用)

通过`@ConfigurationProperties`读取配置信息并与 bean 绑定。

```
@Component
@ConfigurationProperties(prefix = "library")
class LibraryProperties {
    @NotEmpty
    private String location;
    private List<Book> books;

    @Setter
    @Getter
    @ToString
    static class Book {
        String name;
        String description;
    }
  省略getter/setter
  ......
}
```

你可以像使用普通的 Spring bean 一样，将其注入到类中使用。

#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_5-3-propertysource-%E4%B8%8D%E5%B8%B8%E7%94%A8)5.3. `@PropertySource`（不常用）

`@PropertySource`读取指定 properties 文件

```
@Component
@PropertySource("classpath:website.properties")

class WebSite {
    @Value("${url}")
    private String url;

  省略getter/setter
  ......
}
```

更多内容请查看我的这篇文章：[《10 分钟搞定 SpringBoot 如何优雅读取配置文件？》open in new window](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486181&idx=2&sn=10db0ae64ef501f96a5b0dbc4bd78786&chksm=cea2452ef9d5cc384678e456427328600971180a77e40c13936b19369672ca3e342c26e92b50&token=816772476&lang=zh_CN#rd) 。

### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_6-%E5%8F%82%E6%95%B0%E6%A0%A1%E9%AA%8C)6. 参数校验

**数据的校验的重要性就不用说了，即使在前端对数据进行校验的情况下，我们还是要对传入后端的数据再进行一遍校验，避免用户绕过浏览器直接通过一些 HTTP 工具直接向后端请求一些违法数据。**

**JSR(Java Specification Requests）** 是一套 JavaBean 参数校验的标准，它定义了很多常用的校验注解，我们可以直接将这些注解加在我们 JavaBean 的属性上面，这样就可以在需要校验的时候进行校验了，非常方便！

校验的时候我们实际用的是 **Hibernate Validator** 框架。Hibernate Validator 是 Hibernate 团队最初的数据校验框架，Hibernate Validator 4.x 是 Bean Validation 1.0（JSR 303）的参考实现，Hibernate Validator 5.x 是 Bean Validation 1.1（JSR 349）的参考实现，目前最新版的 Hibernate Validator 6.x 是 Bean Validation 2.0（JSR 380）的参考实现。

SpringBoot 项目的 spring-boot-starter-web 依赖中已经有 hibernate-validator 包，不需要引用相关依赖。如下图所示（通过 idea 插件—Maven Helper 生成）：

**注**：更新版本的 spring-boot-starter-web 依赖中不再有 hibernate-validator 包（如2.3.11.RELEASE），需要自己引入 `spring-boot-starter-validation` 依赖。

![](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/2021/03/c7bacd12-1c1a-4e41-aaaf-4cad840fc073.png)

非 SpringBoot 项目需要自行引入相关依赖包，这里不多做讲解，具体可以查看我的这篇文章：《[如何在 Spring/Spring Boot 中做参数校验？你需要了解的都在这里！open in new window](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485783&idx=1&sn=a407f3b75efa17c643407daa7fb2acd6&chksm=cea2469cf9d5cf8afbcd0a8a1c9cc4294d6805b8e01bee6f76bb2884c5bc15478e91459def49&token=292197051&lang=zh_CN#rd)》。

👉 需要注意的是： **所有的注解，推荐使用 JSR 注解，即`javax.validation.constraints`，而不是`org.hibernate.validator.constraints`**

#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_6-1-%E4%B8%80%E4%BA%9B%E5%B8%B8%E7%94%A8%E7%9A%84%E5%AD%97%E6%AE%B5%E9%AA%8C%E8%AF%81%E7%9A%84%E6%B3%A8%E8%A7%A3)6.1. 一些常用的字段验证的注解

-   `@NotEmpty` 被注释的字符串的不能为 null 也不能为空
-   `@NotBlank` 被注释的字符串非 null，并且必须包含一个非空白字符
-   `@Null` 被注释的元素必须为 null
-   `@NotNull` 被注释的元素必须不为 null
-   `@AssertTrue` 被注释的元素必须为 true
-   `@AssertFalse` 被注释的元素必须为 false
-   `@Pattern(regex=,flag=)`被注释的元素必须符合指定的正则表达式
-   `@Email` 被注释的元素必须是 Email 格式。
-   `@Min(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
-   `@Max(value)`被注释的元素必须是一个数字，其值必须小于等于指定的最大值
-   `@DecimalMin(value)`被注释的元素必须是一个数字，其值必须大于等于指定的最小值
-   `@DecimalMax(value)` 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
-   `@Size(max=, min=)`被注释的元素的大小必须在指定的范围内
-   `@Digits(integer, fraction)`被注释的元素必须是一个数字，其值必须在可接受的范围内
-   `@Past`被注释的元素必须是一个过去的日期
-   `@Future` 被注释的元素必须是一个将来的日期
-   ......

#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_6-2-%E9%AA%8C%E8%AF%81%E8%AF%B7%E6%B1%82%E4%BD%93-requestbody)6.2. 验证请求体(RequestBody)

```
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {

    @NotNull(message = "classId 不能为空")
    private String classId;

    @Size(max = 33)
    @NotNull(message = "name 不能为空")
    private String name;

    @Pattern(regexp = "((^Man$|^Woman$|^UGM$))", message = "sex 值不在可选范围")
    @NotNull(message = "sex 不能为空")
    private String sex;

    @Email(message = "email 格式不正确")
    @NotNull(message = "email 不能为空")
    private String email;

}
```

我们在需要验证的参数上加上了`@Valid`注解，如果验证失败，它将抛出`MethodArgumentNotValidException`。

```
@RestController
@RequestMapping("/api")
public class PersonController {

    @PostMapping("/person")
    public ResponseEntity<Person> getPerson(@RequestBody @Valid Person person) {
        return ResponseEntity.ok().body(person);
    }
}
```


#### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_6-3-%E9%AA%8C%E8%AF%81%E8%AF%B7%E6%B1%82%E5%8F%82%E6%95%B0-path-variables-%E5%92%8C-request-parameters)6.3. 验证请求参数(Path Variables 和 Request Parameters)

**一定一定不要忘记在类上加上 `@Validated` 注解了，这个参数可以告诉 Spring 去校验方法参数。**

```
@RestController
@RequestMapping("/api")
@Validated
public class PersonController {

    @GetMapping("/person/{id}")
    public ResponseEntity<Integer> getPersonByID(@Valid @PathVariable("id") @Max(value = 5,message = "超过 id 的范围了") Integer id) {
        return ResponseEntity.ok().body(id);
    }
}
```

更多关于如何在 Spring 项目中进行参数校验的内容，请看《[如何在 Spring/Spring Boot 中做参数校验？你需要了解的都在这里！open in new window](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485783&idx=1&sn=a407f3b75efa17c643407daa7fb2acd6&chksm=cea2469cf9d5cf8afbcd0a8a1c9cc4294d6805b8e01bee6f76bb2884c5bc15478e91459def49&token=292197051&lang=zh_CN#rd)》这篇文章。

### [#](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_7-%E5%85%A8%E5%B1%80%E5%A4%84%E7%90%86-controller-%E5%B1%82%E5%BC%82%E5%B8%B8)7. 全局处理 Controller 层异常

介绍一下我们 Spring 项目必备的全局处理 Controller 层异常。

**相关注解：**

1.  `@ControllerAdvice` :注解定义全局异常处理类
2.  `@ExceptionHandler` :注解声明异常处理方法

如何使用呢？拿我们在第 5 节参数校验这块来举例子。如果方法参数不对的话就会抛出`MethodArgumentNotValidException`，我们来处理这个异常。

```
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

    /**
     * 请求参数异常处理
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<?> handleMethodArgumentNotValidException(MethodArgumentNotValidException ex, HttpServletRequest request) {
       ......
    }
}
```


更多关于 Spring Boot 异常处理的内容，请看我的这两篇文章：

1.  [SpringBoot 处理异常的几种常见姿势open in new window](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485568&idx=2&sn=c5ba880fd0c5d82e39531fa42cb036ac&chksm=cea2474bf9d5ce5dcbc6a5f6580198fdce4bc92ef577579183a729cb5d1430e4994720d59b34&token=2133161636&lang=zh_CN#rd)
2.  [使用枚举简单封装一个优雅的 Spring Boot 全局异常处理！](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486379&idx=2&sn=48c29ae65b3ed874749f0803f0e4d90e&chksm=cea24460f9d5cd769ed53ad7e17c97a7963a89f5350e370be633db0ae8d783c3a3dbd58c70f8&token=1054498516&lang=zh_CN#rd)