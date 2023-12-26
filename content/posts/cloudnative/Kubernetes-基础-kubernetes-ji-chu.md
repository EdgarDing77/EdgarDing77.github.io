---
title: Kubernetes-基础
date: 2021-09-17 20:30:16.198
updated: 2021-09-22 19:16:41.178
categories: [Cloud Native]
tags: [Cloud Native]
---

# Kubernetes-基础

2021/09/14

## 前言

 由于有段时间没接触Kubernetes，最近面试运维开发的时候又经常问到，因此重新体系的入门下Kubernetes，主要基于《Kubernetes权威指南》和自己的一些结合。同时因为个人难以配置Kubernetes环境（需要至少三个实例，有着visualBox的方案，但自己也不是特别需要操作的实践经历）且Minikube基本已经完全体验过，内容更多偏向于基础理论的回顾和学习。

## 入门

**「What」Kubernetes是什么？**
是用于自动部署，扩展和管理容器化应用程序的开源系统。

这里不去细究Kubernetes的好处，大致讲下**「Why」为什么要用Kubernetes？**
从目前以我当前的所知来理解，是一种技术趋势，通过虚拟化硬件（虚拟化技术使得硬件也能跟上软件灵活性）将业务无关的东西从软件层面剥离开，去解决基础设施层面的分布式问题。

> 最重要的就是，一旦虚拟化的硬件能够跟上软件的灵活性，那些与业务无关的技术性问题便有可能从软件层面剥离，悄无声息地解决硬件基础设施之内，让软件得以只专注业务，真正“围绕业务能力构建”产品。

**那Kubernetes能做什么？**

- **服务发现和负载均衡**

  Kubernetes 可以使用 DNS 名称或自己的 IP 地址公开容器，如果进入容器的流量很大， Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。

- **存储编排**

  Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。

- **自动部署和回滚**

  你可以使用 Kubernetes 描述已部署容器的所需状态，它可以以受控的速率将实际状态 更改为期望状态。例如，你可以自动化 Kubernetes 来为你的部署创建新容器， 删除现有容器并将它们的所有资源用于新容器。

- **自动完成装箱计算**

  Kubernetes 允许你指定每个容器所需 CPU 和内存（RAM）。 当容器指定了资源请求时，Kubernetes 可以做出更好的决策来管理容器的资源。

- **自我修复**

  Kubernetes 重新启动失败的容器、替换容器、杀死不响应用户定义的 运行状况检查的容器，并且在准备好服务之前不将其通告给客户端。

- **密钥与配置管理**

  Kubernetes 允许你存储和管理敏感信息，例如密码、OAuth 令牌和 ssh 密钥。 你可以在不重建容器镜像的情况下部署和更新密钥和应用程序配置，也无需在堆栈配置中暴露密钥。

### 概览

**在Kubernetes中，Service是分布式集群架构的核心。**Service的服务进程通常基于Socket通信方式对外提供服务，如Redis、MySQL、WebServer等，每个Service通常由多个相关的服务进程提供服务，每个服务进程都有一个独立的Endpoint（IP+Port）访问点，但是Kubernetes可以使得我们通过Service（ClusterIP+Service Port）连接制定的服务。

同时Kubernetes有这内建的透明负载均衡和故障恢复机制，因此能保证后端服务的一个高可用和动态的资源调度，不会影响服务本身的调用。同时这个Service一旦创建就不再变化，意味着我们不再为Kubernetes集群中应用服务进程IP地址变化的问题。

容器提供了强大的隔离功能，因此为了将Service进程组放入容器作为隔离，Kubernetes有更高层次的抽象概念Pod，将每个Service进程包装到相应的Pod中，成为Pod中的Container运行。Pod在设计的时候需要将密切相关的服务进程组放入到同一个Pod中，同时**Pod作为Kubernetes管理的最小运行单元**。

为了建立Service和Pod间的关联关系，Kubernetes首先给每个Pod都填上一个Label，如给运行的MySQL配置`label.name=mysql`标签，同时给相应的Service定义Label Selector（标签选择器），MySQL Service的选择条件就应该为`label.name=mysql`，解决了Pod与Service关联的问题。同时并不是所有的Pod都会映射到一个Service，只有提供服务（对外或是对内）的Pod才会被映射成一个Service。

