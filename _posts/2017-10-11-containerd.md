---
layout: post
title: Containerd的前世今生和未来
categories: Docker
description:
keywords:
---

Containerd的前世今生和未来

  ![containerd](/images/posts/containerd.png)

  [Containerd官网](https://containerd.io)
  
  [Containerd代码](https://github.com/containerd/containerd)

## Containerd的未来
  按照前世今生和未来的顺序，这个章节不是应该放到最后吗？
  
  好菜不怕早，赶紧端上来，言归正传，先来看看Containerd的雄心。
  
  ![containerd-ecosystem](/images/posts/containerd-ecosystem.png)
  
### OCI 标准
  Linux基金会于2015年6月成立[OCI（Open Container Initiative）](https://github.com/opencontainers)组织，旨在围绕容器镜像格式和容器运行时制定一个开放的工业化标准，
  让一个兼容性的容器可以在主要的具有兼容性的操作系统和平台之间进行移植，没有人为的技术屏障。
  
  [OCI](https://github.com/opencontainers) 定义了容器基础设施的规范，获得了众多厂家的支持。
  该组织一成立便得到了包括谷歌、微软、亚马逊、华为等一系列云计算厂商的支持。

### Kubernetes对Containerd的支持
  [Kubernetes孵化项目：通过CRI引入Containerd](https://github.com/kubernetes-incubator/cri-containerd)
  
  为了让Kubernetes在容器运行时使用containerd，需要实现CRI接口。 CRI代表“容器运行时接口”，负责群集上运行的pod和容器的分发以及生命周期管理。
  
  ![kubernetes_containerd](/images/posts/kubernetes_containerd.png)
  
  从这张图，我们可以看到Kubernetes集成Containerd方式的变化，也是Kubernetes摆脱Docker依赖的方式。
  
  虽然目前这个项目还处在孵化阶段，但是 Containerd 已经在2017年3月15日，CloudNativeCon + KubeCon Europe 2017峰会上，被 Docker 捐赠给 CNCF 基金会，
  它和 Kubernetes 都属于 CNCF 基金会下面的项目，一母同胞，何况 Containerd 还有 Docker 撑腰，发展还是可以期待的。
  
### Docker对Containerd的支持
  ![docker_component](/images/posts/docker_component.png)
  
  从这张图中，我们可以看到，Containerd是基于[runC](https://github.com/opencontainers/runc)的，是runC的一个集成。
  
  [runC](https://github.com/opencontainers/runc)是Docker贡献给 [OCI](https://github.com/opencontainers) 组织的，
  按照该开放容器格式标准（OCF, Open Container Format）制定的一种具体实现，用来屏蔽各种操作系统上执行容器的差异，
  这种差异主要体现在各种操作系统的凌乱系统调用和各种驱动程序的不同支持方式。
  
## Containerd的前世
  Containerd的最初目标是解决容器引擎的升级问题，并提供一个代码库。
  
  containerd向上为Docker Engine提供了gRPC接口，使得Docker Engine屏蔽下面的结构变化，确保原有接口向下兼容；向下通过containerd-shim结合runC。
  
  这样使得引擎可以独立升级，避免之前Docker Engine升级会导致所有容器不可用的问题。

## Containerd的今生
  随着Containerd项目的逐步开发，其目标不仅仅只是解决容器引擎的升级问题，也不仅仅只是在runC的基础上抽象出一系列的API。
  
  目前变成了一个具有存储，镜像分发和运行时功能的全功能容器守护进程，以便可以为用户构建一个功能集。
  
  其架构如下图所示：
  ![containerd-architechture](/images/posts/containerd-architechture.png)

## 参考资料

1. [Docker捐出containerd，CoreOS捐出rkt](http://blog.csdn.net/k8scaptain/article/details/68924975)

2. [docker 内部组件结构 -- docker daemon, container,runC](http://www.cnblogs.com/yanjingnan/p/6473831.html)

3. [OCI标准和runC原理解读](http://chuansong.me/n/2032520)

4. [KUBERNETES 1.8 RELEASE INTEGRATES WITH CONTAINERD 1.0 BETA](https://blog.docker.com/2017/09/kubernetes-containerd-integration/)