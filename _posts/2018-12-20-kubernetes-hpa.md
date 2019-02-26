---
layout: post
title: Kubernetes HPA
categories: Kubernetes
description:
keywords:
---

Kubernetes HPA

## Kubernetes 设计理念

用户定义应用程序的规格，Kubernetes 按照定义的规格部署并运行应用程序。

其实现依赖于 ``` 声明式 API ```: 就是当用户向 Kubernetes 提交了一个 API 对象的描述之后，Kubernetes 会负责为你保证整个集群里各项资源的状态，都与你的 API 对象描述的需求相一致。更重要的是，这个保证是一项“无条件的”、“没有期限”的承诺：对于每个保存在 etcd 里的 API 对象，Kubernetes 都通过启动一种叫做“控制器模式”（Controller Pattern）的无限循环，不断检查，然后调谐，最后确保整个集群的状态与这个 API 对象的描述一致。

``` go
for {
  实际状态 := 获取集群中对象X的实际状态（Actual State）
  期望状态 := 获取集群中对象X的期望状态（Desired State）
  if 实际状态 == 期望状态{
    什么都不做
  } else {
    执行编排动作，将实际状态调整为期望状态
  }
}
```

## HPA

1、HPA全称Horizontal Pod Autoscaling，即POD的水平自动伸缩。

Kubernetes 最大的特点是在用户定义的应用程序规格内进行自动伸缩，自动伸缩主要分为两种，其一为POD的水平伸缩，针对于POD数目的增减，也就是HPA；其二为垂直伸缩，即单个POD可以使用的资源的增减，也就是在POD的requests（最小资源）、limits（最大资源）范围内增减资源。

2、HPA是Kubernetes中实现POD水平自动伸缩的功能。

云计算具有水平弹性的特性，这是云计算区别于传统IT技术架构的主要特性。对于Kubernetes中的POD来说，HPA可以实现很多自动化功能，比如当POD中业务负载上升的时候，可以创建新的POD来保证业务系统稳定运行，当POD中业务负载下降的时候，可以销毁POD来提高资源利用率。

3、HPA控制器默认每隔30秒就会运行一次。

如果要修改间隔时间，可以设置horizontal-pod-autoscaler-sync-period参数。

4、HPA的操作对象是RC、RS或Deployment对应的POD

根据观察到的CPU等实际使用量与用户的期望值进行比对，做出是否需要增减POD实例数量的决策。

5、HPA的发展历程

在Kubernetes v1.1中首次引入了HPA特性。HPA第一个版本基于观察到的CPU利用率，后续版本支持基于内存使用。

在Kubernetes 1.6中引入了一个新的API，用来自定义指标，它允许HPA访问任意指标。

Kubernetes 1.7引入了聚合层，允许第三方应用程序通过注册为API附加组件来扩展Kubernetes API。自定义指标API以及聚合层使得像Prometheus这样的监控系统可以向HPA控制器公开特定于应用程序的指标。

## 部署监测工具

因为POD的metrics信息来源于其他第三方监控组件，所以在开始之前要保证监控组件的运行正常，我们拿metrics-server来举例。

### 部署metrics-server

可以按照以下方式部署：

测量 CPU/Memory HPA ，可以使用 ```https://github.com/kubernetes-incubator/metrics-server``` 中所示方式，如下：

```
mkdir K8s-monitor
cd K8s-monitor
git clone https://github.com/kubernetes-incubator/metrics-server.git
# 如果 Kubernetes > 1.8
cd metrics-server/deploy/1.8+/
```

修改 ```metrics-server-deployment.yaml``` 配置，在 ```containers``` 中添加 ```command``` 有关的内容

```
      containers:
      - name: metrics-server
        image: k8s.gcr.io/metrics-server-amd64:v0.3.1
        imagePullPolicy: Always
        command:
        - /metrics-server
        - --kubelet-insecure-tls
        - --kubelet-preferred-address-types=InternalIP,Hostname,InternalDNS,ExternalDNS,ExternalIP
        volumeMounts:
        - name: tmp-dir
          mountPath: /tmp
```

部署

```
kubectl apply -f ./
```
验证

可以通过运行 ```kubectl top node``` 来验证metrics-server是否运行正常。

运行不正常情况

```
kubectl top node
error: metrics not available yet
```

运行正常情况

