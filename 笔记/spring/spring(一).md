**主要内容**：spring框架的概述以及spring中基于XML的IOC配置。

****

### 1.spring概述

#### 	spring是什么

​			spring是分层的java应用full-stack轻量级的开源框架。以AOP和IOC为内核。

#### 	spring的两大核心

​		AOP(面向切面编程)和IOC

#### 	spring的发展历史和优势

#### 	spring体系结构



### 2.程序的耦合及解耦

​	曾经案例中的问题

​	工厂模式解耦

### 3.IOC的概念和spring中的IOC

​	spring中基于XML的IOC环境搭建

4.依赖注入

5.

#### 工厂模式

##### 创建一个Bean对象的工厂模式

javaBean:用Java语言编写的可重用组件。

是我们创建的service和dao对象。

步骤：

第一个：需要一个配置文件来配置我们的service和dao。配置内容：唯一标识和全限定类名。

第二个：通过读取配置文件的配置的内容，反射创建对象。

#### 使用spring的Ioc解决程序耦合