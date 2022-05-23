
jar包 从.class文件的 main()方法进入
主要过程有
1.加载

2.验证

3.准备
	初始化静态变量
4.解析
	静态链接
	动态链接
5.初始化
	静态变量初始化为准确的值

PS：类加载机制为懒加载


类加载器
引导加载器 jvm jre lib下的 rt.jar 
扩展加载器 jvm jre lib下的ext包 ExtClassLoader
应用程序类加载器 自定义类 classpath下的类   AppClassLoader
自定义加载器  加载用户自定义路径下的类

双亲委派机制