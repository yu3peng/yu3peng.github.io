---
layout: post
title: UNIT TESTING ONLINE
categories: Tool
description:
keywords:
---

本文介绍一种在线可视化单元测试方法

![docker debug](/images/posts/unit-testing.jpg)

## 测试工具
  1. [docker playground](http://play-with-docker.com)
  2. [goconvey](https://github.com/smartystreets/goconvey)

## 步骤
### 修改代码
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
   $ docker build -t docker-unit-testing .
   ``` 
   
   * 启动docker容器

   执行如下命令，启动Docker镜像

   ```sh
   $ docker run --privileged --rm -ti -p 8080:80 docker-unit-testing /bin/bash
   ``` 
   
   此处，参数privileged使容器有权限访问操作系统的Kernel及设备，参数rm指示从/bin/bash终端退出后清除该容器。
   
   -p 8080:***80*** 80端口是nginx默认的代理端口。
   
   此时，调试代码的docker环境已经准备就绪。

### 单元测试修改后的代码
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
   由于goconvey只能用http://localhost:8080访问，我们需要借助Nginx，使其在docker playground上呈现。

   * 安装Nginx

   ```sh
   # apt-get install -y nginx
   ``` 

   * 修改配置监听8080端口

   ```sh
   # vim /etc/nginx/nginx.conf
   ```

   在http配置项中，注释掉最后一行

   ```
   # include /etc/nginx/sites-enabled/*;
   ```
   并在下面添加如下内容：

   ```
    server {
        listen 80;
        server_name localhost;
        location / {
            proxy_pass http://localhost:8080;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
   ```
   
   * Nginx重启
   
   ```
   # service nginx restart
   ```
   
   * 安装并运行goconvey.

   ```sh
   # go get github.com/smartystreets/goconvey
   # $GOPATH/bin/goconvey &
   ``` 
   

   在如下页面上点击8080跳转链接
   ![unit_docker_playgroud](/images/posts/unit_docker_playgroud.png)
   
   得到goconvey单元测试页面
   ![unit_goconvey](/images/posts/unit_goconvey.png)
   