**在集群管理方面。**Kubernetess将集群中的机器划分为一个Master和多个Node。在Master上运行这集群管理相关的一些进程：kube-apiserver、kube-controller-manager和kube-scheduler，这些进程实现了整个集群的资源管理、Pod的调度、弹性伸缩、安全控制、系统监控和纠错等管理功能，并且都是自动完成。而Node作为工作节点，运行着真正的应用程序，在Node上运行着kubelet、kube-proxy和container容器运行时服务进程，负载Pod的创建、启动、监控、重启、销毁，以及实现软件模式的负载均衡。

## 基本概念

### 资源对象

---

#### 资源对象的类型

在Kubernetes上的基本概念和术语大多是围绕资源对象（Resource Object）来说，总体上可以分为两类：

1. 某种资源对象，如Node、Pod、Service、Volume

2. 与资源对象相关的事物和动作，如Label、Annotation、Namespace、Deployment等

资源对象一般包含着几个通用属性：Version、Kind、Name、Label、Annotation

- 在Version里，通常包含了此对象所属的资源组，一些资源对象的属性会随着版本的升级和变化

- 类别的对象用于定义资源对象的类型

- Name、Label、Annotation术语资源对象的元数据（metadata）。

这里需要注意几点：

1. 资源对象的名称需要唯一。

2. Label是个很重要的数据来表明该资源对象的特征、类别，以此实现对象之间的关联、控制和协作。

3. 注解可被理解为一种特殊的标签，通常用于实现资源对象属性的自定义拓展。

通过YAML/JSON可以面向资源创建一个对象，并且统一保存在etcd这种非关系数据库中，以实现最快的读写速度，此外所有的资源对象都可以通过Kubernetes提供的kubectl实现CRUD等操作。

#### 资源对象的生命周期

一些资源对象有着自己的生命周期和相应的状态，如Pod创建完提交后，它会处于等待调度状态，调度成功后为Pending，等待镜像的拉取和启动，启动后为Running，正常停止后为Succeded，非正常停止后为Failed。

这里如Pod的生命周期之后会细讲。

### 集群类

---

Cluster表示一个Master和若干个Node组成的Kubernetes集群。

#### Master

> 在每个Kubernetes集群中都需要又一个或一组Master，来进行整个集群的管理和控制，通常占据着一个独立的服务器（高可用集群建议至少3台）。

在Master下运行着以下关键进程：

- kube-apiserver：提供HTTP RESTful API接口的主要服务，是Kubernetes进行所有资源CRUD的唯一入口，也是集群控制的入口进程。

- kube-controller-manager：Kubernetes里所有资源对象的自动化配置中心，资源对象的管理者。

- kube-scheduler：负责资源（Pod调度）调度的进程。

- etcd：一个键值对数据库，作为保存所有集群的后台数据库。

#### Node

> 除了Master以外的所有服务器都可以称为Node，Node可以是物理机也可以是虚拟机，是Kubernetes的工作负载节点。当某个Node宕机时，其上的工作负载会被Master自动转移到其他Node上。

在Node上有着以下关键进程：

- kubelet：负责Pod对应容器的创建、启停等任务，同时与Master进行密切协作，实现Master传递下来的指令。

- kube-proxy：实现Service的通信和负载均衡的服务。

- containerd（容器运行时）：负责本机的容器创建和管理。

通过`kubectl describe node <node_name>`可以查看某个Node详细信息，这里不详细讲述。

#### Namespace

> namespace是一个很重要的概念，实现了多租户的资源隔离，属于不同namespace的资源对象从逻辑上相互隔离。

在Kubernetes中，Master会自动创建两个namespace——default和kube-system，分别用于系统级相关的资源对象如网络组件、DNS组件、监控类组件等和用户空间的业务逻辑资源。

## 核心概念

应用类的概念和相应的资源对象类型是最多的，同时也是一些核心的概念。

### Service

> 应用类的相关资源对象主要是围绕Service和Pod的两个核心对象来展开。

Service是应用服务的抽象，通过Labels为应用提供负载均衡和服务发现。匹配Labels和Pod IP+Port组成的Endpoints，由kube-proxy负载到这些Endpoints上。

每个Service都会自动分配一个cluster IP（仅在集群内部可访问的虚拟地址）和DNS名称，其他容器通过该域名进行服务访问。

