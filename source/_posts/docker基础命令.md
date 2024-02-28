---
file-created: 2024 01 20
last-modified: 2024 01 20
title: docker基础使用
date: 2024-01-09 19:02:18
---
<link rel="stylesheet" href="/css/admonition.css">

我是一个学大数据的，免不了解除hadoop，所以我就去网上找hadoop教程了，最终我选择看菜鸟的教程学习。

在大三上我安装了docker，却不知道怎么用，正好借这次在WSL中使通过docker使用hadoop的经历来学习一下docker的基础操作 

突然感觉没必要写全部的，写一点我可能会用到的常用的

首先就是拉镜像了,我是在wsl中操作的，相当于在Linux中操作
```wsl
$ docker pull 镜像名
```
其实我感觉这一步可以省略，因为假如你创造容器时指定的镜像不存在本地时，docker会自动拉。


```shell
# 列出本机运行的容器 -a可以列出停止和运行起来的
$ docker ps 
# 新建容器并且启动
$ docker run [镜像名/镜像ID]
# 启动已终止容器
$ docker start [容器ID]
$ docker stop [容器ID]
$ docker kill [容器ID]
$ docker rm [容器ID]
$ docker restart [容器ID]
```

```shell
# 在名为 [容器ID] 的容器中启动一个 bash shell
# 但是 -i表示 STDIN 打开,即使没有附加到容器终端，也可以读取。
$ docker exec -it [容器ID] bash
```

比如我在我的WSL中通过docker启动了一个mysql容器，名字可以通过
--name参数指定 
例如：`docker run --name = myname [镜像名]`
如果想要连接，还得 指定端口映射(`-p 主机port:容器port`)
以及查看WSL的ip地址 (假如你要在docker主机之外使用时)。 

注意 wsl和你的windows是公用端口号的

我好像在docker的主机上（wsl）也无法连接docker容器中运行的mysql，但是指定端口映射之后，可以通过映射的端口来连接
```wsl
# 查看WSL的ip地址
$ ip addr show eth0
# 新版的linux发行版应该都是这个吧，之前我是用centos虚拟机的时候还是ifconfig呢
```

3307是我端口映射时指定的主机端口
```wsl
# 记得先安装mysql，放心，运行下面的指令出问题会提醒的。
$ mysql -h 172.25.164.50 -P 3307 -u root -p hmall
```

容器也具有IP地址
```shell
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_id
```
可以查看 容器IP地址，但是我没ping通，应该是打开的方式不对哈哈



