注解：

@Configuration  
@EnableSwagger2

Swagger的作用
+ 接口文档 在页面上展示所有的接口信息
+ 即使反馈 更新后只需修改Swagger中的描述
+ 直接调用 通过Swagger可以直接进行接口的调用，降低了项目开发阶段的调试成本

Swagger旧版本的使用
1. 添加依赖
	``` xml
   <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 --> 
   <dependency> 
	   <groupId>io.springfox</groupId> 
	   <artifactId>springfox-swagger2</artifactId> 
	   <version>2.9.2</version> 
   </dependency> 
   <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui --> 
   <dependency> 
	   <groupId>io.springfox</groupId> 
	   <artifactId>springfox-swagger-ui</artifactId> 
	   <version>2.9.2</version> 
	</dependency>
	```
2. 开启Swagger功能
   在 Spring Boot 的启动类或配置类中添加 @EnableSwagger2注释，开启 Swagger
3. 配置Swagger文档摘要
4. 调用接口访问
配置类信息
![[Pasted image 20220905175456.png]]

Swagger最新版使用
1.导入依赖
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-boot-starter --> 
<dependency> 
	<groupId>io.springfox</groupId> <artifactId>springfox-boot-starter</artifactId> <version>3.0.0</version>
</dependency>
2.开启swagger
在 Spring Boot 的启动类或配置类中添加 @EnableOpenApi 注释，开启 Swagger，部分核心代码如下：

```Java
@EnableOpenApi
@SpringBootApplication
public class Application {...
```
3. 配置摘要信息
```java
import org.springframework.context.annotation.Bean; import org.springframework.context.annotation.Configuration; import springfox.documentation.builders.RequestHandlerSelectors; import springfox.documentation.oas.annotations.EnableOpenApi; import springfox.documentation.spi.DocumentationType; import springfox.documentation.spring.web.plugins.Docket; 
@Configuration 
public class SwaggerConfig { 

	@Bean public Docket createRestApi() { 
		return new Docket(DocumentationType.OAS_30) // v2 不同 
.select() 
.apis(RequestHandlerSelectors.basePackage("com.example.swaggerv3.controller")) // 设置扫描路径 .build(); } }
```
PS：OAS 是 OpenAPI Specification 的简称，翻译成中文就是 OpenAPI 说明书。
4.访问Swagger

> 新版本 VS 老版本

新版本和老版本的区别主要体现在以下 4 个方面：
1.  依赖项的添加不同：新版本只需要添加一项，而老版本需要添加两项；
2.  启动 Swagger 的注解不同：新版本使用的是 `@EnableOpenApi`，而老版本是 `@EnableSwagger2`；
3.  Docket（文档摘要信息）的文件类型配置不同：新版本配置的是 `OAS_3`，而老版本是 `SWAGGER_2`；
4.  Swagger UI 访问地址不同：新版本访问地址是“http://localhost:8080/swagger-ui/”，而老版本访问地址是“http://localhost:8080/swagger-ui.html”

  
  