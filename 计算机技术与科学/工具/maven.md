## 一 . 前言

Maven 是我们常用的依赖管理工具 ,这些年用的也比较多 , 攒了不少笔记 , 整理了一下分享出来 ,也是对之前版图的完善

Maven 的基础概念推荐看 **Maven 权威指南** , 写的很详 . 所以这一篇主要从应用的角度来看各种用法 , 不涉及太多介绍的概念.

## 二 . 常用命令

```
> 打包
mvn clean install -Dmaven.test.skip=true -Dmaven.javadoc.skip=true
// 修改版本号 : versions:set -DnewVersion=5.0.0.1 clean install -f pom.xml -DskipTests    
// mvn clean install -Dmaven.test.failure.ignore=true sonar:sonar    
// mvn clean install -Dmaven.test.failure.ignore=true sonar:sonar -Dsonar.projectKey=proKey -Dsonar.host.url=http://127.0.0.1:8080 -Dsonar.login=dbcbaf5easdasdasdasdasdb42c60cb99    
    
// 跳过 JavaDoc 
> mvn clean install -Dmaven.javadoc.skip=true

// 跳过 CheckStyle
> mvn [target] -Dcheckstyle.skip
// --> 跳过 license : mvn clean install -DskipTests license:format
//
// mvn clean install -Dmaven.test.skip=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip -DskipTests=true

mvn install -Dmaven.test.skip=true -Dmaven.javadoc.skip=true
mvn install -X -Dmaven.test.skip=true -Dmaven.javadoc.skip=true
    
mvn assembly:assembly
    
// 打包打入指定文件
jar -uvf0 test-server-5.2.0.jar BOOT-INF/lib/test-provisioning-java-2.1.2.jar

// 其他生命周期相关命令
mvn clean --> 清空新的项目
mvn compile --> 编译源代码
mvn test-compile --> 编译集成测试
mvn package --> 构建项目
mvn test 

// --> 打包项目并且部署到本地资源库
mvn install
mvn install:install-file -Dfile=algs4.jar
mvn install:install-file -Dfile=algs4.jar -DgroupId=lib.algs4 -DartifactId=algs4 -Dversion=4.0 -Dpackaging=jar　　  
    
// --> 打包并且上传到私服
mvn deploy
mvn clean deploy -Dmaven.test.skip=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip -DskipTests=true
    
// --> 本地加入jar
mvn install:install-file  
-DgroupId=包名  
-DartifactId=项目名  
-Dversion=版本号  
-Dpackaging=jar  
-Dfile=jar文件所在路径  
    
// --> 下载源码
mvn dependency:sources

// --> 下载注释 
mvn dependency:resolve -Dclassifier=javadoc

// --> 部署到Tomcat 
mvn tomcat:redeploy

// --> 跳过测试阶段
mvn package -DskipTests

// --> 临时性跳过测试代码的编译
mvn package -Dmaven.test.skip=true
    
// --> 指定测试类
mvn test -Dtest=RandomGeneratorTest

// --> 以Random开头，Test结尾的测试类
mvn test -Dtest=Random*Test

// --> 用逗号分隔指定多个测试用例 
mvn test -Dtest=ATest,BTest

// --> 运行 checkstyle
mvn checkstyle:checkstyle
    
// --> 打包本地
mvn install:install-file -Dfile=xxx.jar -DgroupId=aaa -DartifactId=bbb -Dversion=1.0.0 -Dpackaging=jar
    
// --> 查看依赖树
mvn dependency:tree    
mvn -X dependency:tree>tree.txt

// --> 查看已解析依赖
mvn dependency:list

// --> 分析依赖关系
mvn dependency:analyze
    
// --> 更新版本 
mvn versions:set -DnewVersion=1.0.1-SNAPSHOT  

// --> 离线模式 (maven-dependency-plugin)
mvn dependency:go-offline
mvn -o verify

复制代码
```

## 三. 基础概念

### 3.1 Maven pom.xml

```
<!-- 基本结构 -->
<!-- -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
</project>
复制代码
```

> 常用变量

```
${basedir} 项目根目录
${project.build.directory} 构建目录，缺省为target
${project.build.outputDirectory} 构建过程输出目录，缺省为target/classes
${project.build.finalName} 产出物名称，缺省为${project.artifactId}-${project.version}
${project.packaging} 打包类型，缺省为jar
${project.xxx} 当前pom文件的任意节点的内容
复制代码
```