```
kubectl top node
NAME       CPU(cores)   CPU%      MEMORY(bytes)   MEMORY%
master01   228m         5%        2551Mi          26%
minion01   189m         4%        1991Mi          20%
minion02   138m         3%        1594Mi          16%
minion03   75m          1%        941Mi           9%
```

提示：刚部署完成后，需要稍等一会才会出现运行正常的情况。

### 部署prometheus

可以使用 ```https://github.com/coreos/prometheus-operator``` 中所示方式。

### 部署其他监控维度的监测工具

可以参考 ```https://github.com/heptiolabs/eventrouter``` 中所示方式。

## 制作镜像

新建文件夹hpa-test-app

在hpa-test-app文件夹下，新建hpa-test-app.go文件，内容如下：

``` go
package main

import (
    "fmt"
    "log"
    "net/http"
    "strconv"
    "strings"
)

func main() {
    http.HandleFunc("/", compPiHandler)
    log.Println("Server work at :8080 ...")
    err := http.ListenAndServe(":8080", nil)
    if err != nil {
        log.Fatal("ListenAndServe: ", err)
    }
}

func compPiHandler(w http.ResponseWriter, r *http.Request) {
    var (
        lenI int
        err  error
    )

    lenS := r.URL.Query().Get("length")
    if lenS != "" {
        lenI, err = strconv.Atoi(lenS)
        if err != nil {
            lenI = 100
        }
    } else {
        lenI = 100
    }

    fmt.Fprintf(w, compPi(lenI))
}

func compPi(L int) string {
    var Pi []string

    N := (L)/4 + 1

    s := make([]int, N+3)
    w := make([]int, N+3)
    v := make([]int, N+3)
    q := make([]int, N+3)
    n := (int)(float32(L)/1.39793 + 1)

    w[0] = 16 * 5
    v[0] = 4 * 239

    for k := 1; k <= n; k++ {
        div(w, 25, w, N)
        div(v, 57121, v, N)
        sub(w, v, q, N)
        div(q, 2*k-1, q, N)
        if k%2 != 0 {
            add(s, q, s, N)
        } else {
            sub(s, q, s, N)
        }
    }

    Pi = append(Pi, fmt.Sprintf("%d.", s[0]))

    for k := 1; k < N; k++ {
        Pi = append(Pi, fmt.Sprintf("%04d", s[k]))
    }

    return strings.Join(Pi, "")
}

func add(a []int, b []int, c []int, N int) {
    i, carry := 0, 0
    for i = N + 1; i >= 0; i-- {
        c[i] = a[i] + b[i] + carry
        if c[i] < 10000 {
            carry = 0
        } else {
            c[i] = c[i] - 10000
            carry = 1
        }
    }
}

func sub(a []int, b []int, c []int, N int) {
    i, borrow := 0, 0
    for i = N + 1; i >= 0; i-- {
        c[i] = a[i] - b[i] - borrow
        if c[i] >= 0 {
            borrow = 0
        } else {
            c[i] = c[i] + 10000
            borrow = 1
        }
    }
}

func div(a []int, b int, c []int, N int) {
    i, tmp, remain := 0, 0, 0
    for i = 0; i <= N+1; i++ {
        tmp = a[i] + remain
        c[i] = tmp / b
        remain = (tmp % b) * 10000
    }
}
```

编译成linux下的程序包，我是在mac上开发的，需要编译到linux下运行，涉及到跨平台编译。

编译代码命令：

```
sudo env GOOS=linux GOARCH=386 go build hpa-test-app.go
```

如果知道linux系统的准确信息（采用 ```uname -a``` 命令查看），例如amd64或者i386，将上述命令中的 ```GOARCH=386``` 替换为 ```GOARCH=amd64``` 或者 ```GOARCH=i386```

编译完成后，本地会多出一个 hpa-test-app 程序，备用。

在hpa-test-app文件夹下，新建Dockerfile文件，内容如下：

```
FROM alpine:latest
MAINTAINER YP "yu3peng@qq.com"
WORKDIR $GOPATH/src/github.com/yu3peng/hpa-test-app
ADD hpa-test-app $GOPATH/src/github.com/yu3peng/hpa-test-app
EXPOSE 8080
ENTRYPOINT  ["./hpa-test-app"]
```

在当前目录下，执行docker build -t hpa-test-app .
输出