![](https://cdn.jsdelivr.net/gh/edgarding77/images@latest/wallpaper/k8s-service.png)



#### Cluster IP

每个Pod都会分配一个单独的IP地址，而且每个Pod都提供一个独立的Endpoint（Pod IP + containerPort）以便被客户端访问。Service一旦被创建，Kubernetes就会被它分配一个全局唯一的虚拟IP地址——ClusterIP，并且在Service的整个生命周期内，其ClusterIP都是不会发生改变，这样使得每个服务具备了唯一IP地址通信节点。

#### 外网访问问题

Kubernetes的三种IP，这三种IP分别是：

- Node IP：Node的IP地址 - 每个节点的物理网卡IP地址，真实IP，可以直接通过该网络进行通信。

- Pod IP：Pod的IP地址 - 每个Pod的IP地址，在使用Docker作为容器支持的情况下，是Docker Engine根据docker()网桥的IP地址段进行分配的，通常是一个虚拟的二层网络。

- Service IP：Service的IP地址

  - Node Port：在Kubernetes集群，Service的Cluster IP地址属于集群内的地址，无法在对外直接使用该地址，因此引入Node Port解决外部应用访问集群内服务的问题。实现方式：在每个Node需要提供外部访问服务的Service开启一个对应TCP监听端口，外部只需要任意一个IP+NodePort即可访问。

  当然

### Pod

> **Pod是Kubernetes的基本调度单元。**

**「Why」为什么需要有这么一种概念和结构呢？**

- 为多进程之间的协作提供一个抽象模型，使用Pod作为基本的调度、复制等管理工作的最小单位，让多个应用进程之间能有效的调度和伸缩。

- Pod里的多个业务容器共享Pause容器的IP，共享Pause容器挂接的Volume，这样既简化了密切关联的业务容器之间的通信问题，也解决了文件共享问题。

容器的本质是对cgroups和namespaces说提供的隔离能力的一种封装，容器蕴含的隔离性仅针对于单个进程的额外局限，然而cgroups和namespaces原本都是针对单个进程的额外局限，同一个进程组可以共享着相同的访问权限与资源配额。而实现容器编排的第一个拓展点，就是要找到容器领域中与“进程组”相对应的概念，这是实现容器从隔离到协作的第一步 —— **Pod**。

**Pod的结构**：

- Pause：gcr.io/google_containers/pause_amd64

- user container1:xxxiamge....

同时，在网络通信上，每个Pod分配为一个IP，Pod里的容器共享该IP，Kubernetes**要求底层网络支持集群内任意两个Pod之间的TCP/IP直接通信**，这通常采用虚拟二层网络技术实现，例如Open vSwitch、Flannel等。

Pod也有两种类型：普通的Pod和Static Pod，后者的特点在于，它并没有存放在Kubernetes的etcd中，而是被存放在某个具体的Node上的一个具体文件中，并且只能在该Node启动运行。

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: web
  labels:
    name: web
spec:
  containers:
  - name: web
    image: kubeguide/tomcat-app:v1
    ports:
    - containerPort: 8080 
```

#### Pod共享实现细节

  Pod 内部多个容器共享 UTS、IPC、网络等名称空间是通过一个名为** Infra Container **的容器来实现的，这个容器是整个 Pod 中第一个启动的容器，只有几百 KB 大小（代码只有很短的几十行，见**[**这里**](https://github.com/kubernetes/kubernetes/tree/master/build/pause)），Pod 中的其他容器都会以 Infra Container 作为父容器，UTS、IPC、网络等名称空间实质上都是来自 Infra Container 容器。

  如果容器设置为共享 PID 名称空间的话，Infra Container 中的进程将作为 PID 1 进程，**其他容器的进程将以它的子进程的方式存在**，此时将由 Infra Container 来负责进程管理（譬如清理[僵尸进程](https://en.wikipedia.org/wiki/Zombie_process)）、感知状态和传递状态。

  由于 Infra Container 的代码除了注册 SIGINT、SIGTERM、SIGCHLD 等信号的处理器外，就只是一个以 pause()方法为循环体的无限循环，永远处于 Pause 状态，所以也常被称为**“Pause Container”**

#### Pod生命周期

  通过`kubectl explain pod.status`可以了解关于Pod状态的一些信息，Pod的状态定义在`PodStatus`对象中，其中有个`phase`字段，大概有这么几种：

  - 挂起（Pending）：Pod 信息已经提交给了集群，但是还没有被调度器调度到合适的节点或者 Pod 里的镜像正在下载

  - 运行中（Running）：该 Pod 已经绑定到了一个节点上，Pod 中所有的容器都已被创建。至少有一个容器正在运行，或者正处于启动或重启状态

  - 成功（Succeeded）：Pod 中的所有容器都被成功终止，并且不会再重启

  - 失败（Failed）：Pod 中的所有容器都已终止了，并且至少有一个容器是因为失败终止。也就是说，容器以非`0`状态退出或者被系统终止

  - 未知（Unknown）：因为某些原因无法取得 Pod 的状态，通常是因为与 Pod 所在主机通信失败导致的

  除此之外，PodStatus 对象中还包含一个 PodCondition 的数组，里面包含的属性有：

  - lastProbeTime：最后一次探测 Pod Condition 的时间戳。

  - lastTransitionTime：上次 Condition 从一种状态转换到另一种状态的时间。

  - message：上次 Condition 状态转换的详细描述。

  - reason：Condition 最后一次转换的原因。

  - status：Condition 状态类型，可以为 “True”, “False”, and “Unknown”.

  - type：Condition 类型，包括以下方面：

  - PodScheduled（Pod 已经被调度到其他 node 里）

  Ready（Pod 能够提供服务请求，可以被添加到所有可匹配服务的负载平衡池中）

  - Initialized（所有的init containers已经启动成功）

  - Unschedulable（调度程序现在无法调度 Pod，例如由于缺乏资源或其他限制）

  - ContainersReady（Pod 里的所有容器都是 ready 状态）



### 容器的发展

---

> 通过了解容器在来衍生思考kubernetes可以有着意想不到的收获。

1. **文件隔离-chroot：**功能是当某个进程经过`chroot`操作之后，它的根目录就会被锁定在命令参数所指定的位置，以后它或者它的子进程将不能再访问和操作该目录之外的其他文件。
这里需要提到UNIX的设计哲学，“一切资源皆可视为文件”。

2. **访问隔离-namespace：**用于避免开发者提供的API相互冲突，Linux的namespace的内核提供的全局资源封装，是内核针对进程设计的访问隔离机制。

3. **资源隔离-cgroups：**如果要让一台物理计算机中的各个进程看起来像独享整台虚拟计算机的话，不仅要隔离各自进程的访问操作，还必须能独立控制分配给各个进程的资源使用配额，不然的话，一个进程发生了内存溢出或者占满了处理器，其他进程就莫名其妙地被牵连挂起，这样肯定算不上是完美的隔离。
Linux 系统解决以上问题的方案是[控制群组](https://en.wikipedia.org/wiki/Cgroups)（Control Groups，目前常用的简写为`cgroups`），**它与名称空间一样都是直接由内核提供的功能，用于隔离或者说分配并限制某个进程组能够使用的资源配额，资源配额包括处理器时间、内存大小、磁盘 I/O 速度，等等**。

4. **系统封装-LXC：**当文件系统、访问、资源都可以被隔离后，容器已经有它降生所需的全部前置支撑条件，并且 Linux 的开发者们也已经明确地看到了这一点。为降低普通用户综合使用namespaces、cgroups这些低级特性的门槛，2008 年 Linux Kernel 2.6.24 内核刚刚开始提供cgroups的同一时间，就马上发布了名为Linux 容器（LinuX Containers，LXC）的系统级虚拟化功能。

5. **应用封装-Docker：**这里可以参考[StackOverflow上的一段对话](https://stackoverflow.com/questions/17989306/what-does-docker-add-to-lxc-tools-the-userspace-lxc-tools/)。

6. **集群封装-Kubernetes：**Docker的成功靠的是理念，Kubernetes的成功可能是一种大势所趋。

### Label

> Label是Kubernetes系统中另一个核心概念，用于识别Kubernetes对象，以Key-Value的方式附加在各种对象上（key最长不能超过63字节，value 可以为空，也可以是不超过253字节的字符串），如Node、Pod、Service、Deployment等，一个资源对象可以定义任意数量的Label，同一个Label也可以添加到任意数量的资源对象上。

通过给指定的资源对象进行添加/删除捆绑一个或多个Label实现多维度的资源分组管理功能，以便灵活、方便地进行资源分配、调度、配置、部署等管理工作。

通过Label Selector，实现像sql一样对Label的筛选，从而对管理对象的分组管理，实现集群的高可用：

```YAML
apiVersion: v1
kind: pod
metadata:
  name: web
  labels:
    app: myweb

```

```YAML
spec:
  selector:
    app: myweb

```

### Deployment

> Deployment的出现是为了解决Pod创建的麻烦，Deployment类似于一个Pod Template，自动创建多个Pod实例，这个就是Deployment所要完成的事。

以下为一个Deployment例子：

```YAML
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myweb
  template:
    metadata:
      labels:
        app: myweb
      spec:

```

这里有几个重要的属性：

- replicas：Pod的副本数目，Deployment会一直自动创建到符合replicas数目的Pod

- selector：目标Pod的标签选择器

- template：用于自动创建新的Pod副本模版

Deployment有个很重要的特性——**自动控制**，如果Pod所在的Node发生宕机，Kubernetes会观察到该故障，并自动创建一个新的Pod调度到其他合适的节点上，Kubernetes会实时监控集群中目标Pod的replicas数目，并且尽力于Deployment中的replicas数量保持一致。**因此，哪怕只有一个Pod，也尽量通过Deployment进行部署。**

### StatefulSet

Deployment对象用来实现无状态服务的多副本自动控制功能的，那么有状态的服务，比如ZooKeeper集群、MySQL高可用集群、Kafka集群等如何去实现自动部署和管理就是个问题。

有状态集群节点有如下特殊特性：

- 每个节点都由固定的身份ID，通过该ID实现集群内成员的相互发现

- 集群规模较为固定

- 集群每个节点的状态通过持久化数据到永久存储中

- 集群成员节点的启动顺序（以及关闭顺序）通常也是固定

而通过Deployment控制Pod副本数量来实现以上有状态的集群，就难以实现，如Deployment创建的Pod因为Pod的名称是随机产生的，就无法确定唯一不变ID，同时Pod的启动顺序也不定。

因此Kubernetes引入专门的资源对象——StatefulSet，从本质上来说，可被看作Deployment/RC的一个变种，有着以下特性：

- StatefulSet里的每个Pod都由稳定的，唯一的网络标识，可以用来发现集群内的其他成员。

- StatefulSet控制的Pod副本的启停顺序是受控的，操作第n个Pod时，前n-1个Pod得时runtime状态

- StatefulSet里的Pod采用稳定的持久化存储卷，通过PV或PVC来实现，删除Pod时，存储卷不受影响。

### Job

> 批处理应用的特点是一个或多个进程处理一组数据（图像、文件、视频等），在这组数据处理完后，批处理任务自动结束。为了解决这类应用，Kubernetes引入Job资源对象概念。

Jobs控制器提供了两个控制并发数的参数：`completions`（表示运行任务的总数）和`parallelism`（表示并发运行的个数），从该特性可以得知Job类型的Pod都是短暂运行的，可以视为一组容器，其中每个容器也都仅运行一次，且Job生成的Pod副本是不可能自动重启的。

#### CronJob

为了解决Job的Pod副本restartPolicy=Never的问题，引入CronJob，可以周期性的执行某个任务。

### 应用配置问题

如上，总结，三种应用建模的资源对象：

- 无状态服务的建模：Deployment

- 有状态集群的建模：StatefulSet

- 批处理应用的建模：Job

在进行应用建模时，在解决应用需要在不同的环境下修改配置的问题？涉及ConfigMap和Secret两个对象。

#### ConfigMap

就是一个保存配置项（key=value）的一个Map，ConfigMap是分布式系统中的“配置中心”的独特实现之一。几乎所有的应用都需要一个静态的配置文件来提供启动参数，但这个应用是分布式的，这配置文件的分发就是个麻烦，通常引入一个新的API，从而导致应用的耦合和侵入，Kubernetes采用一个简单的方案来规避：

- 用户将配置内容保存到ConfigMap，文件名可以作为key，value就是整个文件的内容，多个配置文件可以放入同一个ConfigMap。

- 在Pod里将ConfigMap定义为特殊的Volume作为挂载，在Pod被调度到某个具体Node上，ConfigMap里的配置文件就会被自动还原到本地目录，然后映射到Pod里指定的配置目录，这样用户的程序就可以无感知地读取配置。

- 在ConfigMap发生修改后，Kubernetes会重新获取ConfigMap的内容，并且在目标节点上更新对应的文件。

#### Secret

Secret也用于解决应用配置的问题，不过解决的是对敏感配置的问题，如数据库密码、token等，同时Secret中的数据要求以BASE64编码格式加密存放。

### 应用运维问题

通过Kubernetes对Deployment的一些sepc状态，实现自动扩缩容的机制，如：

```YAML
kind: HorizontalPodAutoscalar
spec:
  maxReplicas: 10
  minReplicas: 1
  scaleTraget: 
    kind: Deployment
    name: apache
  targetCPUUtilizationPercentage: 90

```

张总张总张总这里可以的只，这是一个名为apache的Deployment的Pod的副本，但这些Pod副本的利用率值超过90%时，会触发自动动态扩容，限定副本的数量为1～10。

以上是一种HPA（Horizontal Pod Autoscaler）机制，实现水平扩缩容。同时还有VPA（Vertiacal Pod Autoscaler）Pod垂直自动扩缩容，根据容器资源使用率自动推测并设置Pod合理的CPU和内存的需求指标，从而更加精确地调度Pod，提升运维自动化水平。

### 存储类

存储类对象主要包括：Volume、Persistent Volume、PVC和StorageClass

#### Volume

首先，Kubernetes中的Volume定义在Pod上，被一个Pod和多个容器挂载到具体的目录下：其次Kubernetes中的Volume与Pod的生命周期相同，但与容器的生命周期不相关。

同时Volume也有一些丰富的类型，提供容器使用，如临时目录、宿主机目录、共享存储等：

1. emptyDir：用于Pod分配到Node时创建，初始内存为空。

2. hostPath：为Pod上挂载宿主机上的文件和目录，用于一些日志文件持久化等，因为不同的Node具有相同配置的Pod，会因为宿主机目录和文件有所不同导致对volume访问的结果不同。

3. 公有云Volume

4. 其他类型Volume

### 动态存储管理

Volume属于静态管理的存储，因此动态存储管理出现：PV（Persistent Volume）、StorageClass、PVC。

PV表示由系统动态创建的（dynamically provisioned）的一个存储卷，可以理解成Kubernetes集群中某个网络存储对应的一块存储，与Volume，但不同在于PV不是定义在Pod上的，而是独立于Pod之外定义。

而Kubernetes支持的存储系统有很多种，那么系统怎么知道从哪个存储系统中创建生命规格的PV存储卷？这就是涉及StorageClass和PVC，StorageClass用于描述存储系统的特征，而PVC（PV Claim）用于表明申请的PV规格。这里不去过多赘述。

### 安全类

Kubernetes中的用户主要有两类：普通用户和运维人员，开发的Pod应用需要通过API Server查询、创建以及管理其他相关资源对象，所以这类用户才是关键用户。因此，Kubernetes设计了Service Account资源对象，为Pod提供必要的身份认证，同时带来基于角色访问控制权限系统——RBAC（Role-Based Access Control）。

通过Service Acount所处的namespace，可以访问相对应namespace的Pod，同时Service Account通过Secret来保存对应的用户（应用）身份凭证，包含CA根证书（ca.crt）和签名后的Token。API Server通过接受到的Token来确定Service Account身份。

当容器创建的时候，Kubernets会将对应的Secret对象中的身份信息（token等）持久化到容器固定位置，但容器里的用户进程通过Kubernetes提供的客户端API进行访问API Server，这些API会自动读取这些身份信息文件，并将附加HTTPS请求中传递给API Server实现身份认证逻辑。在身份认证通过后，就涉及授权的问题，这就是RBAC解决的问题。

这里也不去过多赘述Role、ClusterRole、RoleBinding等概念，之后会详细讲。

## 参考

- 《Kubernetes权威指南》

- [https://kubernetes.io/zh/](https://kubernetes.io/zh/)

- [https://cloudnative.to/](https://cloudnative.to/)

- [https://www.servicemesher.com/istio-handbook/concepts/overview.html](https://www.servicemesher.com/istio-handbook/concepts/overview.html)

- [https://jimmysong.io/awesome-cloud-native/](https://jimmysong.io/awesome-cloud-native/)

- [https://edu.aliyun.com/roadmap/cloudnative?from=timeline](https://edu.aliyun.com/roadmap/cloudnative?from=timeline)

- [https://jimmysong.io/blog/microservice-reading-notes/](https://jimmysong.io/blog/microservice-reading-notes/)