> dependency 结构

```

<!-- 典型的 依赖结构 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <type>jar</type>
    <scope>compile</scope>
    <optional>true</optional>
    <exclusions></exclusions>
</dependency>
GroupId / artifactId / version 
packaging : Maven 项目的打包方式
classifier : 用于构建输出附属组件
scope : 依赖的范围
optional : 标记依赖是否可选
exclusions : 用来排除传递性依赖

复制代码
```

> 依赖范围

```
<!-- dependency 依赖范围 -->
<SCOPE>
    - complie : 会在编译 ,测试 ,允许三个阶段均有效
    - test : 该范围在测试的时候才有效 
    - provided : 编译器 , 测试有效 ,运行时无效
    - runtime : 运行时有效 ,编译 测试时无效(例如JDBC)
    - system : 等同于 provided , 仅用于手动编辑本地包
</SCOPE>
复制代码
```

> 生命周期

```
validate - 验证项目是否正确，并提供所有必要信息
compile - 编译项目的源代码
test - 使用合适的单元测试框架测试编译的源代码。这些测试不应要求打包或部署代码
package - 获取已编译的代码并将其打包为可分发的格式，例如JAR。
verify - 对集成测试结果进行任何检查，以确保满足质量标准
install - 将软件包安装到本地存储库中，以便在本地用作其他项目的依赖项
deploy - 在构建环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享。


// 补充 : 
Integration test : 集成测试 , 运行项目的集成测试

复制代码
```

> Maven 的执行过程

```
> clean : clean
> resource : resource
> compiler : compile
> resource : testResource
> compiler : testCompile
复制代码
```

### 3.2 依赖管理的用法

```
<dependencyManagement>
    <dependencies>
        <....>
    </dependencies>
</dependencyManagement>

<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
复制代码
```

### 3.3 依赖传递

```
> 传递性依赖 : 当依赖没有声明依赖范围的时候 ,默认是 complie , 当作用域都处在 complie 时期时(即当项目处在编译器时) , 依赖可以传递
	- scope 同样可以控制依赖传递的范围 , 当 scope 为 test 或者 provide 时 ,只有这2个时期依赖才会传递
	
> 依赖调节 : 基于传递性依赖 ,导致同一个依赖回重复 , 此时存在依赖调节 , 该操作有以下几个原则:
	- 1 路径最近者优先
	- 2 第一声明者优先

> 排除依赖 : <exclusion>
复制代码
```

> 可选依赖的作用 :

```
> 可选依赖 : <optional> true
    - A - B -C(可选)
    - A 依赖 B , B 依赖于 C , 当C 为可选依赖的时候 ,并不是传递到 A
    ? 可选依赖应该避免使用 , 只有当一个项目实现了多个特性的时候 ,才需要实现可选依赖

<dependency>
    <!-- ..-->
    <optional>true</optional>
</dependency>
复制代码
```

### 3.4 Maven 仓库

```
> 路径的生成步骤:
    - 根据 GroupId 准备路径 , formatAsDirectory 将 groupid 的句点分隔符转换为路径
        : org/test/
    - 根据 artifactId 准备路径 ,在上述基础上加上 artifactId + "一个路径分隔符"
        : org/test/gang/
    - 使用版本信息 , 加入 version 和分隔符
        : org/test/gang/1.0.0
    - 构建artifactId全名
        : : org/test/gang/1.0.0/test.jar
    - 加入 classifier
        : org/test/gang/1.0.0/test
    - 如果有extension :
    	
    	
 >  Maven 仓库类型
    - 简单区分 : 远程仓库 - 本地仓库
    - 特殊划分 :
        - 中央仓库 : Maven 核心仓库
        - 私服 : 局域网私有仓库 , 缓存核心仓库以及提交
            : 节省外网宽带
            : 加速Maven构建 
            : 部署第三方控件
            : 提高稳定性 , 增强控制
            : 降低中央仓库负载	
    - 部署到远程仓库:

复制代码
```

### 3.5 Maven Profiles

## 四 . 常用操作

### 4.1 使用根路径

```
// PS : 
 <properties>
    <rootpom.basedir>${basedir}/../..</rootpom.basedir>
  </properties>
复制代码
```

### 4.2 定制本地 jar 包

