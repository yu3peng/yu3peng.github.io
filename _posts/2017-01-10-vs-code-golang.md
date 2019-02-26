---
layout: post
title: VISUAL STUDIO CODE & GOLANG 开发环境搭建
categories: Tool
description:
keywords:
---

集成开发工具，墙裂推荐

![VSCode_Golang](/images/posts/VSCode_Golang.png)

## 环境要求
* win7或以上
* 64位

## Go安装

1. 下载地址：https://golang.org/dl/ (墙内下载地址http://www.golangtc.com/download)

2. 下载后直接双击msi文件安装，默认安装在c:\go 

3. 安装完成后默认会在环境变量 Path 后添加 Go 安装目录下的 bin 目录 C:\Go\bin\，并添加环境变量 GOROOT，值为 Go 安装根目录 C:\Go\ 

4. 验证是否安装成功，在运行中输入 cmd 打开命令行工具，在提示符下输入 go 

5. 设置工作空间gopath目录(Go语言开发的项目路径)

   Windows 设置如下，新建一个环境变量名称叫做GOPATH，值为你的工作目录，例如笔者的设置GOPATH=e:\mygo

   ![GOPATH](/images/posts/GOPATH.jpg)

   以上 %GOPATH% 目录约定有三个子目录：

   src 存放源代码（比如：.go .c .h .s等）

   pkg 编译后生成的文件（比如：.a）

   bin 编译后生成的可执行文件（为了方便，可以把此目录加入到 windows的PATH 变量中，在环境变量path后追加%GOPATH%\bin）

6. 用go env命令查看环境变量设置

## 安装git版本控制

1. 下载git

   访问https://git-for-windows.github.io/下载安装 

2. 配置账号
  
  1. 访问githubhttps://github.com/注册账号

     github主要作为我们的远程仓库，将本地代码提交到上面，注意注册时填写的email和用户名要和我们本地git配置中的email和用户名相同 
   
   2. 从开始菜单找打git程序打开git bash 

      ![git bash](/images/posts/git_bash.jpg)

      然后输入

      ```
      $ git config --global user.name "Your Name"$ git config --global user.email "email@example.com"
      ```

      ***将上面的your name 和 email@example.com换成你在github上注册的用户名和邮箱***

## 安装vs code及golang插件

1. 下载地址 https://code.visualstudio.com/

2. 安装golang插件

   vs code为开发者提供了很多插件，要想让vs code可以更好的编写go代码，需要安装golang插件

   ![vscode plugin go install](/images/posts/vscode_plugin_go_install.jpg)

3. 安装go插件所需的第三方包

   要想让golang插件实现语法智能提示，debug等，必须安装一些第三方包，安装第三包有两种方式，一种是让软件自动安装，一种是手动安装 

   1. 第一种方式 

      在E:\mygo\src目录下新建hello.go文件，代码如下

      ```go
      package main 

      import "fmt" 

      func main() { 
          fmt.Println("Hello world!") 
      }
      ```

      然后用vs code打开此文件，然后我们会发现在vs code的右下角会出现Analysis Tools Missing，单击即可帮我们安装此插件所需的所有第三方包 

      ***在安装过程中需要下载gorename包(golang.org/x/tools/cmd/gorename)和guru包(golang.org/x/tools/cmd/guru),
      需要翻墙，否则无法下载安装，不会翻墙的童鞋请前往它的github地址 https://godoc.org/golang.org/x/tools，将其git clone下来
      将git clone的文件拷贝到E:\mygo\src\golang.org\x\tools目录***

   2. 第二种方式

      打开命令行提示符，然后粘贴输入如下命令

      ```
      go get -u -v github.com/nsf/gocode
      go get -u -v github.com/rogpeppe/godef
      go get -u -v github.com/golang/lint/golint
      go get -u -v github.com/lukehoban/go-outline
      go get -u -v sourcegraph.com/sqs/goreturns
      go get -u -v golang.org/x/tools/cmd/gorename
      go get -u -v github.com/tpng/gopkgs
      go get -u -v github.com/newhook/go-symbols
      go get -u -v golang.org/x/tools/cmd/guru
      ```

      同样在获取gorename和guru包的时候需要翻墙，解决方法同上

4. vs code配置

   启动vs code后选择文件菜单-》首选项-》工作区设置

   在打开的settings.json文件里粘贴如下代码

   ```
   { 
       "go.buildOnSave": true, 
       "go.lintOnSave": true, 
       "go.vetOnSave": true, 
       "go.buildTags": "", 
       "go.buildFlags": [], 
       "go.lintFlags": [], 
       "go.vetFlags": [], 
       "go.coverOnSave": false, 
       "go.useCodeSnippetsOnFunctionSuggest": false, 
       "go.formatOnSave": true, 
       "go.formatTool": "goreturns", 
       "go.goroot": "C:\\Go",
       "go.gopath": "e:\\mygo", 
       "go.gocodeAutoBuild": false
   }
   ```

重启即可启用vs code飞一般的编写go代码了

## 安装icons插件

   vscode-icons插件，可以为vscode里的不同文件类型提供相应的图标，如下图

   ![vscode plugin icons](/images/posts/vscode_plugin_icons.jpg)

   安装方法与[安装golang插件](#vs-codegolang)相同

   ![vscode plugin icons install](/images/posts/vscode_plugin_icons_install.jpg)

## 安装debug插件delve
   
   执行 go get -u -v github.com/derekparker/delve/cmd/dlv命令，安装调试插件，安装好之后，就可以调试go语言了

   ***delve目前还不支持32位机器***