```
Sending build context to Docker daemon  5.12 kB
Step 1/6 : FROM golang:latest
 ---> 2422e4d43e15
Step 2/6 : MAINTAINER YP "yu3peng@qq.com"
 ---> Using cache
 ---> f4a9005be88b
Step 3/6 : WORKDIR $GOPATH/src/github.com/yu3peng
 ---> Using cache
 ---> 6d5fec3b81a1
Step 4/6 : ADD . $GOPATH/src/github.com/yu3peng
 ---> Using cache
 ---> 24b3025ae163
Step 5/6 : EXPOSE 8080
 ---> Using cache
 ---> c2e09786bf15
Step 6/6 : ENTRYPOINT ./hpa-test-app
 ---> Using cache
 ---> b9d6c77740b8
Successfully built b9d6c77740b8
```

执行docker image ls

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hpa-test-app        latest              b9d6c77740b8        14 minutes ago      10 MB
```

## 服务测试
基于上面提供的hpa-test-app镜像, 我们创建一个hpa-test-app service, 然后为该service添加HPA机制。

### 创建Deployment 和 service

```
~$ kubectl get svc,deploy
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   45d

~$ kubectl run hpa-test-app  --image=yu3peng/hpa-test-app:latest  --requests=cpu=6m,memory=32Mi --limits=cpu=60m,memory=128Mi --expose --port=8080
service "hpa-test-app" created
deployment.apps "hpa-test-app" created
~$ kubectl get svc,deploy
NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/hpa-test-app   ClusterIP   10.105.191.80   <none>        8080/TCP   4s
service/kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP    45d

NAME                                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/hpa-test-app   1         1         1            0           4s
```

### 创建HPA

#### 基于CPU利用率

可以直接使用命令创建HPA

```
~$ kubectl autoscale deployment hpa-test-app --cpu-percent=70 --min=1 --max=10
deployment.apps "hpa-test-app" autoscaled
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   31%/70%   1         10        1          16s
```

#### 基于Memory利用率

需要通过配置文件创建

```
cat << EOF > hpa-test-app-memory-metrics.yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: hpa-test-app-memory-metrics
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: hpa-test-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: memory
      targetAverageValue: 100Mi
EOF
kubectl create -f hpa-test-app-memory-metrics.yaml
```

### 增压和减压测试

首先我们启动一个busybox的pod, 用来对hap-test-app服务进行压力测试

```
cat << EOF > pod-busybox.yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always
EOF
kubectl create -f pod-busybox.yaml
```

然后我们找到hpa-test-app服务的clusterIP:

```
~$ kubectl get svc hpa-test-app
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
hpa-test-app   ClusterIP   10.105.191.80   <none>        8080/TCP   3m
```

#### 应用扩张

进入容器, 持续访问hpa-test-app的API

默认计算100位的π，用以下命令实现

```
kubectl exec -it busybox /bin/sh
/ # while true; do wget -q -O- 10.105.191.80:8080; done
```

如果想加大计算（例如计算1000为的π）可以通过以下命令实现

```
kubectl exec -it busybox /bin/sh
/ # while true; do wget -q -O- 10.105.191.80:8080/?length=1000; done
```

然后我们持续关系HPA的扩张

```
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   100%/70%   1         10        1          3m
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   100%/70%   1         10        2          3m
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   331%/70%   1         10        2          4m
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   331%/70%   1         10        5          4m
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   185%/70%   1         10        8          5m
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   130%/70%   1         10        10         7m
```

pod数量的变化情况 1–>2–>5–>8–>10, 最终达到最大的扩展上线而停止。

#### 应用收缩

中断对app的访问, 会发现容器又收缩为原来的1个:


```
~$ kubectl get hpa
NAME           REFERENCE                 TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
hpa-test-app   Deployment/hpa-test-app   33%/70%   1         10        3          21m
```

## 参考资料

[1] [如何利用kubernetes实现应用的水平扩展(HPA)](https://blog.csdn.net/qq_17016649/article/details/79297796)

[2] [k8s controller-manager之hpa源码分析](https://blog.csdn.net/weixin_39961559/article/details/80578907)

[3] [k8s全栈监控之metrics-server和prometheus](http://www.cnblogs.com/cuishuai/p/9857120.html)

[4] [K8S集群基于heapster的HPA测试](http://blog.51cto.com/ylw6006/2113848)

[5] [kubectl run 背后到底发生了什么？](https://www.yangcs.net/posts/what-happens-when-k8s/)

[6] [Kubernetes之kubectl常用命令使用指南:2:故障排查](https://blog.csdn.net/kozazyh/article/details/79514742)