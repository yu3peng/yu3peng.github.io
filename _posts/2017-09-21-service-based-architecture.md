---
layout: post
title: Service-based Architecture
categories: 5G
description:
keywords:
---

本文介绍基于服务的架构在3GPP 5G 核心网中的应用

![SBA](/images/posts/SBA.png)

## 广义的基于服务的架构
  通常意义上的基于服务的架构，也就是广义的基于服务的架构，它强调将 ***服务*** 作为其架构中的首要组件，***用于实现各种功能***（包括业务层面和非业务层面）。
  它包括SOA、MSA、SBA。具体信息可以参见：[基于服务架构的世界](http://dockone.io/article/1646) 以及[Comparing Service-based Architectures](http://nealford.com/downloads/Comparing_Service-based_Architectures_by_Neal_Ford.pdf)

### SOA
  [面向服务架构（SOA）](https://www.ibm.com/developerworks/cn/webservices/ws-soa-design1/)主张根据层而不是领域来对整体架构进行拆分，导致一个简单的业务变更会影响到多个层，需要更多的测试才能发布出去。
  
  这是一种 ***能共享就共享*** 的架构模式，侧重抽象和业务功能的重用。
  
  ![SOA-detail](/images/posts/SOA-detail.png)
  
#### business services
  业务服务（business services）是一种抽象的、高层级的、粗粒度的服务，定义在企业层面的执行的核心业务操作。
  因为抽象，所以不依赖于任何实现或者协议，一般只包括服务名字，期望的输入以及期望的输出。
  可选地，这些服务类型还可以包括处理步骤或者跟服务相关的特殊编排规则。
  业务服务一般都用XML、Web Services Definition Language（WSDL）或者Business Process Execution Language（BPEL）等语言来表述。
  一般确认某个服务是否属于业务服务会在服务名上下文前后加上“我们是否在做某某的业务”来加以判断。
  例如，有两个服务，分别名为ProcessTrade（处理交易）和InsertCustomer（插入客户）。
  那么“我们是否在做处理交易的业务”可以很清楚看出ProcessTrade是一个业务服务；
  而“我们是否在处理插入客户的业务”听上去就不对，所以不是一个好的业务服务抽象，更像是一个在处理业务服务时所调用的某个具体服务。
  
#### messaging middleware
##### message transformation
  消息转换（message transformation）指的是架构将一种数据格式转换为另外一种格式的能力。
  例如，如下图所示，客户调用服务并以JSON格式发送数据，而服务期望接收的是Java对象。
  消息转换并不关心请求相关的数据本身，而只是关注数据格式的转换。
  微服务架构并不提供这种能力，而SOA架构则通过消息中间件提供这种功能。
  
  ![message-transformation](/images/posts/message-transformation.png)
  
##### message enhancement
  消息增强（message enhancement）指的是架构在请求的数据部分到达服务之前之前对其进行修改、删除或者增加的能力。
  消息增强的例子包括改变日期格式、添加额外数值或者查询数据库进行数据转换，例如将统一安全鉴定程序委员会（Committee on Uniform Security Identification Procedures，CUSIP）代码转换为股票代码或者从后者转换为前者。
  微服务模式不提供这种能力，主要是因为其架构中不包含实现这一功能的中间件组件。
  SOA通过其消息中间件完全支持这种能力。

  下图展示了这种能力。
  注意，图中客户所发送的是CUSIP代码（一种标准交易标识码）和格式为MM/DD/YY的日期数据，而服务期望接收的是证券交易所每日正式牌价表（Stock Exchange Daily Official List，SEDOL）代码（另外一种交易标识码）以及格式为YYYY.MM.DD的日期以及股票代码（假设是股票交易）。
  本例中，消息中间件将CUSIP代码（苹果公司是037833100）转化为SEDOL数（苹果公司是2046251），查询并添加代码（AAPL），将日期从04/23/15转化为2015.04.23。
  
  ![message-enhancement](/images/posts/message-enhancement.png)
  
##### protocol transformation
  协议转换（protocol transformation）所描述的是架构允许客户采用与服务端预期不匹配的协议来调用服务的能力。
  下图展示了这种能力。
  注意，图中服务客户采用REST进行通信，但是负责处理请求的服务要求建立RMI/IIOP链接（例如，Enterprise JavaBeans 3 [EJB3]）和AMQP连接。
  微服务可以支持多种协议类型，但是服务的客户和服务必须采用同一通信协议。
  在SOA架构中，多个协议则可以根据需要混合使用。
  
  ![protocol-transformation](/images/posts/protocol-transformation.png)
  
#### enterprise services
  是具体的、企业层级的、粗粒度的服务，用以实现业务服务所定义的功能。
  它们可以用任何语言和平台进行定制，或者采用第三方采购的产品（COTS）来实现。
  企业服务很独特的一点是它们通常会在组织内共享。
  例如，一个RetrieveCustomer（检索客户）的企业服务可能被组织内很多模块使用，用来接收客户信息。
  其它例如CheckTradeCompliance（检查交易合规） , CreateCustomer（创建客户）, ValidateOrder（验证订单） 和 GetInventory（获取库存目录）等都是企业服务很好的例子。
  企业服务通常依赖应用服务（application services）和基础服务（infrastructure services）来完成特定业务请求。
  但是在某些情况下，某个企业服务也可能把完成特定请求所需要的所有业务功能都归入自身，形成自包含的服务。
  
#### application services
  应用服务（application services）是细粒度的、特定于具体应用的服务，与某个特定应用的语境相关。
  应用服务提供在企业服务中没有的特定的业务功能。
  例如，一个大型保险公司汽车报价应用可能提供服务来计算汽车保险费率。
  这是一个只针对该应用而并不适用于整个企业的服务。
  应用服务可以从某个专用的用户界面直接调用，或者通过某个企业服务调用。
  应用服务的例子包括：AddDriver（添加司机）、AddVehicle（添加车辆）以及CalculateAutoQuote（计算机车报价）等等。
  
#### infrastructure services
  基础服务（infrastructure services），与微服务架构相同，这些服务用于实现非功能性任务，例如审计、安全和日志。在SOA中，基础服务可以从应用服务或者企业服务调用。
  
### MSA
  [微服务架构（MSA）](../../../../2017/05/02/microservice/)带来一些复杂性，比如调用关系链以及网络调用隐含的性能问题。单个微服务不管从业务领域方面还是从性能方面来说都很简单，易于理解。但如果是一群微服务，就不是这么回事了。微服务架构的优点只有在一定条件下才能得以体现，比如每个微服务对它们的数据具有独占所有权，或者有属于自己的数据库存储。

  这是一种 ***能不共享就不共享*** 的架构模式，非常强调[有边界的上下文](../../../../2017/05/02/microservice/#建模)。
  
  ![MSA-detail](/images/posts/MSA-detail.png)
  
#### api layer
  API层作为服务接入层。
  在客户和服务之间放置一个API层通常是个不错的主意，因为这个组件实质上构造了一个抽象层，使得客户不需要知道服务端的确切位置。
  同时也使得服务粒度的改变不会影响到服务客户。对服务粒度进行抽象的确需要在API层提供一定的智能和一定程度的调配能力，但这些问题可以慢慢通过重构解决，重要的是服务端可以根据需要演化，而不是要求服务的客户也经常做出变更。
  
  例如，假设有个服务跟产品订单业务功能有关。
  我们发现它的粒度太粗了，想把它分解成两个粒度更细的服务，从而提高服务的伸缩能力并简化部署。
  如果没有API层来为实际的服务端提供抽象，使用该服务的每个客户都要做出变更，从调用一个服务转为调用两个服务。
  如果使用了API层，服务的客户端就不需要知道（甚至不在乎）同一请求现在将被分解成两个服务调用。

### SBA
  基于服务架构比单体架构或面向服务架构具有更快的交付速度，它使用 ***微服务软件架构*** 和面向领域开发拥护者们所推崇的 ***以领域为中心*** 的方式对代码进行拆分。
  
  面向服务架构主张根据层而不是领域来对整体架构进行拆分，导致一个简单的业务变更会影响到多个层，需要更多的测试才能发布出去。
  
  以领域为中心的架构把测试范围减少到单个要发布的组件上，相比单体架构或面向服务架构交付得更快。
  
  要发布的组件越小，测试范围就越小，这就是微服务优化的目标。
  
  基于服务架构在提升软件交付速度方面也卓有成效。
  
  ![SBA-detail](/images/posts/SBA-detail.png)
  
## 狭义的基于服务的架构
  此处讨论的是3GPP中的基于服务的架构 SBA：***Network function service + Service-based interface***

### 机制
  ![SBA-3GPP](/images/posts/SBA-3GPP.png)
  
  更多信息请参考：[5G Network Architecture:Standard Progress, and Tranfromation to SBA and Network Slicing](http://www.openairinterface.org/docs/workshop/3_OAI_Workshop_20170427/plenary/Wei_Chen_-_5G_Network_Architecture.pdf)

  从上图，我们可以看到 NF Service 具有以下特性：
  1. 自包含
  2. 可复用
  3. 同一个 NF 下的各个 NF Service 独立管理
  
  SBA架构借鉴了IT领域的“微服务”设计理念，从业务的角度，将网络功能定义为多个相对独立、可被灵活调用的服务模块。基于此，运营商可以按照业务需求进行灵活定制组网。

  由此可见，SBA架构就是：把网元按照业务功能的维度进行解耦，形成相互独立、模块化的功能，然后再通过服务化的方式，在统一的构架里按业务需要组织起来，敏捷地支持多种接入方式和多种业务的需求，而且每个功能都可以独立迭代更新，以快速支持新的业务需求。

  从上图中我们可以看到，基于服务的接口”则为“网络功能服务”定义了网络功能间的两种通信方式，分别是“请求--响应”、“订阅--通知”。
  
  这也为 NF Service 提供了两种交互方式：
  
  ![interaction-in-SBA](/images/posts/interaction-in-SBA.png)
  
### 优点

  SBA 被确定为3GPP 5G网络统一/唯一基础架构，它具备多方面的优点：
  1. 网络快速升级
  2. 提升网络资源利用率
  3. 加速网络新能力引入
  4. 在授权的情况下开放给第三方

  总之，3GPP正式确定把SBA作为5G核心网的统一/唯一基础架构，意味着5G网络真正走向开放化、服务化、软件化方向，这就实现了5G网络采用模块化功能设计模式，通过对功能组件的组合，构建满足不同应用场景需求的专用逻辑网络，从而有利于实现5G与垂直行业融合发展。
  
### 影响
  3GPP《5G系统总体架构标准（V15.0.0）》确定把SBA作为5G核心网的统一/唯一基础架构！
  
  ![23501](/images/posts/3GPP-23501.png)
  
  这份3GPP《5G系统总体架构标准》，对5G系统架构、功能、接口关系、流程、漫游、与现有网络共存关系进行标准化，定义了5G系统的第二阶段系统架构。

  其中第一项即与SBA相关，具体内容是：面向5G核心网控制平面应该具有 ***基于服务的接口*** 的SBA架构。

  也就是说，3GPP已经正式把SBA写入/引入到目前最新版本的《5G系统总体架构标准》，SBA成为5G核心网的统一/唯一基础架构。
  
  这是一个重磅消息，因为其重大意义，从下面这两句句话可以看出来：
  
  （1）《5G系统总体架构标准》是3GPP整个5G设计的第一个技术标准，也是事关5G全系统设计的基础性标准。
  
  （2）5G架构和5G核心网的设计对5G网络的管理及运营至关重要，对运营商及整个产业链来说有非常重要的意义。

### 挑战

  挑战主要来自以下两个方面：

#### 1. NF Service 标准化
  由于 NF Service 接口还在标准化中，导致同一个 NF 下的所有 NF Service 只能来自同一个供应商，目前这个问题这在 3GPP Stage 3 中处理。
  
  ![29501](/images/posts/3GPP-29501.png)
  
  这份文档规定了5GC基于SBA的技术实现。

  其他相关文档：

  TS 22.261定义了5G系统的要求。 

  TS 23.501中定义了5G系统架构的要求，TS 23.502中定义了5G系统程序和流程，是TS 23.501的配套规范。

  TS 29.501中规定了5GC SBI API的设计原则和文档指南。

#### 2. SBA 业务架构落地
  
  网络功能服务化的过程，就是细化网络功能的过程，在这个过程中划分出一个个具备不同职责的网络功能业务逻辑模块，每个网络功能业务逻辑模块都会提供相应业务逻辑的服务化接口。

  这些网络功能业务逻辑模块可以独立部署和运行，并提供相应的网络功能业务能力。

  但是每一个网络功能业务逻辑模块只具备相对单一的业务职能，如果要完成整体的网络功能流程和目标，就需要和周围其他的网络功能业务逻辑模块交互。
  
  同时，这个过程中还需要依赖各种与业务无直接关联、相对独立的基础设施和组件，比如数据库、缓存、消息队列等等。

  所以，除了网络功能业务逻辑模块这个实体外，还存在其他各类基础设施和组件实体，如何将这两类实体关联起来，已经超出了网络功能服务化的研究范畴。

### 3GPP 23.501 解析
#### 非漫游状态下的架构以及参考点示意图
  ![SBA-3GPP-5G-Architecture](/images/posts/SBA-3GPP-5G-Architecture.png)
  
  ![reference-point-representation](/images/posts/SBA-3GPP-5G-Architecture-reference-point-representation.png)
  
#### 漫游状态下的架构以及参考点示意图
  ![SBA-3GPP-5G-Architecture-roaming](/images/posts/SBA-3GPP-5G-Architecture-roaming.png)

  ![reference-point-representation-roaming](/images/posts/SBA-3GPP-5G-Architecture-reference-point-representation-roaming.png)

#### 网络功能
##### Network function
###### AF
  Application Function
  
  与3GPP核心网互动，以提供服务，例如支持以下内容：
  * 应用对流量路由的影响
  * 访问网络曝光功能
  * 与策略控制进行互动

  基于运营商部署，允许被运营商信任的应用程序功能，可以直接与相关网络功能进行交互。
  运营商不允许直接访问网络功能的应用功能，应通过NEF使用外部曝光框架与相关网络功能进行交互。
  
###### AMF
  Access and Mobility Management Function
  
  在AMF的单个实例中可以支持部分或全部AMF功能：
  * 终止RAN CP接口（N2）。
  * 终止NAS（N1），NAS加密和完整性保护。
  * 注册管理。
  * 连接管理。
  * 可达性管理。
  * 移动管理。
  * 合法拦截（AMF事件和LI系统接口）。
  * 为UE和SMF之间的SM消息提供传输。
  * 用于路由SM消息的透明代理。
  * 访问身份验证。
  * 访问授权。
  * 为UE和SMSF之间的SMS消息提供传输。
  * 安全锚定功能（SEAF）。它与AUSF和UE交互，接收作为UE认证过程的结果建立的中间密钥。在基于USIM的身份验证的情况下，AMF从AUSF检索安全资料。
  * 安全上下文管理（SCM）。 SCM从SEAF接收用于导出访问网络特定密钥的密钥。
  
  除了上述AMF的功能之外，AMF还可以支持非3GPP接入网络。

###### AUSF
  Authentication Server Function
  
  支持[3GPP TSG SA WG3 Security](http://www.3gpp.org/ftp/tsg_sa/WG3_Security/)规定的认证服务器功能。 
  
###### DN
  Data Network
  
  比如运营商服务、互联网接入和第三方服务。
  
###### NEF
  Network Exposure Function
  
  网络曝光功能（NEF）支持以下功能：
  * 它提供了一种手段，安全地暴露3GPP网络功能提供的服务，例如边缘计算。另外，它为应用功能提供了一种安全地向3GPP网络提供信息的方法，例如，移动模式，通信模式。在这种情况下，NEF可以认证，授权和限制应用功能。
  * AF交换的信息与内部网络功能交换的信息之间进行转换。
  * 网络曝光功能从其他网络功能（基于其他网络功能的暴露功能）接收信息。它可以实现前端（NEF FE），使用统一数据存储库（UDR）（由3GPP定义的接口）的标准化接口将接收到的信息存储为结构化数据。存储的信息可以被NEF访问和“重新曝光”到其他网络功能和应用功能，并用于其他目的，如分析。
  
  应用触发服务流程
  ![Application Trigger service](/images/posts/Application Trigger service.png)
  
  1. NEF（网络曝光功能）接收应用触发请求（外部标识符或MSISDN，请求标识符，有效期，应用端口ID和触发有效负载）消息。
  应用端口ID用于识别UE内的接收应用。有效期指示触发消息有效的时间。
  如果触发消息没有首先到达UE，并且触发消息有效，则仍然可以再次发送触发消息。
  触发有效载荷包含目的地为UE上的应用的信息。
  NEF检查应用程序是否被授权基于请求者标识符发送应用程序触发器。 
  NEF还检查请求者是否超出其触发提交的配额。
  
  2. NEF调用UDM提供的“Get Subscriber Serving NF（External Identifier or MSISDN，NF type）”服务，以确定为UE服务的NF，并获得UE的SUPI。

  3. NEF将应用程序触发器请求（SUPI，NAS Container）发送到AMF。 NAS容器包括在步骤1中接收的应用端口ID和触发有效负载。

  4. AMF向UE发送应用触发请求（NAS容器）。如果UE未连接，则AMF寻呼UE。当UE接收到NAS容器时，应用端口ID标识要通知哪个应用，并将有效载荷发送到应用。
  相应的响应消息被发送回AMF以确认接收到请求消息。
  
  5. NEF从AMF接收应用程序触发响应。
  
  6. NEF将应用程序触发响应发送到应用程序服务器。
  
###### NRF
  Network Repository Function
  
  NF Repository功能（NRF）支持以下功能：
  * 支持服务发现功能。从NF实例接收NF发现请求，并向NF实例提供发现的NF实例（被发现）的信息。
  * 维护可用的NF实例的NF配置文件及其支持的服务。
  
  在NRF中维护的NF实例的信息如下：
  * NF实例ID
  * NF类型
  * PLMN ID
  * 网络切片相关标识符，例如S-NSSAI，NSI ID
  * NF的FQDN或IP地址
  * NF容量信息
  * NF特定服务授权信息
  * 支持的服务名称
  * 每个支持的服务实例的端点信息
  * NF服务有兴趣接收的每种通知类型的其他服务参数，例如DNN，通知端点。
  
  服务注册流程
  
  ![Service-Register](/images/posts/Service-Register.png)
  
  服务注销流程
  
  ![Service-De-Registration](/images/posts/Service-De-Registration.png)
  
  服务发现流程
  
  ![Service-Discovery-in-the-same-PLMN](/images/posts/Service-Discovery-in-the-same-PLMN.png)
  
  ![Service-Discovery-across-PLMNs](/images/posts/Service-Discovery-across-PLMNs.png)
  
  备注：每个PLMN可以由Proxy提供对外SBI接口，不同的PLMN之间，NRF可以互通
  
  服务订阅流程
  
  ![Service-Subscription](/images/posts/Service-Subscription.png)
  
  ![Nnf_Service Subscription](/images/posts/Nnf_Service Subscription.png)
  
  服务去订阅流程
  
  ![Service-Unsubscription](/images/posts/Service-Unsubscription.png)
  
  服务通知流程
  
  ![Service-Notification](/images/posts/Service-Notification.png)
  
  ![Nnf_Service Notification](/images/posts/Nnf_Service Notification.png)
  
  心跳检测流程
  
  ![Nnf_Heartbeat_Request](/images/posts/Nnf_Heartbeat_Request.png)
  
  备注：心跳检测过程中可以传递负载信息
  
###### NSSF
  Network Slice Selection Function
  
  网络切片选择功能（NSSF）支持以下功能：
  * 选择为UE服务的一组网络切片实例。
  * 确定允许的NSSAI（Network Slice Selection Assistance Information）。
  * 确定用于服务于UE的AMF集合，或者基于配置，可能通过查询NRF来选择候选AMF的列表。
  
###### PCF
  Policy Control Function
  
  策略控制功能（PCF）包括以下功能：
  * 支持统一的策略框架来管理网络行为。
  * 提供控制平面功能的策略规则以执行它们。
  * 实施前端（PCF FE）以访问与统一数据存储库（UDR）中的策略决策相关的订阅信息。
  
###### (R)AN
  (Radio) Access Network
  
  5G Access Network。  
  
###### SMF
  Session Management Function
  
  会话管理功能（SMF）包括以下功能。在SMF的单个实例中可以支持部分或全部SMF功能：
  * 会话管理，会话建立，修改和释放，包括UPF和AN节点之间的隧道维护。
  * UE IP地址分配和管理（包括可选授权）。
  * UP功能的选择和控制。
  * 配置UPF的流量转向，将流量路由到正确的目的地。
  * 终止策略控制功能的接口。
  * 控制部分策略实施和QoS。http://nealford.com/downloads/Comparing_Service-based_Architectures_by_Neal_Ford.pdf
  * 合法拦截（SM事件和LI系统接口）。
  * 充电数据收集和支持充电接口。
  * 控制和协调UPF收费数据。
  * 终止SM消息。
  * 下行数据通知。
  * 通过N2通过AMF向AN发送的特定SM信息的发起者。
  * 确定会话的SSC模式。
  * 漫游功能。
  * 处理本地执行应用QoS SLA（VPLMN）。
  * 收费数据收集和计费接口（VPLMN）。
  * 合法拦截（用于SM事件的VPLMN和与LI系统的接口）。
  * 支持与外部DN进行交互，以便通过外部DN传输PDU会话授权/认证信令。
  
  注意：并非所有功能都需要在网络切片的实例中得到支持。
  
###### UDM
  Unified Data Management
  
  统一数据管理（UDM）包括对以下功能的支持：
  * 3GPP AKA认证证书处理。
  * 用户识别处理。
  * 访问授权。
  * 注册/移动管理。
  * 订阅管理。
  * 短信管理。
  
  UDM使用可以存储在UDR中的订阅数据和认证数据，在这种情况下，UDM前端（FE）实现应用程序逻辑，并且不需要内部用户数据存储。 不同的UDM FE可以在不同的交易中为同一用户服务。
  
###### UE
  User Equipment
  
###### UPF
  User plane Function
  
  用户平面功能（UPF）包括以下功能。 在UPF的单一实例中可以支持部分或全部UPF功能：
  * Intra / RAT间移动性的锚点（如适用）。
  * 与数据网络互连的外部PDU会话点。
  * 分组路由和转发。
  * 分组检查和用户平面策略规则实施的一部分。
  * 合法拦截（UP收集）。
  * 流量使用情况报告。
  * 上行链路分类器，用于支持路由到数据网络的流量。
  * 分支点支持多归属PDU会话。
  * 用户平面的QoS处理，例如 包过滤，门控，UL / DL速率执行
  * 上行流量验证（SDF到QoS流映射）。
  * 上行链路和下行链路中的传输级别数据包标记。
  * 下行数据包缓存和下行数据通知触发。
  
  注意：在网络切片的用户平面功能的实例中，不需要支持所有的UPF功能。
  
##### Service-based interface
###### Namf

  Service-based interface exhibited by AMF. NF Services provided by AMF.
  
  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | Namf_Communication|This service enables an NF to communicate with the UE and/or the AN through the AMF.                      |5.2.2.1                   |SMF, SMSF, PCF, NEF, Peer AMF|
  | Namf_EventExposure|This service enables other NFs to subscribe or get notified of the mobility related events and statistics.|5.2.2.2                   |SMF, NEF, PCF, UDM           |
  
###### Nsmf

  Service-based interface exhibited by SMF. NF Services provided by SMF.

  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | Nsmf_PDUSession   |This service manages the PDU sessions and uses the policy and charging rules received from the PCF. The service operations exposed by this NF service allows the consumer NFs to handle the PDU sessions.|5.2.8.2                   |V-SMF, H-SMF, AMF|
  | Nsmf_EventExposure|This service exposes the events happening on the PDU sessions to the consumer NFs.|5.2.8.3                   |PCF, NEF, AMF          |

###### Nnef

  Service-based interface exhibited by NEF. NF Services provided by NEF.
  
  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | Nnef_EventExposure|Provides support for event exposure|                   |Provides support for event exposure|

###### Npcf

  Service-based interface exhibited by PCF. NF Services provided by PCF.
  
  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | Npcf_AMPolicyControl|This PCF service provides access control, network selection and mobility management related policies, UE Route Selection Policies to the NF consumers.|5.2.5                   |AMF|
  | Npcf_SMPolicyControl|This PCF service provides session related policies to the NF consumers.|5.2.5                   |SMF          |
  | Npcf_Policy Authorization|This PCF service authorises an AF request and creates policies as requested by the authorised AF for the PDU session to which the AF session is bound to. This service allows the NF consumer to subscribe/unsubscribe the notification of access type and RAT type, PLMN identifier, access network information, usage report etc.|5.2.5                   |AF, NEF          |
  
###### Nudm

  Service-based interface exhibited by UDM. NF Services provided by UDM.
  
  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | Nudm_UE context management|provide the NF consumer of the information related to UE's transaction information, e.g. UE's serving NF identifier, UE status, etc.|5.2.3                   |AMF, SMF, SMSF|
  | Nudm_Subscriber data management|Allow NF consumer to retrieve user subscription data when necessary|5.2.3                   |AMF, SMF          |
  | Nudm_Authentication|Provide updated authentication related subscriber data to the subscribed NF consumer.|5.2.3                   |AUSF          |
  | Nudm_EventExposure|Allow NF consumer to subscribe to receive an event.Provide monitoring indication of the event to the subscribed NF consumer.|5.4.2                   |NEF          |

###### Nnrf

  Service-based interface exhibited by NRF. NF Services provided by NRF.
  
  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | NF_management|Provides support for Discovery of NF, NF services.|5.2.7.1                   |AMF, SMF, PCF, NEF, NRF, SMSF, AUSF, UDM|
  
###### Nausf

  Service-based interface exhibited by AUSF. NF Services provided by AUSF.
  
  | Service Name      | Description                                                                                              |Reference in TS 23.502 [3]|Example Consumer(s)          |
  | ----------------- |:--------------------------------------------------------------------------------------------------------:| :------------------------:| :---------------------------:|
  | Nausf UEauthentication|The AUSF provides UE authentication service to requester NF|5.2.10.1                   |AMF|

## 参考资料

1. [微服务架构之外的选择——基于服务架构](http://www.infoq.com/cn/news/2016/10/service-based-architecture)

2. [3GPP Identifies Service-Based Architecture Proposed by China Mobile as 5G Unified Infrastructure](http://www.cn-c114.net/576/a1010714.html)

3. [3GPP TS 23.501v1 5G核心网架构和流程](http://www.tech-invite.com/3m23/tinv-3gpp-23-501.html)

4. [再见MME…5G核心网你彻底变了！](http://www.sdnlab.com/19818.html)

5. [鲜枣课堂：5G核心网，到底长啥样？](http://www.eeworld.com.cn/mp/xzclasscom/a51282.jspx)

6. [3GPP 5G UPF](https://www.cnblogs.com/jmilkfan-fanguiju/p/11825022.html)
