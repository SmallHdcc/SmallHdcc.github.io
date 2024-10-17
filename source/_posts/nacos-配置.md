---
title: nacos 配置
date: 2024-02-28 18:30:33
tags:
  - 工具配置
categories:
  - 技术
---
<link rel="stylesheet" href="/css/admonition.css">

### 关于中间件

  就算是还要很多年之后才能去做微服务或者根本不做，我觉得学一下这些中间件也都是有必要的。
比如 docker，k8s,当然它们两个应该不能算中间件，它们一个是工具，一个是管理工具的工具，注册中心nacos，zookeeper，dubbo( rpc 框架), ElaskSearch 它们好像不仅仅可以用在服务端开发上，只能说我自己的眼界窄了。

有一说一 docker 确实好用，哈哈哈哈，不过我也就算是基础使用

!!! info 注册中心
    微服务的每一个功能都对应着一个服务，它们之间有些服务会互相依赖，在不同的机器上部署服务，首先应该想到的就是通过网络，响应哪一门语言都提供了并发编程的工具包
    同样的，既然使用了微服务，那说明项目很大，可能每一个服务都部署在不止一台服务器上，既然如此，仅依靠java原生的网络编程包 如何做负载均衡，难道要搞一个集合，集合里面的是要依赖服务的ip地址吧？ 那就扯淡了，服务多，我都不敢想得写多少集合，麻烦的一。
    最佳做法就是将服务交给一个第三方模块，由它来管理服务，将服务提供给调用者，让调用者不需要关注生产者的情况，这个第三方最好还能做负载均衡
    注册中心就干这事！



### 配置nacos

!!! info 环境
    Ubuntu22.04  使用 fish 作为 shell。
    (fish有命令提示，真棒！)
    
用的黑马的配置文件

如果想让nacos正常工作，mysql 也必须要启动，nacos的数据库应该也必须启动吧，大概.....

在需要用到nacos的微服务中
首先需要引入依赖

```xml
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
```
引入依赖之后，在Spring的配置中

```yml
    Spring:
        cloud:
          nacos:
            discovery:
              server-addr: your_nacos_address (ip:port)
```

按照这样做之后，理论上在浏览器的网址中就可以访问到nacos的页面了
默认情况下 账号和密码都是 nacos

进去之后，点击侧边栏的服务列表，刷新之后应该就可以看到我们在nacos中注册的服务了
服务的名字应该是 在Spring 的 配置中配置的名字
!!! info 服务名
    ``` yml
        Spring:
          application:
                  name: your_service_name
    ```

!!! note 然后呢？
    nacos 或者说注册中心 只是让我不必关注服务提供者的状态和地址了，但是我还得远程调用

!!! warning 使用docker时遇到的问题
    我的 mysql 也是部署在docker上的，但是它居然会自动关闭！
    这个时候需要重新启动mysql，再启动nacos，不然nacos不会正常工作
    要保证 mysql 实在 nacos 之前启动的， 而且邪门的在于不能在 docker desktop 重启
    还必须使用命令行！
    当然这对于我来说不是什么麻烦事，只是觉得奇怪。


### Dubbo 与 openFeign

!!! info openFeign
    openFeign 是简化远程调用的声明式http客户端框架，而 Dubbo 与它功能相似，是一个 RPC 框架

SpringCloud 高度继承openFeign，经过配置之后，可以简化向注册中心调用服务的代码
    
