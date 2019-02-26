---
layout: post
title: NFV 关键定义
categories: NFV
description:
keywords:
---

[WIP] NFV 关键定义

![key](/images/posts/key.jpg)

## virtualisation container

  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义如下：

> **virtualisation container:** partition of a compute node that provides an isolated virtualized computation environment.  
NOTE: Examples of virtualization container includes virtual machine and OS container.

  计算节点的分区，提供独立的虚拟计算环境。

  注意：

  虚拟化容器包含了虚机（virtual machine）和 操作系统级别的容器（OS container），当需要特别指明的时候，请使用 virtual machine 或者 OS container。

  关于 OS container，在 ETSI GS NFV-EVE 004<sup>[[2]](#参考资料)</sup> 中解释如下：

> Container-based virtualisation, also called operating system (OS)-level virtualisation, is an approach to virtualisation which allows multiple isolated user space instances on top of a kernel space within the OS. The isolated guests are called containers.

  在 ETSI GS NFV-EVE 004<sup>[[2]](#参考资料)</sup> 中，将 Docker<sup>TM</sup> 划入了OS Container 的范畴：

> Docker<sup>TM</sup> is an open-source project that provides an additional layer of abstraction and automation of operating-system-level virtualisation on Linux<sup>TM</sup>.

  如果在同一篇文章中出现了 OS container 和 Application container，请提高警惕，前方有雷！
  想要排雷，请看下图：
  ![os-vs-app](/images/posts/os-vs-app-containers.jpg)
  更多解读，请参考 operating-system-containers-vs-application-containers<sup>[[7]](#参考资料)</sup>

## VNFC

  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义如下：

> **Virtualised Network Function Component (VNFC):** internal component of a VNF providing a VNF Provider a defined sub-set of that VNF's functionality, with the main characteristic that a single instance of this component maps 1:1 against a single Virtualisation Container.

  VNF 的内部组件，可为 VNF 提供商提供相关功能的子集，主要的特征是：该组件的单个实例被 1：1 地映射到单个的[虚拟化容器](#virtualisation-container)。

  注意：

  VNFC 的单个实例被 1：1 地映射到单个的虚机（virtual machine）或通常所指的容器（OS container）。

### VNFC Instance
  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义如下：

> **Virtualised Network Function Component (VNFC) Instance:**  instance of a VNFC deployed in a specific Virtualisation Container instance. It has a lifecycle dependency with its parent VNF instance

  部署在特定虚拟化容器实例中的 VNFC 实例。 它与其父 VNF 实例具有生命周期依赖关系。

## VNFD

### 定义

  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义 如下：

> **Virtualised Network Function Descriptor (VNFD):** configuration template that describes a VNF in terms of its deployment and operational behaviour, and is used in the process of VNF on-boarding and managing the lifecycle of a VNF instance

  描述 VNF 的部署与操作行为的配置模板，其被用于 VNF 的运行过程，以及对于 VNF 实例的生命周期管理。

  在 ETSI GS NFV-IFA 011<sup>[[3]](#参考资料)</sup> 中描述 如下：
> A VNFD is a deployment template which describes a VNF in terms of deployment and operational behaviour requirements. It also contains connectivity, interface and virtualised resource requirements. 

  VNFD 是根据部署和操作行为要求描述 VNF 的部署模板。 它包含连接性，接口和虚拟化资源要求。

### 主要内容

* VDU（虚拟化部署单元）：描述部署 VNFC 时 VNFC 的资源需求。

* Connectivity：

  * 外部连接点：描述 VNF 的外部连接点，其中 VDU 的内部连接点作为外部连接点公开，或者外部连接点直接连接到内部虚拟链路。

  * 内部虚拟链路：描述一个或多个 VDU 的虚拟链路。

  * 内部连接点：描述 VDU 的内部连接点。

* 部署风格：根据内部拓扑结构和资源需求，指定 VNF 的给定部署配置。

  * 实例化级别：根据为每个 VDU 创建的 VNFC 实例的数量描述在部署风格内实例化的资源的级别。

  * VDU 配置文件：描述用于特定部署风格的给定 VDU 的附加实例化数据。

  * VL 配置文件：描述用于特定部署风格的给定 [Virtual Links](../../../../2017/11/23/nfv-link/#virtual-links) 的附加实例化数据。

  * VNF LCM 操作配置：表示配置生命周期管理操作的信息。

  * Scaling Aspect：描述用于水平缩放的细节。

更多详细信息可以参考 ETSI GS NFV-SOL 001<sup>[[4]](#参考资料)</sup> 的 A.1	VNFD with deployment flavour modelling design example 章节

### VNFD 的 TOSCA 描述

  在 ETSI GS NFV-SOL 001<sup>[[4]](#参考资料)</sup> 的第6章 VNFD TOSCA model 模型中有具体描述，目前这份标准的版本是 0.5.0，处在早期草案阶段，还未对外公布。

  但是在对外公布的 TOSCA-Simple-Profile-NFV-v1.0<sup>[[6]](#参考资料)</sup> 的第5章 VNF Descriptor Template for NFV 有具体描述，此描述与 ETSI GS NFV-SOL 001<sup>[[4]](#参考资料)</sup> 中的描述一致。

### VNFD 的 YANG 描述

  ETSI GS NFV-SOL 006<sup>[[5]](#参考资料)</sup> 的第6章 VNFD YANG module definitions 是有关的描述，但是目前只有目录结构，并无实质内容，目前这份标准的版本是 0.2.0，处在早期草案阶段，还未对外公布。

## VNF
  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义如下：
  
> **Virtualised Network Function (VNF):** implementation of an NF that can be deployed on a Network Function Virtualisation Infrastructure (NFVI)

  可以被部署于网络功能虚拟化基础设施 NFVI 的 NF（网络功能）

### VNF Instance

  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义如下：

> **Virtualised Network Function Instance (VNF Instance):** run-time instantiation of the VNF software, resulting from completing the instantiation of its components and of the connectivity between them, using the VNF deployment and operational information captured in the VNFD, as well as additional run-time instance-specific information and constraints

  VNF 软件的运行时实例，VNF 实例是各个组件及其相互间连接的实例化在完成之后的结果。在相关实例化的过程之中，使用了在 VNFD（Virtualised Network Function Descriptor，虚拟化的网络功能模块描述符）之中捕获的虚拟化网络功能部署及运行信息，以及额外的运行时实例特定信息与约束条件。

## VNFM

  在 ETSI GS NFV 003<sup>[[1]](#参考资料)</sup> 中定义如下：

> **Virtualised Network Function Manager (VNFM):** functional block that is responsible for the lifecycle management of VNF

  负责 VNF 生命周期管理的功能块

  主要功能如下:

1. 根据 VNF 类型和容量规划
  VNF 所包含的 VNFC 类型、VNFC 数量、VNFC 冗余关系、VNFC 软件信息及软件间关联关系，VNFC 所需的 VM 资源、VNFC 间网络连接关系等。

1. 根据 VNFD 生成对 VIM 的资源需求，规划网元需要的虚拟资源
  根据网元容量、网元模型（例如虚拟网元包含哪些子功能模块及各模块之间的关系）以及虚拟机推导模板，推导出虚拟网网元所需的虚拟机资源描述（Resource Request Description，RRD），在资源预留池中，根据 RRD 向 VIM 申请需要的虚拟资源。

1. VNF生命周期管理，包括以下5个方面。

    实例化： VNF 向N FVO 申请分配 VM 资源， NFVO 分配资源成功后， VNFM 向 VIM 申请创建并启动 VM ，加载相关软件。

    查询：指查询有哪些 VNF 实例。

    扩容和缩容：根据节能策略以及虚拟网元的资源负荷（包括虚拟机资源和物理服务器资源）状况，触发虚拟机扩容和缩容流程，并指示 VIM 申请/释放所需虚拟机资源。

    自愈：指查询有哪些 VNF 实例。

    终止。

1. VNF 所用 NFVI 资源的数据配置（如虚拟机 IP 地址）。

1. VNFI 性能、事件的采集并向 EMS 或 VNF 上报。

1. VNF 业务量、事件的采集。

1. VNFI 事件与 VNF 事件关系的对应。

1. 运行过程中根据 NFVI / VNF 的故障/性能等情况决定 VM 是否迁移，如果判断需要迁移，则向 NFVO 发起迁移请求，由 NFVO 统一协调。

1. 构建 VNF 实例描述 VID（VNF Instance Description）文件
  根据虚拟资源申请结果生成 VID 文件，并传递给 OMU ，由 OMU 引导和监控虚拟网元中的各个虚拟机业务软件版本装载过程。

1. 网络管理。

## 参考资料

[1] [ETSI GS NFV 003 Terminology for Main Concepts in NFV](http://www.etsi.org/deliver/etsi_gs/NFV/001_099/003/01.03.01_60/gs_nfv003v010301p.pdf)

[2] [ETSI GS NFV-EVE 004 Report on the application of Different Virtualisation Technologies in the NFV Framework](http://www.etsi.org/deliver/etsi_gs/NFV-EVE/001_099/004/01.01.01_60/gs_NFV-EVE004v010101p.pdf)

[3] [ETSI GS NFV-IFA 011 VNF Packaging Specification](http://www.etsi.org/deliver/etsi_gs/NFV-IFA/001_099/011/02.04.01_60/gs_nfv-ifa011v020401p.pdf)

[4] [ETSI GS NFV-SOL 001](http://docbox.etsi.org/ISG/NFV/Open/Drafts/SOL001_TOSCA_desc/NFV-SOL001v050.zip)

[5] [ETSI GS NFV-SOL 006](http://docbox.etsi.org/ISG/NFV/Open/Drafts/SOL006_YANG_based_NFV_Descriptors_spec/NFV-SOL006v002.zip)

[6] [TOSCA-Simple-Profile-NFV-v1.0](http://docs.oasis-open.org/tosca/tosca-nfv/v1.0/tosca-nfv-v1.0.html)

[7] [operating-system-containers-vs-application-containers](https://blog.risingstack.com/operating-system-containers-vs-application-containers/)