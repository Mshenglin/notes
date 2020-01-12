## maven

##### 1，依赖管理：maven工程对jar报的管理

##### 2.maven的安装

##### 3.maven各种仓库之间的联系

仓库分为三类：本地仓库，中央仓库，远程仓库(私服)

##### 4.maven的标准目录结构

```
	a.核心代码部分	src/main/java

​	b.配置文件部分	src/main/resources

​	c.测试代码部分	src/test/java

​	d.测试配置文件部分   src/test/resources
```

##### 5.maven的常用命令

```
mvn clean   ——清除编译文件
mvn compile ——编译Java文件
mvn test ——编译运行测试类
mvn package ——打包项目
mvn install ——安装
```

##### 6.maven的生命周期

```
1.清理生命周期
clean
2.默认生命周期
	a.编译compile
	b.测试test
	c.打包package
	d.安装install
	e.发布deploy
	在执行后面的时候，前面的就一定执行完毕
3.站点生命周期
```

##### 7.maven的概念模型

```
1.依赖管理
 	a.项目对象模型
 	pom.xml ——配置项目运行的自身信息
 	b.依赖管理模型
 	<dependency>
			<groupId>junit</groupId>//公司名称
			<artifactId>junit</artifactId>//项目名
			<version>4.9</version>//版本号
		</dependency>
2.项目的一键构建
	a.编译compile
	b.测试test
	c.打包package
	d.安装install
	e.发布deploy
```

