---
layout: post
title: NFV 术语及定义
categories: NFV
description:
keywords:
---

NFV必读

![definitions](/images/posts/definitions.jpg)

## NFV的术语及定义
### 0~9
 
无。
 
### A~B
 
无。
 
### C
 
（1）CPU（Central Processing Unit，中央处理器）：一种NFV计算域设备，用以提供主要的容器接口。
 
（2）compute domain（计算域）：一种NFVI（笔者注：NFVI即NFV Infrastructure——NFV基础设施）内的域，其中包括各种服务器与存储（设备）。
 
（3）compute node（计算节点）：对于服务器的抽象定义。
 
（4）Connection Point（CP，链接点）：虚拟链接的相应连接点。
 
### D~H
 
无。
 
### I
 
（1）infrastructure network domain（（NFV）基础设施（的）网络域）：一种NFVI内的域，其中包括计算基础设施与存储基础设施（笔者注：即NFVI的计算域）的各种内部数据互联网络。
 
注：（NFV）基础设施（的）网络域（的部署）先于VNFs（笔者注：VNFs即Virtualised Network Function——网络功能虚拟化）的实现。
 
### J~K
 
无。
 
### L
 
（1）lifecycle management（生命周期管理）：对VNF（网络功能虚拟化）或NS（Network Service，网络服务）的实例化、维护以及终结进行管理所需的一组功能。
 
### M
 
（1）measurement（测量）：为得到测量数值或测量结果而执行的一组操作。对相关操作的实际执行可以得到测量数值（基于ISO/IEC 15939:2007: "Systems and software engineering -- Measurement process"《ISO/IEC 15939:2007：系统与软件工程——测量过程》中对于“测量”的定义，NIST Special Publication 500-307: "Cloud Computing Service Metrics Description"《云计算服务度量描述》中有相关的引用）。
 
（2）metric（度量）：（对于）在性能评估以及/或者网络可靠性评估中所产生的数量的标准定义。其具有一种预期效用，并专门用于传递测量数值的准确含义。
 
注：此处的这一定义符合IETF RFC 2330: "Framework for IP Performance Metrics"《IP性能度量框架》以及IETF RFC 6390: "Guidelines for Considering New Performance Metric Development"《（关于）发展新的性能度量（方式）的指导意见》中对于“性能度量”的定义。例如：分组传输性能或者网络的可靠性。
 
### N
 
（1）network controller（网络控制器）：将一个（NFV）网络域中的一些或者全部控制与管理功能集中在一起的功能模块。其可通过已经明确定义好的接口为其他功能模块提供所在（NFV）网络域的（全局）视图。
 
