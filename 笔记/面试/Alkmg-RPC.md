## 项目总结

### 项目的基础

##### 1.RPC与HTTP的区别？

RPC和HTTP都是可以用来通信，但是两者首先是功能上是不相同的，RPC可以使用HTTP做通信协议，以及TCP也可以

##### 2.项目中的负载均衡用到哪了？

##### 3.Kryo是如何序列化的？

##### 4.Kryo的性能？

##### 5.服务过多，熔断策略？

##### 6.项目集成spring，如何实现通过注解消费服务？

使用一个@S

##### 7.项目的spring使用机制？

##### 8.项目的亮点？

SPI，使用Spring集成注解消费，netty网络优化

##### 9.一致性哈希在某个节点挂了，如何保证一致性？

##### 10.实现一个高性能的RPC主要是哪方面？

通信协议，IO，服务的容错，服务的治理

##### 11.有没有钩子函数让别人使用？

有，再负载均衡或者

##### 12.一致性哈希比其他方法的优点？

##### 13.常见的RPC框架？

##### 14.服务的注册中心如何做的？

##### 15.为什么Zookeeper更适合作注册中心？

##### 16.Dubbo为什么推荐基于随机的负载均衡？

##### 17.RPC项目中有做单元测试吗？

##### 18.了解Nacos和Zookeeper的区别吗？用的TCP还是HTTP2传输的？

##### 19.动态代理？

##### 20.服务治理？

##### 21.如果一个服务自启动，如何让客户端无感知？

### Netty

##### 1.TCP的拆包和粘包如何解决？

##### 2.Netty底层原理？

##### 3.Netty的网络传输协议？为什么是TCP不是UDP？

##### 4.Netty的特点？

##### 5.为什么选用Netty来做通信框架？还知道其他网络通信框架？能聊聊gRPC和dubbo这两类框架区别？

##### 6.Netty怎么实现高性能的？netty高性能主要依赖了哪些特性？

##### 7.netty bytebuf工作原理，和NIO里buffer区别？

### Zookeeper

##### 1.Zookeeper如何保证一致性？Zab协议？

##### 2.CAP理论？

##### 3.什么是Zookeeper？在分布式中是如何使用的？

Zookeeper是一个分布式的协调服务，给我们提供了高性能、稳定的分布式数据一致性解决方案。Zookeeper是将数据保存在内存之中。在分布式的常见下常用于a、分布式锁。通过创建唯一节点来获取分布式锁，当锁的一方执行完代码或者挂掉之后就释放锁。b、通过 **Watcher 机制** 可以很方便地实现数据发布/订阅。当你将数据发布到 ZooKeeper 被监听的节点上，其他机器可通过监听 ZooKeeper 上节点的变化来实现配置的动态更新。

##### 4.Zookeeper有几种角色？

有三种角色，

##### 5.Zookeeper集群节点宕机了怎么发现剔除的？

##### Zookeeper集群如何实现？

### IO模型

##### 1.IO的多路复用？

##### 2.IO的五大模型？

##### 3.NIO、BIO和AIO？

##### 4.select 、poll和Epoll？

##### 5.JDK中NIO有哪些重要组件？

Buffer、chaell、selector