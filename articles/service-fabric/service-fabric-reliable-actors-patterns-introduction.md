<properties
   pageTitle="向模式和反模式引入 Azure Service Fabric 参与者"
   description="非常适用于 Service Fabric 参与者的设计模式"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/11/2015"
   wacn.date=""/>

# Reliable Actors 设计模式简介
Service Fabric 的 Reliable Actors 编程模型是围绕参与者模型构建的平台，用于解决实际云规模的实际问题。Azure Service Fabric 是一种平台，用于为云和本地构建易于部署和管理的高度可靠、可缩放的应用程序。本文旨在作为有关实际问题的实用文章，通读各种模式之后，你应能够了解如何使用 Service Fabric 参与者模型来构建“企业”解决方案或“云”解决方案。

## 模式
在此部分中，我们将列出我们在与客户合作过程中所利用的一组模式和关联方案。这些模式表示各类问题，适用于我们的客户在 Microsoft Azure 上构建的广泛解决方案。虽然这些方案以真实情况为基础，不过我们去掉了大多数特定于领域的问题，以使读者可更清晰地了解模式。你可能会发现很多示例代码十分简单或显而易见。我们包含该代码是为了实现完整性，而不是因为它特别巧妙或令人印象深刻。

本文中介绍的模式并不打算用于全面介绍或用作规范 — 一些开发人员可能会采用与我们提供的方式不同的方式来解决相同问题或模式。

[模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)

[模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)

[模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)

[模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)

[模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-things)

