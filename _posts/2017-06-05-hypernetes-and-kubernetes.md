---
layout: post
title: Hypernetes & Kubernetes
categories: Kubernetes
description:
keywords:
---

本文介绍Hypernetes与Kubernetes之间的关系

![rethink-of-virtualization-and-containerization](/images/posts/rethink-of-virtualization-and-containerization-13-638.jpg)

## Hypernetes相关链接
  1. [Hypernetes的卖点](http://sacc.it168.com/PPT2016/%E4%B8%9315-3%EF%BC%8D%E8%A3%B4%E5%BD%A4.pdf)
  2. [github地址](https://github.com/hyperhq/hypernetes)
  3. [基于 HyperContainer 和 Hypernetes 项目的公有云：Hyper_](https://www.hyper.sh/) 
  4. [知乎话题：如何评价 hyper_](https://www.zhihu.com/question/35412725/answer/101715150)
  5. [Secure NFV (Clearwater) deployment on Kubernetes (Hypernetes) in 10 min](https://www.youtube.com/watch?v=3QTbFS7iLgo)
  6. [项目拥有者博客](https://wangxu.me/)
  7. [将Hypernetes整合至Kubernetes带来安全性与多租户机制](https://www.kubernetes.org.cn/239.html)
  8. [Hypernetes安装日志](/assets/pdf/Hypernetes_install_Log.txt)

## Hypernetes简介
  Hypernetes在Kubernetes基础上增加了多租户认证授权、容器SDN网络、基于Hyper的容器执行引擎以及基于Cinder的持久化存储等。
  
  基本上***Hypernetes = Bare-metal + Hyper + Kubernetes + Cinder(Ceph) + Neutron + Keystone***
  
  在介绍Hypernetes细节之前先首先提一下相关背景，也就是Kubernetes的多租户支持情况。
  
  Kubernetes在多租户方面的支持还是比较少的，没有“租户”这一概念，也只有namespace提供了一个逻辑的资源池（可以给这个namespace设定一些资源的配额），并且它在认证授权、网络、Container Runtime等方面离真正的多租户还都比较远。
  
  1. 认证方面，虽然支持client certificates，tokens，http basic auth等，但没有用户管理的功能。如果想要新建用户，需要手动修改配置文件并重启服务。最新版本增加了Keystone的认证，可以借助Keystone来管理用户。
  
  2. 授权方面，目前只有AlwaysDeny ，AlwaysAllow以及ABAC模式。ABAC模式根据一个策略文件来配置不同用户的权限，比如限定用户只能访问特定的namespace等，但对于新创建的namespace等资源需要重复修改策略文件。
  
  3. Kubernetes要求cluster内部所有的容器之间是全通的，而多租户情况下需要不同租户之前网络是隔离的。
  
  4. Docker的安全性问题，跟虚拟化还是有不少距离。
  
  正是由于上面这些原因，很多公司都在虚拟机里面来跑Kubernetes，比如Google Container Engine、OpenStack Magnum等。
  
  在虚拟机内部跑容器虽然提升了安全性，但也引入了一些问题，比如容器的网络不能通过IaaS层来统一管理，容器无法直接使用IaaS层的持久化存储，无法集中调度所有容器的资源等。
  
  先来看一下Hypernetes的架构图
  
  ![Hypernetes-orchitecher](/images/posts/hypernetes-orchitecher.png)
  
  Hypernetes在Kubernetes基础上增加了一些组件
  1. 增加了Tenant的概念，不同Tenant之间的网络和资源(ns, pod, svc, rc等)是隔离的。这些租户通过keystone管理，并提供认证和授权
  
  2. 通过Neutron给不同租户提供二层隔离的网络，并支持Neutron的各种插件（目前主要是ovs）
 
  3. 通过Hyper提供基于虚拟化的容器执行引擎，保证容器的隔离
  
  4. 还有通过Cinder给容器引入各种持久化存储（目前主要是ceph）
  
  具体到Hypernetes内部，详细的架构是这样的
  
  ![Hypernetes-orchitecher-1](/images/posts/hypernetes-orchitecher-1.png)
  
  为了支持多租户，Hypernetes基于Kubernetes增加了很多组件，这些组件都是以Plugin的形式提供的。
  
  这样非常方便扩展，也很容易将Hypernetes与现有的IaaS在同一个基础架构上运行起来
  
  比如，如果你不喜欢Neutron，可以把它替换成odl等。
  
## 与Kubernetes的关系
  1. The hypervisor-based container runtime for Kubernetes，通过Hyper提供基于虚拟化的容器执行引擎，保证容器的隔离，并将其贡献在kubernetes组织下：[frakti](https://github.com/kubernetes/frakti)，再通过CRI（Container Runtime Interface），原生接入Kubernetes，类似CoreOS公司的rkt接入kubernetes。
     在Hypernetes v1.6版本中，Hypernetes的容器运行时也将从一套名为[runV](https://github.com/hyperhq/runv)的OCI标准容器运行时切换到[frakti](https://github.com/kubernetes/frakti)。
  
  2. Hypernetes 是从Kubernetes项目中fork出来的。为了支持多租户，Hypernetes基于Kubernetes增加了很多组件，这些组件都是以Plugin的形式提供的。
  
  3. 关于虚拟化和容器化的思考，可以参见项目拥有者的一篇报告[Re-Think of Virtualization and Containerization](https://www.slideshare.net/gnawux/rethink-of-virtualization-and-containerization)
  
  4. Hypernetes 借助Kubernetes中的RBAC（Role-Based Access Control）来实现接入控制，具体方式如下图所示
  
  ![RBAC](/images/posts/RBAC.png)
  
  userA和userB是通过[role_binding1][role1]连接到name space 1，得到在name space1进行操作的权限。userB则是[role_binding2]->[role2]得到在name space2进行操作的权限。
  
## Proposal of hypernetes1.6 auth
- Continue to use keystone, Keystone authentication is enabled by passing the --experimental-keystone-url=<AuthURL> option to the API server during startup

- Use  [RBAC](https://kubernetes.io/docs/admin/authorization/) for authorization，enable the authorization module with --authorization-mode=RBAC

- Add auth-controller to manage RBAC policy

## auth workflow:

```
kubectl                 apiserver                keystone               rbac         auth+controller
   +                       +                         +                    +                 +
   |         1             |                         |                    |                 |
   +-----+request+--------->                         |                    |                 |
   |                       |           2             |                    <--+update policy++
   |                       +----+Authentication+----->                    |                 |
   |                       |           3             |                    |                 |
   |                       <---+user.info,success+---+                    |                 |
   |                       |                      4  +                    |                 |
   |                       +-------------------+Authorization+------------>                 |
   |                       |                     5   +                    |                 |
   |                       <--------------------+success+-----------------+                 |
   |          6            |                         +                    |                 |
   <-----+response+--------+                         |                    |                 |
   |                       |                         |                    |                 |
   +                       +                         +                    +                 +

```

- To update rbac policy, auth-controller need to get user-info. Auth-controller update `ClusterRole` and `RoleBinding` to get permissions of namespaces, when user is updated. May be we should add user as a kind of resource.

- Watch `Namespace` and update the permissions of namespace scoped resources for regular user.

1. kubectl send request with (username, password) to apiserver
2. Apiserver receive request from kubectl, and call [AuthenticatePassword](https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/apiserver/plugin/pkg/authenticator/password/keystone/keystone.go#L42)(username, password) to check (username, password) via keystone. 
3. If check successfully return (user.info, true), continue to step4. if check fail return (user.info, fales), request fail.
4. call [Authorize](https://github.com/kubernetes/kubernetes/blob/master/plugin/pkg/auth/authorizer/rbac/rbac.go#L70)(authorizer.Attributes) to check RBAC roles  and binding. 
5. if check success return true and execute operation of the request. if check fail return false ,request fail.
6. return request result 

## Add user
- Use [Third Party Resources](https://kubernetes.io/docs/tasks/access-kubernetes-api/extend-api-third-party-resource/) to create user resource :

```
apiVersion: extensions/v1beta1
kind: ThirdPartyResource
metadata:
  name: user.stable.example.com
description: "A specification of a User"
versions:
- name: v1
```

- Add a user object. May be there more detail user information will be set using custom fields like `username`. At least username are required.

```
apiVersion: "stable.example.com/v1"
kind: User
metadata:
  name: alice
username: "alice chen"
```

## auth-controller 

auth-controller is responsible of updating RBAC roles and binding when

- new user is created: allow the user to create/get namespace
- new namespace is created/deleted
- network is created/deleted
- user is removed: delete all user related data

auth -controller will watch resources including `User`, `NameSpace`, `Network` and update RBAC roles and binding using superuser.
First  create `ClusterRole` for regular user to get permissions of `NameSpace`:

```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1alpha1
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced.
  name: namespace-creater
rules:
  - apiGroups: [""]
    resources: ["namespace"]
    verbs: ["get", "create"] # I think verbs should include "delete"，user have permission of deleting their namespace 
```

### new user are created:
create `ClusterRoleBinding` for new regular user reference `namespace-creater` role:

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1alpha1
metadata:
  name: username-namespace-creater
subjects:
  - kind: User 
    name: username
roleRef:
  kind: ClusterRole
  name: namespace-creater
  apiGroup: rbac.authorization.k8s.io
```
### a new namespace are created:
create `Role` namespaced:

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1alpha1
metadata:
  namespace: namespace
  name: access-resources-within-namespace
rules:
  - apiGroups: [""]
    resources: [ResourceAll]
    verbs: [VerbAll]
```
create `Rolebinding` reference to `role` namespace:

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1alpha1
metadata:
  name: namespace-binding
  namespace: namespace 
subjects:
  - kind: User
    name: username
roleRef:
  kind: Role
  name: access-resources-within-namespace
  apiGroup: rbac.authorization.k8s.io
```

role and rolebinding will be removed when namespace are deleted
### a network is created/deleted
`Network` will be defined non-namespaced resource. we can set permission of `Network` in `ClusterRole` when user are created.

### a user is deleted:
`ClusterRoleBinding` related the user will be removed at least. 
  