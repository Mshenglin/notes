## Zookeeper的概念



## 2 Zookeeper的数据结构

### 2.1数据模型

Zookeeper数据模型是采用层次的多叉树结构，每个节点可以用来存储数据。每个节点都可以拥有多个子节点。这些节点的被称为znode.

### 2.2 znode数据节点

每个节点在Zookeeper中都被称为znode。他是Zookeeper的最小数据单元。

#### 2.2.1znode的数据类型

znode有四种数据类型：

1.持久节点：一旦创建就一直存在，只有进行删除的时候，该节点可以被删除。

2.临时节点：节点的生命周期与客户端的会话（session）绑定。会话消失则节点消失。并且，零时节点只能做叶子节点，不可以做子节点。

3.持久顺序节点：具有持久的特性以外，还有顺序的特性。

4.临时顺序节点：具有零时节点的特性以外，还有顺序性。

#### 2.2.2 znode的数据结构

每个Znode由两个部分构成：

1. stat:状态信息。
2. data：节点存放的数据具体内容。

#### 2.2.3 版本

在stat中会记录该节点的一些信息。比如节点的更新时间、节点的长度、节点的子节点数等。

同时，会在会记录三个相关的版本：

- dataVersion:当前节点的版本号。
- aclVersion:当前znode的ACL版本。
- cversion:当前znode子节点的版本。

#### 2.2.4 Watcher(事件监听器)

![img](https://upload-images.jianshu.io/upload_images/7382796-ae10260074027ac3..png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

#### 2.2.5 会话(Session)

Session可以看成ZooKeeper服务器和客户端之间的一个TCP长连接，通过这个连接，客户端可以心跳检测与服务器保证有效的会话。

## 3 zookeeper的集群

保证高可用，一般是以集群的方式去部署Zookeeper，这样只要集群中的大部分机器还可以使用，Zookeeper本身还可以使用，一般最低三台服务器可以构成一个Zookeeper集群。

![img](https://snailclimb.gitee.io/javaguide/docs/system-design/distributed-system/zookeeper/images/zookeeper集群.png)

每一个Server代表一个安装的Zookeeper服务器，组成集群的服务器都会维护当前状态的服务器状态。集群间通过ZAB协议来保证数据的一致性。

### 3.1 Zookeeper的集群角色

