**http协议** 超文本传输协议，互联网传输中的统一规则，使得遵循这一标准的浏览器都可以实现文本、音视频的发送和接受。
**RPC** 可以基于http 也可以基于自定义协议
RPC调用屏蔽了远程调用的规则，使得不同服务器上的微服务之间互相调用和本地调用的方式一致。

网络模型
OSI网络七层模型
![[附件/Pasted image 20230306145826.png]]
-   **第一层：应用层。**定义了用于在网络中进行通信和传输数据的接口。
-   **第二层：表示层。**定义不同的系统中数据的传输格式，编码和解码规范等。
-   **第三层：[会话层](https://www.zhihu.com/search?q=%E4%BC%9A%E8%AF%9D%E5%B1%82&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2808483275%7D)。**管理用户的会话，控制用户间逻辑连接的建立和中断。
-   **第四层：传输层。**管理着网络中的端到端的数据传输。
-   **第五层：网络层。**定义网络设备间如何传输数据。
-   **第六层：链路层。**将上面的网络层的数据包封装成[数据帧](https://www.zhihu.com/search?q=%E6%95%B0%E6%8D%AE%E5%B8%A7&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2808483275%7D)，便于物理层传输。
-   **第七层：物理层。**这一层主要就是传输这些二进制数据。

RPC 服务
RPC架构
client 
client Stub
Server 
Server Stub
![[附件/Pasted image 20230306150714.png]]

![[附件/Pasted image 20230306150830.png]]
流行的RPC框架
1. GRPC  Netty
2. Thrift 
3. Dubbo
