---
layout: post
title: OPEN SOURCE MANO
categories: NFV
description:
keywords:
---

OSM简介

## OSM简介
OSM（开源MANO）是由ETSI托管的一个项目，用于开发一个开源的NFV MANO软件堆栈，这与其提出的架构相一致。

该项目首次在2016年移动世界大会上作为运营商用例展示。

有趣的是，OSM同时使用了两个项目——OpenMANO和RIFT.io——以及OpenStack和Ubuntu JuJu。

考虑到这些项目的重用，OSM得到电信公司（如Telefónica of Spain ，英国电信，奥地利电信，韩国电信和Telenor）的支持，以及英特尔，Mirantis，RIFT.io，博科，戴尔，RADware等设备商的支持。

## OSM Mapping to ETSI NFV MANO
![osm_map](/images/posts/osm_map.png)

### 借鉴OpenMANO
OpenMANO是Telefonica发布的一个项目，由VIM（OpenVIM）、VNF管理器和Orchestrator组成，如下图所示

![OpenMANO](/images/posts/open-source-mano-fig-2.png)

该图还强调了OpenMANO也可以在其架构中与OpenStack一起作为VIM。OpenVIM是NFV VIM的参考实现。

它非常类似于OpenStack，与NFV基础设施中的计算节点以及提供计算和网络功能以及部署虚拟机的OpenFlow控制器接口。

它提供了一个基于REST的北向接口。

OpenVIM是EPA(早期访问计划)感知的，包括功能支持，如CPU和NUMA(Non-Uniform Memory Access)固定，PCI(Peripheral Component Interconnect)传输等。

截至今天，OpenMANO是一个非常基本的实现，不适合商业部署。