（2）[network forwarding path](http://www.telcocloudbridge.com/beginners-guide-to-network-service-and-vnf-forwarding-graph-in-nfv/)（网络转发路径）：由一系列NF（Network Function，网络功能（模块））所组成的连接点序列表，其中也包含相关的转发策略以及虚拟链路。
 
（3）Network Function（NF，网络功能（模块））：（NFV）网络基础设施中的功能模块。其具有明确定义的各个外部接口以及明确定义的功能性行为。
 
注：实际上，网络功能（模块）与目前的网络节点或者实体设备等同。
 
（4）Network Functions Virtualisation（NFV，网络功能虚拟化）：通过虚拟化的硬件（设备）抽象，将网络功能从其所运行的硬件（设备）中分离出来。
 
（5）Network Functions Virtualisation Infrastructure（NFVI，网络功能虚拟化基础设施）：具有部署网络功能虚拟化的能力的环境中，所有硬件设备与软件组件的统称。
 
注：网络功能虚拟化基础设施NFVI可以跨越若干个物理位置（例如：数据中心运营场所）进行扩展部署，此时，为这些物理站点提供数据连接的网络也被考虑成网络功能虚拟化基础设施NFVI的一部分。在网络功能虚拟化NFV的范畴内，NFVI与VNF均是顶级的概念型实体，而其他的NFV组成部分则是这两大主要实体的子实体。
 
（6）Network Functions Virtualisation Infrastructure（NFVI）components（NFVI组件）：不可以在（网络）现场更换的，但是在制造时可作为COTS（commercial off-the-shelf，商务现货供应）组件以示区分的NFVI硬件资源。
 
（7）Network Functions Virtualisation Infrastructure Node（NFVI-Node，NFVI节点）：以独立实体进行部署与管理的物理设备。NFVI节点为支持虚拟化网络功能VNFs执行环境提供所需的各项NFVI功能。
 
（8）Network Function Virtualisation Infrastructure Point of Presence（NFVI-PoP，NFVI接入点/服务提供点）：以虚拟网络功能模块VNF形式所部属的网络功能模块。
 
（9）Network Functions Virtualisation Management and Orchestration（NFV-MANO，NFV的管理与（业务）编排）：由NFVO（Network Functions Virtualisation Orchestrator，网络功能虚拟化编排器）、VNFM（Network Functions Virtualisation Manager，网络功能虚拟化管理器）以及VIM（Virtualised Infrastructure Manager，虚拟化基础设施管理器）共同完成/提供的功能。
 
（10）Network Functions Virtualisation Management and Orchestration Architectural Framework（NFV-MANO Architectural Framework，NFV-MANO体系结构框架）：所有功能模块（包括NFV-MANO中的功能模块以及与NFV-MANO互通的功能模块）、这些功能模块所使用的数据库、这些功能模块之间用以交换信息（以执行NFV管理与业务编排）的参考点以及接口的集合。
 
（11）Network Functions Virtualisation Orchestrator（NFVO，网络功能虚拟化编排器）：可用以管理NS（Network Service，网络服务）生命周期，并协调NS生命周期的管理、协调VNF（Virtualised Network Function，虚拟化的网络功能）生命周期的管理（需要得到VNF管理器VNFM的支持）、协调NFVI（NFV Infrastructure，网络功能虚拟化基础设施）各类资源的管理（需要得到虚拟化基础设施管理器VIM的支持），以此确保所需各类资源与连接的优化配置。
 
（12）Network Interface Controller（NIC，网络接口控制器）：在NFV计算节点内，提供与（外部）基础设施网络（连接）的物理接口的设备。
 
（13）network operator（网络运营商）：电子通信网络整体或其中一部分的运营商。由此类网络运营商所组成的协会或者组织也可被称为“网络运营商”（参见ETSI Directives: Annex 1: "Definitions in relation to the member categories of ETSI"《ETSI（欧洲电信标准协会）指令：附录1：关于ETSI成员类别的定义》）。
 
（14）Network Point of Presence（N-PoP，网络接入点）：NF（网络功能（模块））作为一个PNF（Physical Network Function，物理网络功能（模块））或者一个VNF（Virtual Network Function，虚拟网络功能（模块））进行部署的（网络）位置。
 
（15）Network Service（NS，网络服务）：一组VNF，VNF转发图，虚拟链接（VL）和连接点（CP）一起形成“网络服务”。
 
注：网络服务有助于高层服务的性能、可靠性以及安全性的提高。端到端网络服务行为的产生需要单个网络功能（模块）行为以及网络基础设施组合机制行为相互结合。
 
（16）network service descriptor（网络服务描述符）：描述网络服务的部署的模板。包括：业务拓扑（虚拟化网络功能的各个组成部分及其相互间的关系、虚拟链路Virtual Links、虚拟化网络功能的转发表）以及网络服务的各项特征（比如服务等级协议SLAs、网络服务实例的运行及生命周期管理所需具备的相关特征）。
 
（17）network service orchestration（网络服务编排）：NFV业务编排器功能的子集，用以进行网络服务的生命周期管理。
 
（18）network service provider（网络服务提供商）：部署网络服务的服务提供商类型。
 
（19）network stability（网络稳定性）：提供相关功能服务，并在非正常情况下（网络过载或者其他未超过设计极限的网络异常）恢复其指定的行为，与此同时，保持NFV（网络功能虚拟化）结构稳定性的能力。
 
（20）NF forwarding graph（网络功能（模块）转发表）：连接网络功能（模块）节点的各条逻辑链路的汇总，用以描述这些网络功能（模块）节点之间的数据流向。
 
（21）NF set（连接网络功能（模块）集）：具有未指定连接的所有网络功能（模块）的集合。
 
（22）NFVI component（网络功能虚拟化基础设施组件）：不可以在（网络）现场更换的，但是在制造时可作为COTS（commercial off-the-shelf，商务现货供应）组件以示区分的NFVI硬件资源。
 
（23）NFV framework（NFV架构/框架）：由ETSI ISG NFV发布的相关规范中所定义的所有实体、参考点、信息模型以及其他组成部分。
 
（24）NFV Infrastructure（NFVI，网络功能虚拟化基础设施）：具有部署网络功能虚拟化的能力的环境中，所有硬件设备与软件组件的统称。
 
注：网络功能虚拟化基础设施NFVI可以跨越若干个物理位置（例如：数据中心运营场所）进行扩展部署，此时，为这些物理站点提供数据连接的网络也被考虑成网络功能虚拟化基础设施NFVI的一部分。在网络功能虚拟化NFV的范畴内，NFVI与VNF均是顶级的概念型实体，而其他的NFV组成部分则是这两大主要实体的子实体。
 
（25）NFV-Resource（NFV-Res，网络功能虚拟化资源）：网络功能虚拟化资源存在于网络功能虚拟化基础设施之中，由VNF/VNSF正确地执行相关功能而使用。
 
（26）NS Catalog（网络服务目录）：所有可用网络服务描述符（NSD）的存储库。
### O
 
无。
 
### P
 
（1）Physical Network Function（PNF，物理网络功能（模块））：专用硬件，提供特定的网络功能，例如防火墙。
 
### Q
 
无。
 
### R
 
（1）resiliency（弹性）：规避网络服务中断，使其回归正常状态的网络功能虚拟化框架/结构的相关能力。或者在出现网络故障、宕机或甚至出现正常操作中断事件时，以最低的、可接受的质量进行服务传送的网络功能虚拟化框架/结构的相关能力。
 
### S
 
（1）scaling（伸缩）：根据需要，为虚拟化网络功能模块动态地分配或回收相关资源的能力。
 
注：“伸缩”包括横向扩展/纵向扩展以及向外扩展/向内扩展。
 
（2）scaling out/in（向外扩展/向内扩展）：通过增加/删除资源实例（例如：VM（虚拟机））来进行伸缩的能力。
 
（3）scaling up/down（横向扩展/纵向扩展）：通过改变所分配的资源（例如：增加/减少内存、CPU能力或存储规模）来进行伸缩的能力。
 
（4）service（业务）：由服务提供商面向用户提供的一系列服务选择组合中的组件，是提供给用户的一种功能。ETSI TR 121 905: "Digital cellular telecommunications system (Phase 2+); Universal Mobile Telecommunications System (UMTS); LTE; Vocabulary for 3GPP Specifications(3GPP TR 21.905)"《数字蜂窝移动通信系统（第二阶段）；通用移动通信系统（UMTS）；LTE；3GPP（移动通信国际标准组织）规范的词汇（3GPP TR 21.905）》中有详细定义。
 
注：“用户”有可能是终端客户，也可能是网络实体或者一些中间实体。
 
（5）service continuity（业务连续性）：按照业务的功能规范及行为规范，以及业务的服务水平协议SLA，进行业务的连续传送。业务连续性既涉及到数据平面，也涉及到控制平面。能保证在出现干扰或异常事件（不论是定期的或不定期的、恶意的、有意的还是无意的）时，能够全部完成所发起的事务或者会话。
 
注1：从终端用户的视角来看，“业务连续性”意味着以多种媒介跨越不同网络域（接入网络、汇聚网络以及核心网）或者不同类型用户终端设备时，正在进行的通信的持续性。
 
注2：端到端的业务持续性则需要按照相关业务的服务水平协议SLA所定义的服务质量对其进行传送。无论是通过非虚拟化的网络来传送，还是通过虚拟化的网络来传送，还是通过这两类网络的融合网络来传送，都必须要遵从这个原则。
 
（6）Service Level Agreement（SLA，服务水平协议）：双方或多方之间的谈判所达成的协议，代表着各方对于服务以及/或者服务行为（比如：可用性、性能、业务连续性、对于网络异常的响应、安全性、可维护性、操作性）的共同理解，并以可测量的目标值表征服务水平。
 
注：上述定义的范畴并不包含服务水平协议SLA的业务环节/方面。
 
（7）service provider（服务提供商）：通过电子通信网络整体或其中一部分向用户提供服务，或者基于某种商业原则向第三方提供服务的公司或者组织。由此类网络运营商所组成的协会或者组织也可被称为“网络运营商”（参见ETSI Directives: Annex 1: "Definitions in relation to the member categories of ETSI"《ETSI（欧洲电信标准协会）指令：附录1：关于ETSI成员类别的定义》）。
 
### T
 
（1）tenant domain（出租域）：提供虚拟化的网络功能模块VNFs，或者将虚拟化的网络功能融入到Network Services（网络服务）之中的域。出租域负责上述两项功能的管理以及业务编排——包括对它们进行功能配置，以及在应用层对它们进行维护等。
 
### U
 
（1）user service（用户业务）：由服务提供商提供给终端用户/客户/订户（订阅用户）的一系列服务选择组合中的组件。
 
### V
 
（1）Virtual Application（VA，虚拟应用）：对可以被装载到虚拟机VM的一种软件的更为普遍（适用）的术语。
 
注：一个VNF（虚拟化的网络功能模块）就是一种类型的虚拟应用VA。
 
（2）virtual link（虚拟链路）：一组连接点，其中包括这些连接点之间的连通性关系以及相关的目标性能指标（比如：带宽、延迟、QoS（服务质量））。
 
注：虚拟链路可以互联两个以上（含）的实体（虚拟化的网络功能模块VNF组件、虚拟化的网络功能模块VNFs或者物理网络功能模块PNF），而且，虚拟链路需要得到网络功能虚拟化基础设施NFVI的VN（Virtual Network，虚拟网络）的支持。
 
（3）Virtual Machine（VM，虚拟机）：一种虚拟化的计算环境，其与实体电脑/服务器的功能行为非常相似。
 
注：一个虚拟机所使用的是其所在的实体电脑/服务器的资源组件（处理器、内存、硬盘存储器、接口、端口等），是通过虚拟机管理程序Hypervisor所产生的——Hypervisor对底层的物理实体资源进行分区，然后分配给各个虚拟机。虚拟机具有承载/代理VNFC（VNF Component，虚拟化的网络功能模块组件）的能力。
 
（4）virtual network（虚拟网络）：虚拟网络在虚拟机VM实例的网络接口以及物理实体网络接口之间对相关的信息进行路由，提供必要的连接。
 
注：虚拟网络是由它的一组许可的网络接口所界定的。
 
（5）virtualisation container（虚拟化容器）：计算节点的分区。虚拟化容器可以被系统分配给单个的、虚拟化的计算环境。
 
注：虚拟化容器的例子包括虚拟机以及OS（操作系统）容器。
 
（6）Virtualisation Deployment Unit（VDU，虚拟化部署单元）：承载虚拟功能的虚拟机。
 
注：在虚拟机管理程序Hypervisor里面，虚拟化部署单元VDU的主要特征是基于能够被映射至单个虚拟机VM的构建体的单个虚拟化网络功能模块VNF实例或者虚拟化网络功能模块VNF子集实例。
 
（7）Virtualised CPU（vCPU，虚拟化的CPU）：虚拟机管理程序Hypervisor为某个虚拟机创建的虚拟化的中央处理器。
 
注：在实际的应用之中，虚拟化的CPU可以是对一个实体CPU以及/或者多核CPUs的时分共享，可以给一个虚拟机VM分配一个核或者多个核。此外，也可能是虚拟机管理程序Hypervisor仿真出一个CPU指令集，从而使得虚拟化的CPU指令集不同于原生的CPU指令集（仿真将会对性能产生重大的影响）。
 
（8）Virtualised Infrastructure Manager（VIM，虚拟化的基础设施管理器）：负责对网络功能虚拟化基础设施NFVI的计算资源、存储资源以及网络资源进行控制与管理的功能模块。虚拟化的基础设施管理器VIM可被部署于基础网络运营商的基础设施域Infrastructure Domain（例如：NFVI接入点/服务提供点）。
 
（9）Virtualised NIC（vNIC，虚拟化的网络接口控制器）：虚拟机管理程序Hypervisor为某个虚拟机创建的虚拟化的网络接口控制器。
 
（10）Virtualised Network Function（VNF，虚拟网络功能）：可以被部署于网络功能虚拟化基础设施NFVI的NF（网络功能）。
 
（11）Virtualised Network Function Instance（VNF Instance，虚拟化的网络功能模块实例）：虚拟化的网络功能软件的运行时间实例化。VNF实例是各个组件及其相互间连接的实例化在完成之后的结果。在相关实例化的过程之中，使用了在VNFD（Virtualised Network Function Descriptor，虚拟化的网络功能模块描述符）之中捕获的虚拟化网络功能部署及运行信息，以及额外的运行时实例特定信息与约束条件。
 
（12）Virtualised Network Function Component（VNFC，虚拟化网络功能组件）：虚拟化网络功能的内部组件，其可为虚拟化网络功能提供商提供相关功能的子集，主要的特征是：该组件的单个实例被 1：1 地映射到单个的虚拟化容器。
 
（13）Virtualised Network Function Component(VNFC) Instance（虚拟化的网络功能模块组件实例）：部署于特定的虚拟化容器实例的虚拟化的网络功能模块组件VNFC的实例。VNFC实例的生命周期管理与其父VNF实例具有相关性。
 
（14）Virtualised Network Function Descriptor（VNFD，虚拟化的网络功能模块描述符）：描述一个虚拟化网络功能模块的部署与操作行为的配置模板。其被用于虚拟化的网络功能模块的运行过程，以及对于虚拟化的网络功能模块实例的生命周期管理。
 
（15）Virtualised Network Function Manager（VNFM，虚拟化的网络功能模块管理器）：[负责 VNF 生命周期管理的功能块](../../../../2018/01/16/nfv-key-definition/#vnfm)。
 
（16）Virtualised Network Function Package（VNF Package，虚拟化的网络功能模块包）：包括对于虚拟化的网络功能模块描述符、与虚拟化的网络功能模块相关的软件镜像/映像、一些额外事项（例如：检查完整性，以证明相关存档的有效性）在内的归档。
 
（17）Virtualised NIC（vNIC，虚拟化的网络接口控制器）：虚拟机管理程序Hypervisor为某个虚拟机创建的虚拟化的网络接口控制器。
 
（18）Virtualised Storage（vStorage，虚拟化的存储）：分配给一个虚拟机VM的虚拟化非易失性存储。
 
（19）Virtualised Switch（vSwitch，虚拟化的交换机）：由虚拟机管理程序Hypervisor所实施的、可以互联虚拟机VM的虚拟化网络接口控制器的以太交换机（可以是各个虚拟化网络接口相互互联，也可以是虚拟交换机的虚拟化网络接口控制器与计算节点的网络接口控制器的互联）。
 
（20）Virtual link （VL，虚拟链路）：提供VNF之间的连接。
 
（21）[VNF Forwarding Graph](../../../../2017/11/23/nfv-link/#vnf-forwarding-graph-vnf-fg)（VNF FG，虚拟网络功能转发表）：一个或一组Network Forwarding Path组成的。
 
（22）VNF Catalog（虚拟网络功能目录）：所有可用VNF描述符（VNFD）的存储库。
 
（23）VNF Set（虚拟化的网络功能模块集）：相互间具有未指定连接的虚拟化网络功能模块的集合。
 
### W到Z
 
无。

## 参考资料

1. [ETSI NFV 003 Terminology for Main Concepts in NFV](http://www.etsi.org/deliver/etsi_gs/NFV/001_099/003/01.03.01_60/gs_nfv003v010301p.pdf)