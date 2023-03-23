# kubernet_CRD
kubernet_CRD
 Kubernetes typically takes care of  deploying and managing containers. And while this is, in fact, Kubernetes’s main job, it can actually do much more than that. something called custom resource definitions, or CRDs for short.
 
 kubernet configmap vs CRD
 ConfigMaps by design are meant to provide configuration for your pods. They can be mounted as files or environment variables into the pod. They work well if you have well-defined config files like, for example, Apache or MySQL config.

CRDs can also be consumed by pods but only by contacting the Kubernetes API. They simply have a different purpose than ConfigMaps. They’re not meant to be used to provide configuration to your pods but to extend the Kubernetes API in order to build custom automation.
https://www.cnblogs.com/liugp/p/16683916.html
CRD（Custom Resource Definition） 本身是一种 Kubernetes 内置的资源类型，即自定义资源的定义，用于描述用户定义的资源是什么样子。CRD 的相关概念：
![image](https://user-images.githubusercontent.com/12657295/227265136-f48c908d-126e-4061-ad6d-979c1703b3d3.png)


CRD 是 v1.7 +新增的无需改变代码 就可以扩展 Kubernetes API 的机制，用来管理自定义对象。它实际上是ThirdPartyResources（TPR） 的升级版本，而 TPR 已经在 v1.8 中删除。
从 Kubernetes 的用户角度来看，所有东西都叫资源 Resource，就是 Yaml 里的字段 Kind 的内容，例如 Service、Deployment 等。
除了常见内置资源之外，Kubernetes 允许用户自定义资源 Custom Resource，而 CRD 表示自定义资源的定义。
当你创建新的 CustomResourceDefinition（CRD）时，Kubernetes API 服务器会为你所指定的每个版本生成一个新的 RESTful 资源路径。
基于 CRD 对象所创建的自定义资源可以是名字空间作用域的，也可以是集群作用域的， 取决于 CRD 对象 spec.scope 字段的设置。
定义 CRD 对象的操作会使用你所设定的名字和模式定义（Schema）创建一个新的定制资源， Kubernetes API 负责为你的定制资源提供存储和访问服务。 CRD 对象的名称必须是合法的 DNS 子域名。

定制资源 和 定制控制器
资源（Resource） 是 Kubernetes API 中的一个端点， 其中存储的是某个类别的 API 对象的一个集合。 例如内置的 Pod 资源包含一组 Pod 对象。

定制资源（Custom Resource） 是对 Kubernetes API 的扩展， 定制资源所代表的是对特定 Kubernetes 安装的一种定制。 不过，很多 Kubernetes 核心功能现在都用定制资源来实现，这使得 Kubernetes 更加模块化。

定制资源可以通过动态注册的方式在运行中的集群内或出现或消失，集群管理员可以独立于集群更新定制资源。

一旦某定制资源被安装，用户可以使用 kubectl 来创建和访问其中的对象， 就像他们为 Pod 这种内置资源所做的一样。

2）定制控制器
就定制资源本身而言，它只能用来存取结构化的数据。 当你将定制资源与定制控制器（Custom Controller） 结合时， 定制资源就能够提供真正的声明式 API（Declarative API）。
Operator 模式就是将定制资源与定制控制器相结合的。
3）Operator 介绍
Kubernetes CRD Operator = kubernetes CRD + custom controller

Operator 是由 CoreOS 开发的，用来扩展 Kubernetes API，特定的应用程序控制器，它用来创建、配置和管理复杂的有状态应用，如数据库、缓存和监控系统。
Operator 基于 Kubernetes 的资源和控制器概念之上构建，但同时又包含了应用程序特定的领域知识。创建Operator 的关键是CRD（自定义资源）的设计。
Operator是 Kubernetes 的扩展软件， 它利用定制资源管理应用及其组件。 Operator 遵循 Kubernetes 的理念，特别是在控制器方面。
Operator 模式会封装你编写的（Kubernetes 本身提供功能以外的）任务自动化代码。
Kubernetes 的 Operator 模式概念允许你在不修改 Kubernetes 自身代码的情况下， 通过为一个或多个自定义资源关联控制器来扩展集群的能力。
Operator 是 Kubernetes API 的客户端。Operator 旨在为其管理的资源提供强大的自动化功能，因此它还需要一些额外的支持性代码。
1、Operator Framework

Operator Framework 同样也是 CoreOS 开源的一个用于快速开发 Operator 的工具包，该框架包含两个主要的部分：

Operator SDK——无需了解复杂的 Kubernetes API 特性，即可让你根据你自己的专业知识构建一个 Operator 应用。
Operator Lifecycle Manager OLM——帮助你安装、更新和管理跨集群的运行中的所有 Operator（以及他们的相关服务）
在这里插入图片描述

2、Operator 安装
Operator SDK 提供以下工作流来开发一个新的 Operator：

使用 SDK 创建一个新的 Operator 项目
通过添加自定义资源（CRD）定义新的资源 API
指定使用 SDK API 来 watch 的资源
定义 Operator 的协调（reconcile）逻辑
使用 Operator SDK 构建并生成 Operator 部署清单文件
3、安装 Operator SDK
下载地址：https://github.com/operator-framework/operator-sdk/releases
operator sdk 官方文档：https://sdk.operatorframework.io/docs/installation/

wget https://github.com/operator-framework/operator-sdk/releases/download/v1.23.0/operator-sdk_linux_amd64

# 添加可执行权限
 
chmod +x operator-sdk_linux_amd64
 
# 添加软链
ln -s /opt/k8s/crd/Operator/operator-sdk_linux_amd64 /usr/local/bin/operator-sdk
4、Operator 简单使用
【示例1】快速简单使用

operator-sdk init --domain=example.com --repo=github.com/example-inc/memcached-operator
# 步骤二: 创建 API（operator-sdk create api --group cache --version v1 --kind Memcached --resource=true --controller=true）
operator-sdk create api --group cache --version v1 --kind Memcached --resource=true --controller=true
# 步骤三: 构建镜像-需要本地存在 docker 环境（make docker-build IMG=liumiaocn/memcache:v1）
make docker-build IMG=liumiaocn/memcache:v1
# 步骤四: 运行Operator-需要环境具备 K8s/K3s（make install && make deploy IMG=liumiaocn/memcache:v1）
make install && make deploy IMG=liumiaocn/memcache:v1
# 步骤五：创建自定义资源
kubectl apply -f config/samples/cache_v1_memcached.yaml
# 步骤六：删除CR和相关资源（kubectl delete -f config/samples/cache_v1_memcached.yaml）
kubectl delete -f config/samples/cache_v1_memcached.yaml
这里只是简单的安装部署使用，后面会单独拿一篇文章来详细介绍Operator 。

4）Kubernetes API 聚合层
使用聚合层（Aggregation Layer），用户可以通过附加的 API 扩展 Kubernetes， 而不局限于 Kubernetes 核心 API 提供的功能。 这里的附加 API 可以是现成的解决方案，比如 metrics server， 或者你自己开发的 API。

聚合层不同于 定制资源（Custom Resources）。 后者的目的是让 kube-apiserver 能够识别新的对象类别（Kind）。

聚合层在 kube-apiserver 进程内运行。在扩展资源注册之前，聚合层不做任何事情。

要注册 API，你可以添加一个 APIService 对象，用它来 “申领” Kubernetes API 中的 URL 路径。 自此以后，聚合层将把发给该 API 路径的所有内容（例如 /apis/myextension.mycompany.io/v1/…） 转发到已注册的 APIService。

5）声明式 APIs
典型地，在声明式 API 中：

你的 API 包含相对而言为数不多的、尺寸较小的对象（资源）。
对象定义了应用或者基础设施的配置信息。
对象更新操作频率较低。
通常需要人来读取或写入对象。
对象的主要操作是 CRUD 风格的（创建、读取、更新和删除）。
不需要跨对象的事务支持：API 对象代表的是期望状态而非确切实际状态。
命令式 API（Imperative API）与声明式有所不同。 以下迹象表明你的 API 可能不是声明式的：

客户端发出“做这个操作”的指令，之后在该操作结束时获得同步响应。
客户端发出“做这个操作”的指令，并获得一个操作 ID，之后需要检查一个 Operation（操作） 对象来判断请求是否成功完成。
你会将你的 API 类比为远程过程调用（Remote Procedure Call，RPCs）。
直接存储大量数据；例如每个对象几 kB，或者存储上千个对象。
需要较高的访问带宽（长期保持每秒数十个请求）。
存储有应用来处理的最终用户数据（如图片、个人标识信息（PII）等）或者其他大规模数据。
在对象上执行的常规操作并非 CRUD 风格。
6）添加定制资源
Kubernetes 提供了两种方式供你向集群中添加定制资源：

CRD 相对简单，创建 CRD 可以不必编程。
API 聚合 需要编程，但支持对 API 行为进行更多的控制，例如数据如何存储以及在不同 API 版本间如何转换等。
Kubernetes 提供这两种选项以满足不同用户的需求，这样就既不会牺牲易用性也不会牺牲灵活性。

聚合 API指的是一些下位的 API 服务器，运行在主 API 服务器后面；主 API 服务器以代理的方式工作。这种组织形式称作 API 聚合（API Aggregation，AA） 。 对用户而言，看起来仅仅是 Kubernetes API 被扩展了。
CRD 允许用户创建新的资源类别同时又不必添加新的 API 服务器。 使用 CRD 时，你并不需要理解 API 聚合。
CRD	聚合 API
无需编程。用户可选择任何语言来实现 CRD 控制器。	需要编程，并构建可执行文件和镜像。
无需额外运行服务；CRD 由 API 服务器处理。	需要额外创建服务，且该服务可能失效。
一旦 CRD 被创建，不需要持续提供支持。Kubernetes 主控节点升级过程中自动会带入缺陷修复。	可能需要周期性地从上游提取缺陷修复并更新聚合 API 服务器。
无需处理 API 的多个版本；例如，当你控制资源的客户端时，你可以更新它使之与 API 同步。	你需要处理 API 的多个版本；例如，在开发打算与很多人共享的扩展时。
仅支持 CRUD 操作，例如 "logs" 或 "exec"的操作是不支持的。	支持 CRUD 之外的操作。
7）访问定制资源
Kubernetes 客户端库可用来访问定制资源。 并非所有客户端库都支持定制资源。Go 和 Python 客户端库是支持的。

当你添加了新的定制资源后，可以用如下方式之一访问它们：

kubectl
Kubernetes 动态客户端（DynamicClient），可以对任意Kubernetes资源进行RESTFful操作，包括CRD自定义资源。
你所编写的 REST 客户端
使用 Kubernetes 客户端生成工具 所生成的客户端。生成客户端的工作有些难度，不过某些项目可能会随着 CRD 或 聚合 API 一起提供一个客户端
