## 1.Dubbo 简介

Apache Dubbo 是一款 RPC 服务开发框架，用于解决微服务架构下的服务治理与通信问题，官方提供了 Java、Golang 等多语言 SDK 实现。使用 Dubbo 开发的微服务原生具备相互之间的远程地址发现与通信能力， 利用 Dubbo 提供的丰富服务治理特性，可以实现诸如服务发现、负载均衡、流量调度等服务治理诉求。Dubbo 被设计为高度可扩展，用户可以方便的实现流量拦截、选址的各种定制逻辑。

Dubbo3 定义为面向云原生的下一代 RPC 服务框架。3.0 基于 Dubbo 2.x 演进而来，在保持原有核心功能特性的同时， Dubbo3 在易用性、超大规模微服务实践、云原生基础设施适配、安全性等几大方向上进行了全面升级。

## 2.Dubbo 基本工作流程

![dubbo-rpc](asserts/rpc.png)

Dubbo 首先是一款 RPC 框架，它定义了自己的 RPC 通信协议与编程方式。如上图所示，用户在使用 Dubbo 时首先需要定义好 Dubbo 服务；其次，是在将 Dubbo 服务部署上线之后，依赖 Dubbo 的应用层通信协议实现数据交换，Dubbo 所传输的数据都要经过序列化，而这里的序列化协议是完全可扩展的。 使用 Dubbo 的第一步就是定义 Dubbo 服务，服务在 Dubbo 中的定义就是完成业务功能的一组方法的集合，可以选择使用与某种语言绑定的方式定义，如在 Java 中 Dubbo 服务就是有一组方法的 Interface 接口，也可以使用语言中立的 Protobuf Buffers [IDL 定义服务](https://cn.dubbo.apache.org/zh/overview/tasks/triple/idl/)。定义好服务之后，服务端（Provider）需要提供服务的具体实现，并将其声明为 Dubbo 服务，而站在服务消费方（Consumer）的视角，通过调用 Dubbo 框架提供的 API 可以获得一个服务代理（stub）对象，然后就可以像使用本地服务一样对服务方法发起调用了。 在消费端对服务方法发起调用后，Dubbo 框架负责将请求发送到部署在远端机器上的服务提供方，提供方收到请求后会调用服务的实现类，之后将处理结果返回给消费端，这样就完成了一次完整的服务调用。如图中的 Request、Response 数据流程所示。

> 需要注意的是，在 Dubbo 中，我们提到服务时，通常是指 RPC 粒度的、提供某个具体业务增删改功能的接口或方法，与一些微服务概念书籍中泛指的服务并不是一个概念。

在分布式系统中，尤其是随着微服务架构的发展，应用的部署、发布、扩缩容变得极为频繁，作为 RPC 消费方，如何动态的发现服务提供方地址成为 RPC 通信的前置条件。Dubbo 提供了自动的地址发现机制，用于应对分布式场景下机器实例动态迁移的问题。如下图所示，通过引入注册中心来协调提供方与消费方的地址，提供者启动之后向注册中心注册自身地址，消费方通过拉取或订阅注册中心特定节点，动态的感知提供方地址列表的变化。

![arch-service-discovery](asserts/architecture.png)

## 3.Dubbo 核心特性

### 高性能 RPC 通信协议

跨进程或主机的服务通信是 Dubbo 的一项基本能力，Dubbo RPC 以预先定义好的协议编码方式将请求数据（Request）发送给后端服务，并接收服务端返回的计算结果（Response）。RPC 通信对用户来说是完全透明的，使用者无需关心请求是如何发出去的、发到了哪里，每次调用只需要拿到正确的调用结果就行。除了同步模式的 Request-Response 通信模型外，Dubbo3 还提供更丰富的通信模型选择：

- 消费端异步请求(Client Side Asynchronous Request-Response)
- 提供端异步执行（Server Side Asynchronous Request-Response）
- 消费端请求流（Request Streaming）
- 提供端响应流（Response Streaming）
- 双向流式通信（Bidirectional Streaming）

具体可参见各语言 SDK 实现的可选协议列表 或 [Triple协议](https://cn.dubbo.apache.org/zh/docs3-v2/java-sdk/concepts-and-architecture/triple/)

#### 自动服务（地址）发现

Dubbo 的服务发现机制，让微服务组件之间可以独立演进并任意部署，消费端可以在无需感知对端部署位置与 IP 地址的情况下完成通信。Dubbo 提供的是 Client-Based 的服务发现机制，使用者可以有多种方式启用服务发现：

- 使用独立的注册中心组件，如 [Nacos](https://nacos.io/)、Zookeeper、Consul、Etcd 等。
- 将服务的组织与注册交给底层容器平台，如 Kubernetes，这被理解是一种更云原生的使用方式

### 运行态流量管控

透明地址发现让 Dubbo 请求可以被发送到任意 IP 实例上，这个过程中流量被随机分配。当需要对流量进行更丰富、更细粒度的管控时，就可以用到 Dubbo 的流量管控策略，Dubbo 提供了包括负载均衡、流量路由、请求超时、流量降级、重试等策略，基于这些基础能力可以轻松的实现更多场景化的路由方案，包括金丝雀发布、A/B测试、权重路由、同区域优先等，更酷的是，Dubbo 支持流控策略在运行态动态生效，无需重新部署。具体可参见：

- [流量治理示例](https://cn.dubbo.apache.org/zh/overview/tasks/traffic-management)

### 丰富的扩展组件及生态

Dubbo 强大的服务治理能力不仅体现在核心框架上，还包括其优秀的扩展能力以及周边配套设施的支持。

### 面向云原生设计

Dubbo 从设计上是完全遵循云原生微服务开发理念的，这体现在多个方面，首先是对云原生基础设施与部署架构的支持，包括 容器、Kubernetes 等，Dubbo Mesh 总体解决方案也在 3.1 版本正式发布；另一方面，Dubbo 众多核心组件都已面向云原生升级，包括 Triple 协议、统一路由规则、对多语言的支持。