```
> 定制本地 JAR 库包
	mvn install:install-file -Dfile=c:\kaptcha-2.3.jar -DgroupId=com.google.code 
-DartifactId=kaptcha -Dversion=2.3 -Dpackaging=jar

// 首先 通过 mvn install 安装库 ， 然后pom 调用即可
<dependency>
      <groupId>com.google.code</groupId>
      <artifactId>kaptcha</artifactId>
      <version>2.3</version>
 </dependency>
复制代码
```

### 4.3 依赖本地文件

可以是 lib 文件夹下的 jar 包依赖 , 也可以是指定文件路径下的依赖包

```
<dependency>
    <groupId>ldapjdk</groupId>
    <artifactId>ldapjdk</artifactId>
    <scope>system</scope>
    <version>1.0</version>
    <systemPath>${basedir}\src\lib\ldapjdk.jar</systemPath>
</dependency>
复制代码
```

### 4.4 maven 远程仓库部署 jar

```
<!-- step 1 : 链接到远程仓库 -->
<!-- settings -->
<?xml version="1.0" encoding="utf-8"?>

<servers> 
  <server> 
    <id>mycompany-repository</id>  
    <username>jvanzyl</username>  
    <!-- Default value is ~/.ssh/id_dsa -->  
    <privateKey>/path/to/identity</privateKey> (default is ~/.ssh/id_dsa) 
    <passphrase>my_key_passphrase</passphrase> 
  </server> 
</servers>

<!-- servers example 2 -->
<servers> 
    <server> 
        <id>snapshots</id> 
        <username>server account</username> 
        <password>server password</password> 
    </server>
</servers>


<!-- 要将jar部署到外部存储库，必须在pom.xml中配置存储库url，并在settings.xml中配置用于连接到存储库的身份验证信息。-->
<?xml version="1.0" encoding="utf-8"?>

<distributionManagement> 
  <repository> 
    <id>mycompany-repository</id>  
    <name>MyCompany Repository</name>  
    <url>scp://repository.mycompany.com/repository/maven2</url> 
  </repository> 
</distributionManagement>

<!-- example 2 -->
<distributionManagement>
    <repository>
        <id>snapshots</id>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
    </snapshotRepository>
</distributionManagement>




<!-- 提交 -->
mvn clean package deploy
复制代码
```

#### 4.5 relativePath 指定路径

```
// 该配置用于指定父pom的相对路径地址 , 如果在多聚合项目中 , 需要准确的指出父pom的位置
<relativePath>../parent/pom.xml</relativePath>

 <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>

> 该路需要首先查找的路径 , 该路径的顺序为 :
relativePath -> 本地库 -> 远程库


// 而在部分场景中 ,我们期望不使用相对路径 , 直接从依赖库中查找
<parent>
    <groupId>com.baeldung</groupId>
    <artifactId>external-project</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <relativePath/>
</parent>

复制代码
```

#### 4.6 禁用父 Pom 的 Maven 插件

```
// 如果插件有 skip 功能 , 可以通过 skip 跳过
<plugin>
    <artifactId>maven-enforcer-plugin</artifactId>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>


// 通过将参数设置未不存在的值 ,使其不会执行
<plugin>
    <artifactId>maven-enforcer-plugin</artifactId>
    <executions>
        <execution>
            <id>enforce-file-exists</id>
	    <phase>any-value-that-is-not-a-phase</phase>
	</execution>
    </executions>
</plugin>

复制代码
```

#### 4.7 清空缓存

Maven 会将 jar 包下载到本地中 ,通常的路径如下 :

-   **Windows** : C:\Users<user_name>.m2
-   **Mac** : /Users/<user_name>/.m2
-   **Linux** : /home/<user_name>/.m2

```
// 定位缓存的位置 : 
mvn help:evaluate -Dexpression=settings.localRepository -q -DforceStdout

// 方法一 : 直接手动删除
// 方法二 : 通过插件删除
mvn dependency:purge-local-repository
mvn dependency:purge-local-repository -DactTransitively=false
> 只清空缓存 , 不进行预下载和重新解析
mvn dependency:purge-local-repository -DactTransitively=false -DreResolve=false 
复制代码
```

#### 4.8 Maven log 模式

```
> 打开调试模式 : mvn -X clean compile
> 只记录错误 : mvn --quiet clean compile
> 指定 log 路径 :  mvn --log-file ./mvn.log clean compile
> 输出到文件 : mvn clean compile > ./mvn.log
复制代码
```

#### 4.9 Maven 使用不同版本的 JDK

```
set JAVA_HOME="C:\my\java\jdk-11.0.10"
复制代码
```