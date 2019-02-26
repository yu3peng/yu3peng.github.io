---
layout: post
title: OpenStack & Container
categories: OpenStack
description:
keywords:
---

[WIP] 关注的项目

## OpenStack

### Containerization

目前，容器技术的火爆使沉默已久的PaaS焕发了青春，各种概念曾出不穷，比如CaaS(Container-as-a-Service)，ePaaS(elastic PaaS)，其核心思想就是通过通过Container打包应用。仔细想想，过去几年PaaS一直不瘟不火，或者直白一点在IaaS面前显得那么鸡肋，总结起来主要有以下几点原因：
1. 严重依赖IaaS层的实现，比如需要IaaS层提供服务编排和弹性扩展的功能;
2. 处于SaaS和IaaS的夹缝中，很多功能SaaS层和IaaS实现起来更方便;
3. 没有统一的对外接口，各种平台林立(OpenShift，CloudFundry，GAE，SAE)，接口不统一;
4. 应用上PaaS难，没有规范的应用发布标准。

Container的出现，彻底解决了1和2两个问题，而且提供了更好的用户体验(部署快，性能好)，并且能够反过来蚕食SaaS和IaaS的空间(比如CaaS的概念)

OpenStack没有坐以待毙，而是与容器拥抱，创建了以下的一些项目

1. Zun
[wiki](https://wiki.openstack.org/wiki/Zun)，[github](https://github.com/openstack/zun)

1. Magnum
[wiki](https://wiki.openstack.org/wiki/Magnum/)，[github](https://github.com/openstack/magnum/)

1. Heat
[wiki](https://wiki.openstack.org/wiki/Heat/)，[github](https://github.com/openstack/heat)

1. Murano
[wiki](https://wiki.openstack.org/wiki/Murano)，[github](https://github.com/openstack/murano)

其中Nova-Docker为初次尝试，通过它来支持 Docker 容器，实质是将 Docker 容器作为虚机来管理，但在实际使用中，会发现有不少的问题。毕竟，Nova 设计的初衷是管理虚拟机，而容器跟虚拟机在行为和特性上存在较大的不同，无论是管理层还是底层的虚拟化支持层都完全不同。而且，让 Nova 支持各种各样的容器机制（Docker、OpenVZ、Rocket、LXC 等）要进行修改的地方着实不少，可能跟现有框架形成冲突。

Nova-Docker已经不再维护，取而代之的是Zun项目。

Zun和Nova-Docker的区别是

Zun| Nova-Docker
-------|----------
新增API | 复用Nova的API

Zun和Magnum的区别

Zun| Magnum
-------|----------
提供APIs管理容器 | 提供APIs管理容器编排引擎，例如kubernetes
将容器作为OpenStack的资源管理 | 
可以与OpenStackde其他组件集成，例如Neutron network和Cinder volume | 
为用户提供简单的APIs管理容器，屏蔽了各种容器技术之间的差异 | 



