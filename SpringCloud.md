# 前置



## 查询boot—cloud对应版本

https://spring.io/projects/spring-cloud#learn

https://start.spring.io/actuator/info

## cloud升级

注册中心

- Eureka（停用）
- Zookeeper（简单替代）
- Consul
- Nacos（推）

服务调用

- Ribbon（将落）
- LoadBalancer（将起）

服务调用2

- Feign（马上停用）
- OpenFeign（无缝替代 ）

服务降级

- Hystrix（停更）
- resilience4j（国外）
- sentienl（国内）

服务网关

- Zuul（停用）
- Zuul2（胎死腹中）
- gateway（推荐）

服务配置

- Config（可用不推荐）
- Nacos（推）

服务总线

- Bus
- Nacos（推）

## 配置

**1.settings三个编码**

![image-20210908192343994](C:\Users\28961\AppData\Roaming\Typora\typora-user-images\image-20210908192343994.png)

**2.打开注解生效激活**

使用lombok需要打开

![image-20210908192745190](C:\Users\28961\AppData\Roaming\Typora\typora-user-images\image-20210908192745190.png)

**3.修改编译的Java版本**

![image-20210908193240100](C:\Users\28961\AppData\Roaming\Typora\typora-user-images\image-20210908193240100.png)

**4.File Types隐藏指定文件**

![image-20210908194154994](C:\Users\28961\AppData\Roaming\Typora\typora-user-images\image-20210908194154994.png)

## dependencyManagement

Maven 使用dependencyManagement 元素来提供了一种管理依赖版本号的方式。
通常会在一个组织或者项目的最顶层的父POM 中看到dependencyManagement 元素。

使用pom.xml 中的dependencyManagement 元素能让所有在子项目中引用一个依赖而不用显式的列出版本号。

Maven 会沿着父子层次向上走，直到找到一个拥有dependencyManagement 元素的项目，然后它就会使用这个
dependencyManagement 元素中指定的版本号。

这样做的好处就是：如果有多个子项目都引用同一样依赖，则可以避免在每个使用的子项目里都声明一个版本号，这样当想升级或切换到另一个版本时，只需要在顶层父容器里更新，而不需要一个一个子项目的修改 ；另外如果某个子项目需要另外的一个版本，只需要声明version就可。

*     dependencyManagement里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。
*   如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，
     才会从父项目中继承该项，并且version和scope都读取自父pom;
*     如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。

## Dao类注解

使用@Repository有时候会有问题，推荐使用@Mapper

