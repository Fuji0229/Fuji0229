1. 什么是循环依赖
![[附件/Pasted image 20230220180502.png]]
1. 三级缓存 
   + 第一级缓存：singletonObjects 保存实例化，注入，初始化完成的bean实例
   + 第二级缓存：earlySingletonObjects 保存实例化完成的bean实例
   + 第三级缓存：singletonFactories 保存ben创建工厂
     ![[附件/Pasted image 20230220180735.png]]
   + 执行逻辑：
   + 先从 “第一级缓存” 找对象，有就返回，没有就找 “二级缓存”；
   + 找 “二级缓存”，有就返回，没有就找 “三级缓存”；
   + 找 “三级缓存”，找到了，就获取对象，放到 “二级缓存”，从 “三级缓存” 移除。