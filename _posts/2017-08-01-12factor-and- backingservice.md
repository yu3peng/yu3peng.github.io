---
layout: post
title: Twelve-Factor and Backing Service
categories: Architecture
description:
keywords:
---

本文介绍云原生的Twelve-Factor、它的扩展以及实现其中一个Factor：后端支撑服务的具体方式

![twelve-factor-applications](/images/posts/twelve-factor-applications.jpg)

## Twelve-Factor
### 背景介绍
  ***云原生*** 应用是指 ***出生*** 在云里的应用。

#### 娘胎：IaaS

  当本地应用迁移到公有云时，第一步就是把它们重新安置到云上。举个例子：如果一个公司将一个100节点的集群从本地迁移到云，它将会租用100个实例，以和本地完全相同的方式运行应用和服务（安装相同版本的操作系统以及支撑软件）。
  这种租用基础设施的方式被称为基础设施即服务，也就是IaaS。
  
  IaaS提供的好处是双重的：缩放和抽象。
  缩放的好处体现在，机器可以按需添加和删除。这个过程只需点击一个按钮，而在本地情况下则需要几周的时间。
  抽象的好处体现在硬件/数据中心基础设施上。IaaS提供全球基础设施，不需要在同一地理区域建立和维护多个数据中心。
  有了IaaS，也无需担心管理机器、机架、网络、冷却和功耗等方面的问题。
  
  IaaS是一个很好的开始，也是最基本的。每一个试图跳过IaaS，而直接出售PaaS和SaaS产品的云供应商都遇到了挑战。
  例如，微软最初在Azure提供PaaS服务，成功之路受限。他们在2012年为Azure增加了IaaS，并持续至今。
  
#### 脱胎：PaaS
  迁移到公有云后，公司通常需要一些时间来适应新环境。对于一些公司，这个时间可能是几个月甚至几年。其中最大的挑战来自于在公有云上寻求与本地应用同样的安全感。
  
  当公司熟悉并融入新的环境时，他们可能会观察到一些有趣的东西。
  他们所看到的是，有现成的服务可以做他们多年来一直在做的事情。
  IaaS帮他们把硬件抽离出来，这些服务帮助他们甚至把操作系统抽离出来。
  应用程序可以简单地重建在这些服务之上，这为不同的需求提供了通用的平台。
  这种强化被称为PaaS，平台即服务。
  
  PaaS不仅带来了简单，也在IaaS的基础上显著降低成本。
  
  PaaS是一个重要的步骤，因为这是云原生的第一接触点。它引入了在公有云中重构应用并充分发挥其潜力的思维过程。
    
#### 云原生与容器技术  
  事实上，云原生应用的设计理念和原则在较早的时候就被软件架构大师们提出来，只不过在容器技术火热之前，特别是docker技术出现之前，
