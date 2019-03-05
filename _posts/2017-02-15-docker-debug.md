---
layout: post
title: DOCKER DEBUG ONLINE
categories: Docker
description:
keywords:
---

本文介绍一种简单快速地在线调试DOCKER代码的方法

![docker debug](/images/posts/docker_debug.png)

## 调试方式
在阅读Docker源码的时候，有时为了了解代码在运行期的实际行为，对代码做些小改动是一个不错的选择，比如加些日志。为此，需要掌握Docker源码的编译和执行过程。事实上，目前有一个普遍的观点，（Docker的主体部分是Go语言编写的），由于Go语言自身异步的特点，其最好的调试方法不是单线程调试，而是打印日志。

当然，运行Docker的Daemon时加上-D也是一种选择，它提供了最基本的调试手段，当客户端向Daemon发起请求时，在Daemon的控制台可以看到调试信息的输出。

## 调试步骤
### 添加日志打印
   此处为示例，后面章节依据此示例，具体可根据实际情况修改

   在[docker cmd main](https://github.com/yu3peng/docker/blob/dev/cmd/docker/docker.go#L97)中添加如图所示内容
   ![docker debug log](/images/posts/docker_debug_log.jpg)

   在[dockerd cmd main](https://github.com/yu3peng/docker/blob/dev/cmd/dockerd/docker.go#L95)中添加如图所示内容
   ![dockerd debug log](/images/posts/dockerd_debug_log.jpg)

### 创建包含多种工具的docker环境
   * 选择在线docker环境
   相比在本地搭建docker环境，我更乐意选择在线docker环境[docker playground](http://play-with-docker.com)，一来方便，二来其网络速度要快很多。贴张图大家感受一下：
![docker playground speed](/images/posts/docker_playground_speed.jpg)

   * 在[docker playground](http://play-with-docker.com)上clone官方的docker代码

   ```sh
   $ git clone https://github.com/docker/docker.git
   ``` 
   
   * 在docker源码目录下，执行如下命令用以构建Docker镜像

   ```sh
   $ cd docker
   $ docker build -t docker-log-test .
   ``` 
   
   * 启动docker容器
   执行如下命令用以启动Docker镜像

   ```sh
   $ docker run --privileged --rm -ti docker-log-test /bin/bash
   ``` 
   
   此处，参数privileged使容器有权限访问操作系统的Kernel及设备，参数rm指示从/bin/bash终端退出后清除该容器。
   此时，调试代码的docker环境已经准备就绪。

### 编译修改后的代码
   * 执行上一步的命令后，cli会跳转到/go/src/github.com/docker/docker目录，此目录下已经包含了docker的源码，但是此源码是官方docker的代码，并不是需要调试的修改后的代码，将其删除，clone自己分支下的代码

   ```sh
   # cd ../
   # rm -rf docker/
   # git clone https://github.com/yu3peng/docker.git
   ``` 
   
   * 切换到要调试的分支

   ```sh
   # cd docker/
   # git checkout dev
   ``` 
   
   * 编译代码

   ```sh
   # hack/make.sh binary
   ``` 
   
   * 确认容器内docker是否正常工作
   构建成功后应该可以在bundles/1.14.0-dev/binary-XXX目录下找到生成的文件，将生成的二进制docker、dockerd文件复制到/usr/bin下，并将其文件名称重命名为docker、dockerd，然后执行docker –version，以确认容器内的docker是否正常工作。

   ```sh
   # cp bundles/1.14.0-dev/binary-client/docker-1.14.0-dev /usr/bin
   # cp bundles/1.14.0-dev/binary-daemon/dockerd-1.14.0-dev /usr/bin
   # cd /usr/bin
   # mv docker-1.14.0-dev docker
   # mv dockerd-1.14.0-dev dockerd
   ``` 

### 启动Daemon及客户端
   * 以Debug模式启动Docker Daemon：

   ```sh
   # dockerd -D &
   ``` 

   可以看到dockerd添加的打印信息已经显示出来
   ![docker debug log dockerd](/images/posts/docker_debug_log_dockerd.jpg)

   * 确认Docker Daemon运行情况

   ```sh
   # docker version
   ``` 

   可以看到docker添加的打印信息已经显示出来
   ![docker debug log docker](/images/posts/docker_debug_log_docker.jpg)

   * 运行Docker客户端，执行相关操作

   ```sh
   # docker -h
   ······
   ``` 
   