[模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

### 详细了解参与者，简要的历史
由 Hewitt et al. 撰写的[文章](http://dl.acm.org/citation.cfm?id=1624804)是参与者模型的起源，早在 1973 年便已发布，然而参与者模型只在最近才作为一种在分布式系统中处理并发和复杂性的方式而获得越来越多的注意。参与者模型支持相互隔离的各个细粒度对象（参与者）。它们通过异步消息传递进行通信，这可在参与者之间实现直接通信。参与者使用单线程语义进行执行。通过结合参与者状态的封装以及与其他参与者的隔离，这从参与者代码中消除了并发问题，从而简化了编写高度并行的系统。参与者在可用硬件资源池上动态创建。

[Erlang](http://www.erlang.org/) 是最受欢迎的参与者模型实现。开发人员已开始重新发现参与者模型，这刺激了对 Erlang 重新焕发兴趣以及创建类似于 Erlang 的新解决方案：[Scala](http://www.scala-lang.org/) 参与者、[Akka](http://akka.io)、[Akka.net](http://getakka.net/)、[DCell](http://research.microsoft.com/pubs/75988/dcell.pdf)。

## 简单了解 Azure Service Fabric
Azure 结构参与者是一种参与者模型实现，它借用了 Erlang 和分布式对象系统的一些想法，添加了一个参与者间接层并在一个利用 Azure Service Fabric 平台的集成编程模型中公开它们。

Azure 结构参与者的主要好处在于：1) **开发人员工作效率**（即使是对于非专家程序员）；以及 2) **默认情况下的透明可伸缩性**（程序员无需进行任何特殊工作）。Azure 结构参与者是在 Azure 结构上运行的 .NET 库，也是显著降低复杂分布式应用程序的开发难度并按照设计使生成的应用程序具有可伸缩性的工具。我们会在下面展开讨论每个好处。Azure 结构参与者编程模型通过提供以下关键抽象、保证和系统服务来提高专家和非专家程序员的工作效率。

* *熟悉的面向对象的编程 (OOP) 范例*。参与者是使用异步方法和属性实现声明的 .NET 参与者接口的 .NET 类。因此参与者向程序员显示为可以直接调用其方法/属性的远程对象。这会以完全透明的方式将方法调用转换为消息、将它们路由到正确终结点、调用目标参与者的方法并处理故障和个别状况，从而向程序员提供熟悉的 OOP 范例。

* *参与者的单线程执行。* Azure 结构运行时可保证参与者绝不会同时在多个线程上执行。通过结合与其他参与者的隔离，程序员绝不会在参与者级别面临并发情况，因此绝不会需要使用锁或其他同步机制来控制对共享数据的访问。非专家程序员只需此功能便可方便地控制分布式应用程序的开发。

* *透明激活。* 仅当要处理消息时，Azure 结构运行时才会根据需要激活参与者。这可将参与者的逻辑创建（对应用程序代码可见并受其控制）概念与参与者在内存中的物理激活（对于应用程序是透明的）完全分隔开来。在决定何时将参与者“移出页”（停用）或“移入页”（激活）方面，Azure 结构参与者类似于虚拟内存；应用程序可以不间断地访问以逻辑方式创建的参与者的完整“内存空间”（无论它们在任何特定时间点是否处于物理内存中）。透明激活可通过在整个硬件资源池中放置和迁移参与者来实现动态的自适应负载平衡。

* *位置透明性。* 程序员用于调用参与者的方法或传递给其他组件的参与者引用（代理对象）只包含参与者的逻辑标识。Azure 结构运行时会以透明方式完成参与者逻辑标识到其物理位置的转换以及对应的消息路由。应用程序代码与参与者进行通信时不在意其物理位置，该位置可能由于故障或资源管理原因，或是因为参与者在调用它时已停用而随时间发生变化。

* *与持久性存储的透明集成。* Azure 结构参与者允许以声明方式将参与者的内存中状态映射到持久性存储。它可同步更新，以透明方式保证调用方仅在持久状态成功更新之后才收到结果。

* *高可用性、故障转移支持和应用程序生命周期管理。* Azure 结构参与者的状态由平台进行管理，并采用可以还原（例如在群集中的节点发生故障时）的方式进行复制。Azure Service Fabric 还管理应用程序生命周期，并允许进行零停机时间应用程序升级。Azure 结构参与者编程模型旨在指导程序员遵循在通过几个数量级缩放其应用程序或服务时可能成功的途径。这通过采用经验证的最佳做法和模式，并提供较低级别系统功能的高效实现来完成。以下是实现 Azure 结构应用程序的可伸缩性和性能的一些关键因素。

* *应用程序状态的隐式细粒度分区。* 通过使用参与者作为可直接寻址的实体，程序员可隐式划分其应用程序的整体状态。虽然 Azure 结构参与者编程模型并未规定参与者应具有的大小，不过在大多数情况下，最好具有相对较大数量的参与者（数以百万计或更多），其中每个参与者都表示应用程序的一个自然实体，如用户帐户、采购订单等等。在参与者可单独寻址并且其物理位置由运行时抽象出来的情况下，Azure 结构参与者可非常灵活地采用透明的通用方式进行负载平衡和处理热点，无需应用程序开发人员进行考虑。

* *自适应资源管理。* 由于参与者不对它们进行交互的其他参与者的地点进行任何假设，并且由于位置透明性，因此 Azure 结构运行时可对参与者在整个计算群集中的放置/迁移进行细粒度决策以响应加载和通信模式而不会使传入请求失败，从而可以采用非常动态的方式来管理并调整可用硬件资源的分配。通过创建特定参与者的多个副本，运行时可以在需要时提高参与者的吞吐量，而无需对应用程序代码进行任何更改。

* *多路复用通信。* Azure 结构中的参与者具有逻辑终结点，它们之间的消息传送跨一组固定的全交换物理连接（TCP 套接字）进行多路复用。这使 Azure 结构参与者运行时可以托管非常大量（数百万）的可寻址实体（每个参与者的操作系统开销为零）。此外，参与者的激活/停用不会产生注册/注销物理终结点（如 TCP 端口或 HTTP URL）的成本。

* *高效计划。* Azure 结构运行时在自定义线程池中计划大量单线程参与者的执行（每个物理处理器核心一个线程）。借助采用非阻塞基于延续的样式（Azure 结构参与者编程模型的一个要求）编写的参与者代码，应用程序代码会以非常高效的“协作”多线程方式来运行，没有任何争用。这允许系统达到较高吞吐量，并以很高稳定性采用非常高的 CPU 使用率（高达 90% 以上）运行。系统和负载中的参与者数量增长不会形成其他线程或其他操作系统基元这一事实可帮助实现各个节点和整个系统的可伸缩性。

* *显式异步。* Azure 结构参与者编程模型明确了分布式应用程序的异步性质，可指导程序员编写非阻塞异步代码。这可实现高度的分布式并行性和整体吞吐量，而无需显式使用多线程。

<!---HONumber=74-->