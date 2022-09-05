注解 ：@Configuration
# 1.实现InitializingBean
Spring 有两种Bean：
	- 工厂Bean FactoryBean 返回的对象是getObject()方法的所有对象
	- 普通Bean
使用场景：
	1. 通过外部对类是否是单例进行控制，该类自己无法感知
	2. 对类的创建之前进行初始化操作，在afterPropertiesSet()中完成
Spring 初始化Bean的方法
	1. 继承InitializingBean类，实现AfterPropertiesSet()方法
	2. 反射机制，xml文件配置init-method标签
相同点：
	都实现了Bean的注入
不同点：
	1.实现的方式不一致
	2. 接口比配置效率高，配置消除了spring的依赖，实现InitializingBean接口依然采用对Spring的依赖
两种方法都实现的时候，如果调用afterPropertiesSet方法时出错，则不调用init-method指定的方法
# 2.继承自PluginApplication（开发者自定义类）
# 3.实现ApplicationContextAware
继承了该类可以方便的获取ApplicationContext对象，Spring容器在创建该Bean后，自动调用该Bean的setApplicationContext(),将Spring容器本身ApplicationContext对象作为参数传递给该方法。
# 4.extends DefaultPluginApplication