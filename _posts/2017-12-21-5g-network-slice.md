---
layout: post
title: 5G 网络切片
categories: 5G
description:
keywords:
---

5G 网络切片介绍

  ![network-slice](/images/posts/5G-network-slice-logo.jpg)

## 何为网络切片？
  打个比方，车辆是用户，道路是网络。随着车辆的增多，城市道路变得拥堵不堪...
  
  ![traffic](/images/posts/5G-network-slice-traffic.png)
  
  为了缓解交通拥堵，交通部门不得不根据不同的车辆、运营方式进行分流管理，比如设置BRT快速公交通道，非机动车专用通道等…

  ![traffic-1](/images/posts/5G-network-slice-traffic-1.png)
  

  网络亦是如此，要实现从人-人连接到万物连接，连接数量成倍上升，网络必将越来越拥堵，我们就得像交通管理一样，对网络实行分流管理——网络切片。
  
  其实，网络切片并不是一个全新概念，例如VPN就是网络切片的基本版本；本质上网络切片就是将运营商的物理网络划分为多个虚拟网络，每一个虚拟网络根据不同的服务需求，比如时延、带宽、安全性和可靠性等来划分，以灵活的应对不同的网络应用场景。

## 5G的主要应用场景
  具体的讲，5G网络将应对三类场景：移动宽带、大规模物联网和关键任务型物联网。三大应用场景对网络服务的需求是不相同的：
  
### 移动宽带
  
  面向4K/8K超高清视频、全息技术、增强现实/虚拟现实等应用，对网络带宽和速率要求较高。
  
### 大规模物联网
  
  海量的物联网传感器部署于测量、建筑、农业、物流、智慧城市、家庭等领域，这些传感器设备是非常密集的，规模庞大，且大部分是静止的，对时延和移动性要求不高。
  
### 关键任务型物联网
  
  主要应用于无人驾驶、车联网、自动工厂、远程医疗等领域，要求超低时延和高可靠性。

### 解决方案
  这是不是意味着我们需要为每一个服务建设一个专用网络了？例如，一个服务移动宽带，一个服务大规模物联网，一个服务关键任务型物联网。
  
  其实不需要，因为我们可以通过网络切片技术在一个独立的物理网络上切分出多个逻辑的网络，这是一个非常节省成本的做法！
  
  ![5G-network-slice](/images/posts/Network-Slicing-5G-fig-2.png)
  
## 网络切片的构架与挑战
  ![architecture](/images/posts/5G-network-slice-architecture.png)
  
  看完这张图，首先映入脑海的是什么？满满的复杂性和挑战性。
  
  不过总结起来就是“多横三纵”：多横是指应用场景，例如自动驾驶、只能收集、宽带接入、VR以及AR等；三纵是指接入网、核心网、数据网和服务。
  
  1） 在接入网面向用户侧的主要挑战是，某些终端设备（比如汽车）需要要同时接入多个切片网络，另外还涉及鉴权、用户识别等问题。
  
  2） 接入网切片如何与核心网切片配对？接入网切片如何选择核心网切片？
  
  ![match](/images/posts/5G-network-slice-match.jpeg)
  
  ![match-2](/images/posts/5G-network-slice-match-2.jpeg)
  
  3） 编排与自动化是关键。
  所谓编排与自动化包括：
  
  3.1）具备快速创建新服务的能力
  
  3.2）具备应用模板驱动(Template Driven)切片创建环境的能力

  3.3）具备切片参数多样化的能力，以满足不同的商业用例需求

  3.4）具备自动化切片部署的能力

  3.5）具备弹性伸缩能力和还原能力

  3.6）通过服务质量监控，具备动态优化切片性能的能力
  

## 参考资料

1. [NGMN_5G_White_Paper](https://www.ngmn.org/fileadmin/user_upload/NGMN_5G_White_Paper_V1_0_01.pdf)

2. [到底什么叫5G网络切片？](http://www.sohu.com/a/130302264_120921)

3. [网络切片在5G中的应用](https://www.cnblogs.com/sddai/p/6129340.html)

4. [5G系列报告一：千倍容量毫秒时延，万物互联时代即将到来](http://www.sohu.com/a/119472555_481832)