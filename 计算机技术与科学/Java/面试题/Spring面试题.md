IOC加载过程
重要过程：
	1.读取配置文件并生成bean定义信息  **BeanDefinitionReader**
	2.可以在后置处理器中修改bean内容 实现BeanFactoryPostProcessor接口
	代码示例：
```java
package com.Spring.Boot.init;  
import org.springframework.beans.BeansException;  
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;  
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;  
import org.springframework.stereotype.Component; 
/** 
* 扩展方法--后置增强器（可修改bean的定义信息） 
*/ 
@Component public class ExtBeanFactoryPostProcessor implements BeanFactoryPostProcessor {  

@Override 
	public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {  

// BeanDefinition studentService = beanFactory.getBeanDefinition("studentService"); 

	System.out.println("扩展方法--可进行修改beanDefinition的定义信息");  
	}  
}
```
	3.得到完整的BeanDefinition就可以创建对象了，这个过程被称为Spring Bean的生命周期

Bean对象的
  