以虚拟机镜像为基础的应用打包发布方式开销过大，普通开发人员很难将云原生应用的设计理念和原则落实到实际开发中，导致其流行不起来。

  以云原生应用的核心设计原则[12-Factor](https://12factor.net/zh_cn/)来看，我们可以发现容器技术，特别是Docker，正是在设计中融入了对云原生应用的深层支持，才得以风靡软件世界。
  
  随着对[12-Factor](https://12factor.net/zh_cn/)理解的加深，我们也同时会发现[12-Factor](https://12factor.net/zh_cn/)之间的彼此紧密联系，是围绕一个核心目标的12项具体指导原则。
  
  而这个核心目标就是提高应用的可移植性和移动性。

  ![cloud-native-application](/images/posts/cloud-native-application.jpg)

  下文中，就以Docker技术为例，来说明容器技术是怎样来支持[12-Factor](https://12factor.net/zh_cn/)的。

### 容器技术对Twelve-Factor的支持
#### 1. 基准代码

> 应用有一套基准代码，可以部署到多种环境中。这样可以保证同一套代码容易迁移到不同环境中去运行。

> 在Docker的体系中，Dockerfile也是基准代码的一部分，跟应用业务代码保存在同一个代码仓库中，用同一套版本标号。基于Docker体系交付的软件产品不再是一个可执行程序，而是一个Docker镜像。Docker镜像的移动性比传统的可执行程序高得多。

#### 2. 依赖

> 应用要清楚地声明和隔离自己依赖的程序库。这样才能保证应用移动到其他环境时，自己所依赖的程序库也正常运行，并且与环境中其他软件不互相干扰。

> Docker通过Dockerfile中的命令将自己依赖的程序声明出来，并通过docker build命令将这些依赖的程序库打包到交付的Docker镜像中。

#### 3. 配置

> 运行时配置要存储到运行时环境中。一个应用的行为逻辑受两方面因素控制，一方面因素是代码，另一方面因素是配置；代码是与运行时环境无关的，要保存在应用开发的代码仓库中，而配置是与运行时环境相关的。将配置存储到运行时环境中保证了配置与环境的一致性。

> Docker在Dockerfile中用ENV命令声明自己运行时所依赖的环境变量和环境变量的默认值，在docker run命令的--env参数可以在运行时设定环境变量的值。

#### 4. 后端支撑服务

> 将数据库、缓存、消息队列服务这些后台支撑服务当作可挂载的资源。保证这些后端支撑服务对应用业务完全透明，应用只是把这些服务当作一种透明的资源来使用，这样更换环境部署应用只需要更改与资源相关的环境变量。

> 在Docker体系中，通行的做法正是将数据库、缓存和消息队列等后台支撑服务当作资源在Docker容器运行时挂载。而应用程序在构建镜像时，需要将所需要的资源的环境变量用ENV声明出来，在容器运行时，实际环境中的资源环境变量则以--env参数的方式设置到容器中。

#### 5. 构建 发布 运行

> 严格区分构建和运行这两个不同的阶段。通过清楚地区分构建期和运行期两个阶段，对应的软件控制因素也清楚地分为代码和配置两类；代码在构建完成后是不会改变的，而且一套代码可以自由地部署到多套环境中去运行，不同环境中应用软件运行所需要改变的仅仅是配置。

> 在Docker体系中，构建器的“代码”不仅仅是应用程序编程语言的代码（例如Java，C，Python等），也包括这些代码运行时所固定依赖的程序库和这些程序库的“静态配置”。称其为“静态配置”主要是指这些配置不会因为部署环境的改变而改变，因此这些“静态配置”实际上成为的Docker镜像的“代码”。因此，对Docker镜像来说，应用业务代码和静态配置都是“代码”；只有根据部署环境可能变动的“动态配置”，才是真正的“配置”，而这些配置对应了Docker运行时的环境变量。

#### 6. 进程

> 将应用作为无状态的进程来运行。无状态进程保证应用可以随时启动和关闭，随时根据业务压力而增加或减少运行实例数，保证应用的移动性。

> 由于Docker容器技术相对于虚拟机技术来说，大大降低了运行应用实例的开销，提高了启动和关闭应用实例的速度，应用Docker体系发布的应用与无状态应用的模型更加匹配。

#### 7. 端口绑定

> 通过端口绑定来发布服务。保证一个应用服务在不同的运行环境中，可以用指定的任何端口来发布，这也是提高应用移动性的一个重要原则。

> Docker在Dockerfile中用EXPOSE命令声明自己运行时容器所要发布的端口，在docker run命令的-p参数可以指定主机上发布服务的端口与容器端口的映射，这样的设计帮助应用开发者自然而然的实现云原生应用对端口绑定的要求：内部的端口在构建期决定，而对外发布的实际端口在在运行环境中决定。

#### 8. 并发

> 可以通过水平伸缩应用的进程数来增大或缩小系统的容量。

> 如前所述，Docker容器的设计保证了水平伸缩的高效率。

#### 9. 可丢弃性
> 应用进程可以快速启动也可以优雅地关闭。

> Docker容器在快速启动和优雅关闭方面的效率要大大高于传统虚拟机，使得结合Docker容器实现应用的可丢弃性成为自然而然的事情。

#### 10. 开发生产对等

> 保持开发环境、测试环境、预发布环境和生产环境尽量一致。保持各种环境一致，才能减少因为环境不一致造成的与业务代码无关的错误，提高应用的可移动性。

> Docker容器流行起来的一大原因，就是它将应用程序所依赖的程序库以及这些程序库的“静态配置”一并打包成Docker镜像，以Docker镜像部署到不同的环境中，从而大大减少了不同环境上应用的差别，保证了一次构建，任何地方部署运行。

#### 11. 日志

> 将日志以带时间戳的事件流方式来管理。把日志当作事件流来管理，实际是将日志作为结构化的数据而不是非结构化的文件来管理，这样使得日志方便在云环境中由云平台统一管理和分析；否则，在云环境中分布在各处各自为政的日志将给系统分析和排错打来极大困难。

> Docker正是将日志以事件来管理的。利用docker logs命令可以查询指定容器的日志，同时，所有容器的日志正是以结构化JSON数据的格式默认保存在/var/lib/docker/containers//目录中。云平台管理软件可以统一管理处理相应的日志文件给用户提供方便的差错工具。

#### 12. 管理进程

> 将管理任务当作一次性任务来运行。这样保证所有的管理任务都是可以在云平台环境下自动化的，从而为大规模应用的自动运维，大幅度的自动伸缩提供了基础。

> 在Docker体系下，所有的管理工作都有相应的docker客户端命令和docker的REST API服务提供支持，使得云平台可以通过调用命令行或REST API来管理容器。


## Backing Service
### 背景介绍
  Backing Service指需要通过网络调用来完成的服务。比方数据库啊，消息队列啊，以及API访问的服务比如Twitter API等等。
  结合[12-Factor](https://12factor.net/zh_cn/)来看，它是从[后端支撑服务](#4-后端支撑服务)中引申出来的需求：开发者只需要关注应用业务本身的开发，对依赖的服务只需通过外部接入，直接使用。
  
  
### 举例说明
  外部服务有诸如持久化、插件化，服务化等特性。拿MySQL来做比方，如果要把MySQL跟应用打包在一起跑在集群容器里，需考虑把数据存储挂一个Volume出来。
  而这一点在集群中会表现的更加复杂，因为涉及到跨主机数据同步的问题，开发者也许并不熟悉这些细节原理，如果总是纠结于应用之外的问题，那毫无疑问，开发效率会明显降低。
  目前主流的方式是把MySQL的连接信息注入到应用的环境变量中，如果应用不再使用这个数据库，那么可以随时卸载这个数据库，或者接入另一个数据库，只要简单地清除或修改环境变量就可以，而不用再关心怎样优雅地去停掉那个不再被使用的数据库。

  在PaaS上，应用开发者依赖的服务都会产生交集，这也是我们为什么以后端服务的形式来提供这些服务的原因之一，我们把这些后端服务作为一个支撑服务体系，给开发者提供服务。
  还以MySQL来比方，如果没有后端服务，那每一个依赖MySQL的应用都要起一个MySQL的容器。
  对于集群管理者，意味着集群里会跑许多MySQL容器。

  另一个原因是，有很多后端服务比如大数据算法和分析工具，只以API或者独立服务的形式提供出来，如此一来，开发者也没有能力在自己的应用里打包一个这样的容器/镜像。

  最后一个主要原因是，这些后端服务对开发者是热插拔的。使用的时候只要通过命令/接口来创建一个服务实例，然后绑定实例到自己的应用上就可以了，非常便捷。

  那么再具体地，开发者如何使用这些后端服务，与自己的应用对接呢？我们采用了Service Broker协议规范。
  
  Service Broker是CloudFoundry中的概念，目标就是把应用与服务分离。
  Service Broker实现7个REST API，通过API，我们可以知道一个Broker会提供何种服务，并且轻松地实现对服务实例的创建、绑定、解绑，以及销毁。
  采用ServiceBroker规范还有一个好处就是，所有实现了Service Broker协议的服务，都可以以后端服务的方式无缝接入我们的PaaS平台。
  这样，一些第三方服务就可以轻松地为开发者提供诸多服务。

  我们来看一看Service Broker API:
  ![service-broker-api](/images/posts/service-broker-api.jpg)
  
  上面7个API便是Service Broker协议中需要实现的接口。获取服务，创建实例，绑定应用，这一系列操作都通过这几个api完成。
  关于Service Broker API的更多信息，请查看：
  [open service broker api](https://github.com/openservicebrokerapi/servicebroker/blob/v2.12/spec.md)  

## Beyond the Twelve-Factor App
  建立在原有的12 factors上，根据优先顺序做了调整，添加了API first、Telemetry、Authentication and authorization，具体内容请查看[原文](/assets/pdf/beyond-the-12-factor-app.pdf)

***1. One codebase, one App***
* Single version-controlled codebase, many deploys
* Multiple apps should not share code
  * Microservices need separate release schedules
  * Upgrade, deploy one without impacting others
* Tie build and deploy pipelines to single codebase

***2. API first***
* Service ecosystem requires a contract
  * Public API
* Multiple teams on different schedulers
  * Code to contract/API, not code dependencies
* Use well-documented contract standards
  * Protobuf IDL, Swagger, Apiary, etc
* API First != REST first
  * RPC can be more appropriate in some situations

***3. Dependency Management***
* Explicitly declare dependencies
* Include all dependencies with app release
* Create immutable build artifact (e.g. docker image)
* Rely on smallest docker image
  * Base on scratch if possible
* App cannot rely on host for system tools or libraries

***4. Design, Build, Release, Run***
* Design part of iterative cycle
  * Agile doesn’t mean random or undesigned
* Mature CI/CD pipeline and teams
  * Design to production in days not months
* Build immutable artifacts
* Release automatically deploys to environment
  * Environments contains config, not release artifact

***5. Configuration, Credentials, Code***
* “3 Cs” volatile substances that explode when combined
* Password in a config file is as bad as password in code
* App must accept “3 Cs” from environment and only use harmless defaults
* Test - Could you expose code on Github and not reveal passwords, URLs, credentials?

***6. Logs***
Emit formatted logs to stdout
* Code should not know about destination or purpose of log emissions
* Use downstream log aggregator
* collect, store, process, expose logs
* ELK, Splunk, Sumo, etc
  * Use structured logs to allow query and analysis
  * JSON, csv, KV, etc
* Logs are not metrics

***7. Disposability***
* App must start as quickly as possible
* App must stop quickly and gracefully
* Processes start and stop all the time in the cloud
* Every scale up/down disposes of processes
* Slow dispose == slow scale
* Slow dispose or startup can cause availability gaps

***8. Backing Services***
* Assume all resources supplied by backing services
* Cannot assume mutable file system
  * “Disk as a Service” (e.g. S3, virtual mounts, etc)
* Every backing service is bound resource
  * URL, credentials, etc-> environment config
* Host does not satisfy NFRs
  * Backing services and cloud infrastructure

***9. Environment Parity***
* “Works on my machine”
  * Cloud-native anti-pattern. Must work everywhere
* Every commit is candidate for deployment
* Automated acceptance tests
  * Provide no confidence if environments don’t match

***10. Administrative Processes***
* Database migrations
* Run-once scripts or jobs
* Avoid using for batch operations, consider instead:
  * Event sourcing
  * Schedulers
  * Triggers from queues, etc
  * Lambdas/functions

***11. Port Binding***
* In cloud, infrastructure determines port
* App must accept port assigned by platform
* Containers have internal/external ports
  * App design must embrace this
* Never use reserved ports
* Beware of container “host mode” networking

***12. Stateless Processes***
* What is stateless?
* Long-term state handled by a backing service
* In-memory state lives only as long as request
* Requests from the same client routed to different instances
  * “Sticky sessions” cloud native anti-pattern

***13. Concurency***
* Scale horizontally using the process model
* Build disposable, stateless, share-nothing processes
* Avoid adding CPU/RAM to increase scale/throughput
* Where possible, let platform/libraries do threading
  * Many single-threaded services > 1 multi-threaded monolith

***14. Telemetry***
* Monitor apps in the cloud like satellite in orbit
* No tether, no live debugger
* Application Perf Monitoring (APM)
* Domain Telemetry
* Health and system logs

***15. Authentication & Authorization***
* Security should never be an afterthought
* Auth should be explicit, documented decision
  * Even if anonymous access is allowed
  * Don’t allow anonymous access
* Bearer tokens/OAuth/OIDC best practices
* Audit all attempts to access
 
## 参考资料

1. [Docker和云原生应用的12要素](http://blog.sina.com.cn/s/blog_54b9e4210102wcr5.html)

2. [Kubernetes集成外部服务实践](http://wwwbuild.net/dockerone/135777.html)

3. [真正的云原生应用是什么样](http://www.dataguru.cn/article-9846-1.html)

4. [Beyond the Twelve-Factor App](https://content.pivotal.io/blog/beyond-the-twelve-factor-app)

5. [High Level Cloud Native From Kevin Hoffman](https://jimmysong.io/posts/high-level-cloud-native-from-kevin-hoffman/)