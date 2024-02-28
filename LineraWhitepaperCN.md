## Linera：高度可扩展 Web3 应用程序的区块链基础设施
#### 版本2 - 2023年8月16日
### 摘要

我们推出 Linera，这是一个旨在为最苛刻的 Web3 应用程序提供互联网级别的可预测性能、安全性和响应速度的区块链基础设施。为此，Linera 通过引入一种基于弹性验证器的新型综合多链范式解决了区块空间稀缺问题。Linera 将用户置于协议的核心位置，允许他们管理自己的链（称为微链）中的区块生成，以实现最佳性能。为了帮助 Web3 开发人员充分利用 Linera 基础设施，我们开发了一个丰富的、与语言无关的多链编程模型。Linera 应用程序使用异步消息跨链通信。在同一个微链中，应用程序使用同步调用和临时会话（又名资源）进行组合。Linera 的初始 SDK 将针对 Rust 程序员，这要归功于 Wasm 虚拟机。Linera 基础设施基于委托权益证明（delegated Proof of Stake），并使用最先进的经济激励措施和社区的大规模审计来确保可靠的去中心化。[[1](<> '法律免责声明：此文档及其内容不构成出售或要约购买任何代币的要约或招揽。我们发布此白皮书只是为了收到公众的反馈和评论。本文件中的任何内容都不应被理解或解释为对 Linera 基础设施或其代币（如果有）将如何发展、被利用或增值做出保证或承诺。Linera 仅概述其当前计划，这些计划可能会自行决定而发生更改，其成功将取决于许多超出其控制范围的因素。此类前瞻性陈述必然涉及已知和未知的风险，这些风险可能导致未来期间的实际绩效和结果与我们在本文档中描述或暗示的内容存在重大差异。Linera 没有义务更新其计划。无法保证文件中任何陈述的准确性，因为实际结果和未来事件可能与我们的描述或暗示存在重大差异。请勿过度依赖前瞻性陈述。')].

## 目录
1 [**概述**](<>)<br>
&ensp;1.1 [Web3对可预测性能和响应能力的需求](<>)<br>
&ensp;1.2 [区块空间稀缺问题](<>)<br>
&ensp;1.3 [现有方法的不足之处](<>)<br>
&ensp;1.4 [Linera的使命](<>)<br>
&ensp;1.5 [Linera项目概述](<>)<br>
&ensp;&ensp;1.5.1 [基于弹性验证其的集成多链系统](<>)<br>
&ensp;&ensp;1.5.2 [使多链编程成为主流](<>)<br>
&ensp;&ensp;1.5.3 [弹性验证器的可靠去中心化](<>)<br>
2 [**Linera多链协议**](<>)<br>
&ensp;2.1 [参与者：用户、验证者、链所有者](<>)<br>
&ensp;2.2 [安全模型](<>)<br>
&ensp;2.3 [符号标记](<>)<br>
&ensp;2.4 [微链](<>)<br>
&ensp;2.5 [跨链请求](<>)<br>
&ensp;2.6 [链状态](<>)<br>
&ensp;2.7 [区块执行](<>)<br>
&ensp;2.8 [客户端/验证器交互](<>)<br>
&ensp;2.9 [核心协议的扩展](<>)<br>
3 [**多链协议分析**](<>)<br>
&ensp;3.1 [响应能力](<>)<br>
&ensp;3.2 [可扩展性](<>)<br>
&ensp;3.3 [安全性](<>)<br>
4 [**使用Linera构建Web3应用程序**](<>)<br>
&ensp;4.1 [创建应用](<>)<br>
&ensp;4.2 [多链部署](<>)<br>
&ensp;4.3 [跨链通信](<>)<br>
&ensp;4.4 [局部可组合性](<>)<br>
&ensp;4.5 [用户认证](<>)<br>
&ensp;4.6 [临时链](<>)<br>
5 [**去中心化**](<>)<br>
&ensp;5.1 [委托权益证明](<>)<br>
&ensp;5.2 [审计](<>)<br>
6 [**结论**](<>)<br>
A [**跨链通信**](<>)<br>
&ensp;A.1 [消息和收件箱](<>)<br>
&ensp;A.2 [跨链请求和发件箱](<>)<br>

## 1 概述
### 1.1 Web3对可预测性能和响应能力的需求

得益于区块链技术的发展，下一代互联网Web3将赋予用户新一代资产感知应用，并使他们在数字经济中拥有更多的民主控制权。然而，开发具有良好用户体验的Web3应用目前是一项具有挑战性的任务。其中一个问题是规模应用的可靠性和响应性：当太多用户活跃时，区块链可能停止响应或需要支付昂贵的交易费。总体而言，应用开发者希望他们的基础设施编程接口易于使用且可预测，而不受其他应用程序引起的流量干扰。业内已经存在集中式API提供者来简化在流行的区块链上进行编程的过程，但这样的提供者需要被使用者信任，并且也不能改善底层区块链的性能和费用。Linera的目标是通过提供一种区块链基础设施，以保证规模应用的性能和响应能力，来弥合集中式和去中心化应用之间的差距。

### 1.2 区块空间稀缺问题

在现有区块链中，交易手续费和延迟的最坏情况是不可预测的， 其主要原因可以解释为区块空间稀缺问题。换句话说，在只包含一条链的区块链中，为使其交易被选择进入下一个区块，发起交易的用户之间需要竞争。然而，与此同时，区块的生产速率和大小受到共识协议、网络和执行层性能的限制。因此，在交易高峰期（例如NFT空投）期间，用户的交易可能因为其他用户的交易手续费出价远远超过其定价范围而失败，或延迟很长时间才能被确认—在此期间，基础设施对他们实际上是不可用的。

### 1.3 现有方法的不足之处

毫无疑问，多年来已经提出了许多以提高可扩展性为目标的区块链基础架构。在这里，我们提供对常见方法的高阶总结，而不试图详尽无遗。

**更快的单链**。单链中的区块生产速度通常受验证者之间数据传播延迟的限制。早前，为了在满足安全需求和网络约束的条件下达成更高TPS，区块大小是第一个被调整的参数。得益于拜占庭容错（BFT）共识协议的最新进展，今天TPS的新瓶颈看起来是交易的顺序执行而不是共识排序（从消息池中选择合适的交易进入区块以待确认）。

我们可以预见的是，一个区块中包含的许多交易应该是相互独立的（译者注：这并不意味着一个区块中包含的所有交易都是相互独立的，仅表明大部分交易相互独立。例如，同一账号在一个区块内发送两次转账交易，则这两次转账交易并不独立，单该账号所有交易与其他账号的交易在本区块内是独立的）。基于这样的预期，文献19中的一些最近出现的项目已经开发出将一个区块中的交易分成不同的子集并行执行的架构<a href='#References19'>[19]</a>。毫无疑问，上述并行执行架构能够大大提升TPS，但依赖这样的架构依然难以达成超过6位数的TPS。此外，并行执行结构的TPS很大程度上取决于每个区块中相互独立的交易比例<a href='#References26'>[26]</a>，然而发送交易的用户不能事先知道该交易将被哪一个区块打包，亦不知该区块中其他用户发送的交易是否相互独立，由此导致用户无法确认该交易的执行费用和执行延迟。

最后，在高TPS的区块链中，由于执行交易需要CPU（计算资源）具有较高的计算速度，交易数据同步需要较大的网络带宽，使得对验证者的审计变得更加困难。具体而言，通用由于硬件的计算速度和下载能力不足，不能快速重放交易，大部分只拥有通用硬件的社区成员因此不能便捷地参与验证者的审计。

**区块链分片**。解决区块链可扩展性的另一个流行方向是分片。这一项技术将执行状态拆分为固定数量的平行链，每一条平行链由独立的验证者集合运行。

区块链分片技术到今天仍在不断改进，这项技术的演进也曾因为一些困难的挑战颇显波折。首先，不同平行链使用不同的验证者集合的设计为区块链系统引入了安全妥协，攻击者无需攻击整个区块链系统，而只需要选择性攻击系统中最弱的环节（比如铸币）。其次，重组分片（即重新构造用户账户在不同平行链上的交易分布）是一项复杂的操作，其过程中涉及巨量的网络通信<a href='#References33'>[33]</a>。最后，当需要增加分片数量以达成更高的TPS，平行链之间的跨链消息数量也会随之增加<a href='#References26'>[26]</a>。由于平行链运行在独立的验证者集合，这将会导致跨链消息延迟的显著增加，由此抵消了增加新的分片带来的益处[<a href='#References31'>31</a>, <a href='#References33'>33</a>]。

**Rollups**。最后，解决区块空间稀缺性问题的另一种流行方法是Rollup协议，通常基于Optimistic或有效性证明（也称为ZK rollup）实现<a href='#References11'>[11]</a>。从顶层设计来看，Optimistic和ZK Rollups都是Layer 2协议，他们在链下构建一系列大区块，这些大区块将在Layer 1执行、压缩和确认。不幸的是，在Optimistic和ZK Rollups两种场景下，Layer 1确认交易都需要大量的时间。Optimistic协议中解决争议（达成共识）需要几天的时间。ZK Rollups协议将许多Layer 2交易压缩，然后一次性提交到Layer 1确认，作为一条Layer 1交易支付gas。实践上，为每个Layer 2区块收集足够的Layer 2交易，计算有效性证明以及归档交易以强制执行严格的数据可用性（译者著：此处的翻译可能有些疑问）需要耗费几个小时。

由于Layer 1过长的确认时间，某些对于可响应性要求较高且能接受分片带来的安全妥协的应用选择信任Layer 2的执行结果，这些应用必须信任Rollups协议能够持续运行（以保持活跃）以及公平地选择交易（参见Miner Extractable Value<a href='#References15'>[15]</a>），最新的去中心化Rollup协议设计亦充分体现了这样的担忧。

### 1.4 Our mission  我们的使命

受到这些洞见的启发，我们创建了Linera项目。该项目基于如下三个关键原则，开发一种新的Web3基础设施：

- (**i**) 构建一个具有可预测性能和响应性的安全基础设施。为达成此目标，多条链将运行在同一组可扩展的弹性验证器上；

- (**ii**) 针对可扩展的Web3应用构建丰富的生态。为达成此目标，Linera引入新的执行层，多链编程在该执行层上是主流编程方法；

- (**iii**) 最大程度去中心化。为达成此目标，弹性验证者将得到最佳激励，并由社区成员进行规模化审计。

### 1.5 Overview of the project  项目概况

对于区块链社区而言，Linera将致力于引入下述创新点。

#### 1.5.1 一种基于弹性验证器的集成式多链系统

我们的愿景是实现一个大规模Web3基础设施，基于该基础设施开发的应用具有可预测的性能和响应性。为达成这样的目标，我们开发了一种新的多链协议，该协议的设计目的在于将现代云基础设施应用到Web3领域：

- (**1**) 在Linera中，验证器与Web2中的弹性服务相似，验证器并行验证和执行多条链的区块中的交易。在Linera系统中，链(包括活跃的和非活跃的)的数量是无限的，我们也将这样的链称为微链。

- (**2**) 向微链添加新区块的任务与交易的验证和执行是分离的，通常而言，只有链的所有者(们)(译者注：此处原文为owner(s)，表示微链可以有一个或多个owner)。每个Linera用户(译者注：此处user不仅指发起交易的人，也指与区块链交互的客户端)都可以创建自己的微链，用于管理他们自己的账户。

- (**3**) 每一个验证器都管理所有微链(我们称为集成式多链方法)。微链之间通过异步消息交互，或独立运行。这样的设计使得验证器可以将负载拆分到多个集群内部成员(即分片)，以实现弹性伸缩。微链之间凭借验证器的内部网络执行异步消息通信，以确保效率。

- (**4**) 微链有不同的方式接受新区块。在添加新区块时，用户通过低延迟、无内存池的可靠广播协议[<a href='#References7'>7</a>, <a href='#References12'>12</a>]将新区块提交给验证器。某些应用程序需要更加复杂的交互，可能灰根据需要创建临时链。实践上，只有Linera基础设施拥有的公共微链需要完整的BFT共识协议<a href='#References12'>[12]</a>。

- (**5**) 原则上，验证器之间不会相互交互——除了Linera基础设施拥有的公共微链。验证器之间的微链同步通过微链的所有者实现。这意味着对于验证者来说，不活跃的微型链（不创建区块的链）除了存储外没有额外成本。

Linera的弹性验证者是其独特的假设。我们希望Linera社区支持各种新验证者可以选择的云服务提供商。Linera最初受到Meta开发的学术低延迟支付协议FastPay的启发。Linera通过将用户帐户转换为微型链、添加智能合约并支持链之间任意异步消息，从而使FastPay得以推广。Linera多链协议的更多详细描述请参见第2节。我们将在第3节分析该协议。

弹性验证器是Linera的独特假设。我们致力于支持更多可以作为验证器的云服务提供商，供Linera社区选择。Linera最初受到Meta开发的低延迟支付协议FastPay启发。在此基础上，Linera将用户账户转换为微链、添加智能合约并支持微链之间任意异步消息通信，大大丰富了FastPay协议。Linera多链协议的更多详细描述请参见第<a href='#Section2'>2</a>节。我们将在第<a href='#Section3'>3</a>节分析该协议。

#### 1.5.2 使多链编程成为主流

Linera使用同一个验证者集合管理全部微链。大规模的跨链通信通过单个验证者的内部网络实现，Web3应用从此能够利用廉价高效的基础设施弹性扩容。为了推动多链编程的采用，我们做出了以下设计选择：

- (**6**) Linera的执行模型是编程语言无关的，且对开发者友好。初始版本的Linera SDK将基于Wasm，面向Rust编程语言。

- (**7**) Linera应用程序是组合式的多链应用。应用程序被创建吼，可以按需在任何微链上运行。同一应用程序在不同的微链上有不同的运行实例，这些运行实例通过异步消息和发布/订阅频道通信。同一微链上的不同应用程序通过跨合约调用和临时会话对象进行交互。

Linera中的会话对象受到Move语言中资源的启发<a href='#References9'>[9]</a>。Move语言中的静态类型资源被提议用于帮助实现组合性。在Linera中，会话处理和运行时检查实现了类似资源的组合性。例如，要发送代币，Linera合约能够转移包含该代币的临时会话的所有权。

总的来说，构建大型的开发者社区是区块链基础设施被采用的一个重要因素。鉴于Wasm生态对于多语言工具的持续改进<a href='#References4'>[4]</a>，我们认为Wasm能够支撑Linera长期服务于不同的开发者社区。我们将在第<a href='#Section4'>4</a>节讨论更详细的Linera编程模型。

#### 1.5.3 弹性验证者的可靠去中心化

经典的“区块链不可能三角”<a href='#References10'>[10]</a>阐述了同时实现扩展性、安全性和去中心化的困难。这样的的观点在验证器能力恒定的前提下确实成立，然而我们认为，对于如何定义和实施能够满足去中心化要求的弹性验证器这一方向，我们还没有做出足够的努力。

- (**8**) Linera依赖委托权益证明(DPoS)保障安全性，并支持定期更换验证者集合。得益于区块的链接，任何微链的历史交易、跨链消息和执行状态都是防篡改的。

- (**9**) 微链可以被独立审计。这意味着作为整体的Linera可以由社区进行分布式审计，社区成员仅需要通用硬件便可参与。

文献<a href='#References10'>[10]</a>在Roolups场景下讨论了使用高性能验证器，同时由社区驱动的审计保持去中心化。随着Linera项目进展，我们将继续关注有效性（“ZK”）证明和链压缩领域的技术进步。我们将在第<a href='#Section5'>5</a>节进一步讨论Linera的去中心化。

## 2 Linera多链协议

<a name='Section2'>我们</a>将在这一章介绍Linera基础设施的核心：多链协议。本章的主旨在于阐明该协议的主要思想，因此将不会对协议做详尽分析说明。我们将在第<a href='#Section3'>3</a>章中非正式地分析该协议，并在第<a href='#Section4'>4</a>章讨论编程模型。

### 2.1 参与者：用户、验证者、链所有者

Linera协议的目标，在于提供一个计算基础设施。应用开发者可以在该基础设施上创建dApps，终端用户可以安全高效地访问dApps。

和其他区块链系统一样，Linera应用程序的状态被复制到多个部分可信节点，这些节点称为验证者。应用程序通过将交易插入到新区块，并将新区块提交给验证者更新应用程序状态。

为了支持扩展性，Linera不再使用单链，取而代之的是集成式的多链系统。该系统拥有多条并行执行的链（称之为微链），交易被组织到这些并行执行的微链区块中。这意味着Linera应用程序的状态通常分布在不同的微链上。特别需要指出的是，除非正在进行重新配置(<a href='#Section2.9'>2.9</a>)，Linera中的所有微链都使用同一组验证器。

在Linera中，向区块链添加新区块与验证区块的任务是分离的，新区块由链的所有者创建。事实上，链的所有者可以是协议的任何参与者。由于Linera验证器负责验证区块，链的所有者可以只需要实现客户端功能即可。下面是一些链所有者的示例：

- 希望在不同应用程序中对自己的用户拥有更多控制权的终端用户；

- 希望运行临时链(例如用于原子交换)的终端用户；

- 希望将代码部署到链上以创建应用，或管理应用程序的开发者；

- 运行公开链的验证者(例如用于基础设施)。

最后，我们将阐述Linera如何管理当前验证者集(也称为委员会)。第<a href='#Section4'>4</a>章中我们将讨论Linera的编程模型，此外，审计员角色将在第<a href='#Section5'>5</a>章讨论。

### 2.2 Security model

### 2.2 安全模型

<a name='Section2.2'>Linera</a>的共识遵循拜占庭容错机制(BFT)<a href='#References13'>[13]</a>，所有参与者创建自己的公私钥密钥对。Linera使用委托权益证明（DPoS）模型，每个验证者的投票权与其直接质押份额和用为委托给该验证者的质押份额相关。

**假设**。我们以总投票权为*N*的例子来说明Linera协议。一组未知的*Byzantine* (即*dishonest*)验证者子集可能偏离协议，与许多BFT协议[<a href='#References7'>7</a>, <a href='#References13'>13</a>]相似，假设该验证者子集最多控制*f*的投票权，其中*f*的取值范围为*0* ≤ *f* < $\frac{ N }{ 3 }$。实作上，人们通常选择最大值作为*f*，即*f* = $\lfloor \frac{ N - 1 }{ 3 } \rfloor$。

安全层面，我们不对用户、链所有者或网络层实现做任何假设。除非特别说明，否则可用性(译者注：原文这里为liveness，意指服务能响应请求，但翻译为活性、活跃性都不合适，因此我们意译为可用性)不依赖于网络延迟或消息顺序，换句话说，网络是*异步的*<a href='#References13'>[13]</a>。

我们用术语*quorum*(译者注：quorum英文释义为法定人数，但是在区块链场景使用这样的释义略显莫名其妙，因此此处我们保留原单词)来表示由一组验证者签发的签名集合，参与签名的验证者至少拥有*N - f*的投票权。对于任意两个*quorum*，如果同一个诚实的验证者*α*同时存在于两个*quorum*中，称为quorum*交集*，这是*quorum*的重要特性。一个*quorum*中的验证者对数据(通常为一个区块)进行签名，称之为*认证*，被认证过的数据简称为*证书*。

**目标**。Linera旨在确保下述安全特性：

- *安全性*：对于任意微链，不同的验证者看到的都是相同的区块(的前缀)按照同样顺路组成的链条，验证者按照同样的顺序修改微链的执行状态，并最终将同样的消息集合提交到其他微链。

- *微链的最终一致性*：当一个诚实的验证者认证了一个新的区块，并将其添加到微链上，任何用户都可以通过一系列步骤确保该区块被所有诚实的验证者添加到他们的微链上。

- *异步消息的最终一致性*：如果某个诚实的验证者上的微链收到了跨链消息，任何用户都可以通过一系列步骤确保该消息被所有诚实的验证者收到。

- *可靠性*：只有微链的所有者(们)能向微链添加区块。

- *分段可审计性*：Linera的状态有足够的公开加密证据，可以以微链为单位，分布式审计其正确性。

对于只有一个所有者的微链(第<a href='#Section2.4'>2.4</a>节)，Linera还确保以下特性：

- *单一区块验证*：在只有一个所有者的微链中，当所有者在给定高度签名并提交了该高度的第一个区块，只要有一个诚实的验证者接受该区块，那么通过适当的操作，所有者最终一定能够成功收集到足够的投票，为该区块生成一个证书。

- *最坏情况下的效率*：在只有一个所有者的微链中，拜占庭验证者不能给区块生成和正确用户的区块确认造成显著延迟。

========================================================================================================

### 2.3 Notations   符号

We assume a collision-resistant hash function, noted **hash**(·), as well as a secure public-key signature scheme **sign**[.]. A quorum of signatures on a block *B* forms a certificate noted *C* = **cert**[*B*]. In the rest of this report, we identify certificates on the same block *B* and simply write *C* = **cert**[*B*] when *C* is any certificate on *B*.

我们假设存在一个抗碰撞哈希函数，记为 hash(·)，以及一个安全的公钥签名方案 sign[.]。对于区块 B 上的一组签名，形成一个被标记为 C = cert[B] 的证书。在本报告的其余部分，我们将会识别同一区块 B 上的证书，并简单地写作 C = cert[B]，其中 C 是该区块上的任意证书。

The state of the Linera system is replicated across all validators. For a given validator, noted *α*, we use the notation *X*(*α*) to denote the current view of *α* regarding some replicated data *X*. A *data type D* = $\left \langle Tag, arg_1, . . . , arg_n \right \rangle$ is a sequence of values starting with a distinct marker **Tag** and meant to be sent over the network. We use capitalized names to distinguish data type markers from mathematical functions (e.g. **hash**) or data fields (e.g. **owner**$^{id}$(*α*)), and simply write **Tag**$(arg_1, . . . , arg_n)$ for a data type. We write $\widetilde{D}$ for a sequence of data types $(D_1, . . . D_n)$.

Linera 系统的状态被复制到所有验证者中。对于给定的验证者，记为 α，我们使用符号 X(α) 来表示关于某个复制数据 X 的验证者 α 的当前视图。数据类型 D = $\left \langle Tag, arg_1, . . . , arg_n \right \rangle$ 是一系列以独特标记 Tag 开头的值，并打算发送到网络上。我们使用大写名称来区分数据类型标记和数学函数（例如 hash）或数据字段（例如 owner(α)），并简单地写作**Tag**$(arg_1, . . . , arg_n)$ 表示一个数据类型。我们将 写作 $\widetilde{D}$ 表示一系列数据类型 $(D_1, . . . D_n)$。

### 2.4 Microchains   微块链

<a name='Section2.4'>The</a> main building blocks of the Linera infrastructure are its microchains. A microchain (or simply *chain* for short) is similar to a regular blockchain in the sense that it is made of a chain of blocks, each containing a sequence of transactions. Importantly, Linera separates the role of proposing new blocks (chain owners’ role) from validating them (validators’ role). The protocol to extend a chain is configurable and depends on the *type* of the chain.

Linera 基础架构的主要构建模块是其微块链。微块链（简称为链）类似于常规区块链，由一系列包含交易序列的区块组成。重要的是，Linera 将提出新区块（链所有者的角色）与验证新区块（验证者的角色）的职责分开。扩展链的协议是可配置的，并取决于链的类型。

**Chain identifiers.** A microchain is represented by an identifier *id* designed to be nonreplayable. Specifically, a *unique identifier* (or simply *identifier* ) is a non-empty sequence of numbers written as *id* = [ $n_1$, . . . , $n_k$] for some 1 ≤ *k* ≤ $k_{max}$. We use :: to denote the concatenation of one number at the end of a sequence: [ $n_1$, . . . , $n_{k+1}$] = [ $n_1$, . . . , $n_k$] :: $n_{k+1}$ (*k* < $k_{MAX}$). In this example, we say that *id* = [ $n_1$, . . . , $n_k$] is the *parent* of id :: $n_{k+1}$.

链标识符。微块链通过一个被设计为不可重放的标识符 id 来表示。具体来说，唯一标识符（或者简称标识符）是一串非空数字序列，写作id = [n1, . . . , nk]，其中 1 ≤ k ≤ n。我们使用 :: 表示将一个数字连接到序列的末尾：[n1, . . . , nk] = [n1, . . . , nk-1] :: nk（k < n）。在这个例子中，我们说 id = [n1, . . . , nk] 是 id :: nk 的父标识符。

A Linera system starts with a fixed set of microchains defined in the genesis configuration. To create a new chain, the owner of an existing chain must execute a chain-creation transaction. The new identifier is computed as the concatenation of the parent identifier and the index of the transaction creating the new chain.

Linera 系统从初始定义的基因配置中开始，其中包括了一组固定的微块链。要创建新的链，现有链的所有者必须执行一个创建链的交易。新标识符是通过将父标识符和创建新链的交易索引连接而得到的。

**Chain types.** Linera supports three types of microchains:

链类型。Linera 支持三种类型的微块链：

- (**i**) *Single-owner chains* where only one user (as identified by its public key) is authorized to propose blocks;
- （i）单所有者链，只有一个用户（由其公钥标识）被授权提出区块；
- (**ii**) *Permissioned chains* where only a well-defined set of cooperating users are authorized to propose blocks;
- （ii）允许链，只有一个明确定义的合作用户集被授权提出区块；
- (**iii**) *Public chains* where validators propose blocks.
- （iii）公共链，验证者提出区块。

In all three cases, the agreement between validators regarding the next block *B* of a chain is represented in fine by a certificate *C* = **cert**[*B*]. In the case of a single-owner chain, the production of the certificate *C* is inspired by reliable broadcast [7,12] and will be described in detail in Section <a href='#Section2.8'>2.8</a>. In the case of public chains, the certificate *C* is a proof of commit produced by a classical BFT consensus protocol between validators. The case of permissioned chains and public chains is sketched in Section <a href='#Section2.9'>2.9</a>. For simplicity, unless mentioned otherwise, the rest of this report focuses on single-owner chains.

在这三种情况下，验证者对于链的下一个区块 B 的达成一致由证书 C = cert[B] 来表示。在单所有者链的情况下，证书 C 的生成受可靠广播的启发，并将在第 2.8 节中详细描述。在公共链的情况下，证书 C 是由验证者之间的经典 BFT 共识协议生成的提交证明。权限链和公共链的情况将在第 2.9 节中概述。为简单起见，除非另有说明，本报告的其余部分将重点放在单所有者链上。

Every chain includes a field **owner**$^{id}$(*α*) to authenticate their *owner(s)*, if any. We write **owner**$^{id}$(*α*) = *pk* when the chain has a single owner authenticated by the public-key *pk*. Permissioned chains have **owner**$^{id}$(*α*) = { ${pk}_1$, . . . , ${pk}_n$} and public chains **owner**$^{id}$(*α*) = &#x2605;. When **owner**$^{id}$(*α*) = ⊥, the chain is said to be *inactive*.

每个链都包括一个字段 owner(α) 用于验证其所有者，如果有的话。当链由公钥 pk 验证的单一所有者时，我们写作 owner(α) = pk。权限链的 owner(α) = {n1, . . . , nk}，公共链的 owner(α) = ★。当 owner(α) = ⊥ 时，表示该链处于非活跃状态。

**Chain lifecycle.** Any existing chain can create a new microchain for another user and use the block certificate *C* as a proof of creation. Once created, the new microchain works independent from its parent microchain. Linera will make available a dedicated public chain to allow new users to easily create their first chain.

链的生命周期。任何现有的链都可以为另一个用户创建一个新的微块链，并使用区块证书 C 作为创建的证明。一旦创建，新的微块链将独立于其父级微块链运行。Linera 将提供一个专门的公共链，以便新用户可以轻松地创建他们的第一个链。

Linera also makes it possible to safely and verifiably transfer the control of a chain to another user by executing a transaction that changes the key **owner**$^{id}$(*α*). Setting **owner**$^{id}$(*α*) = ⊥ effectively deactivates the chain permanently.

Linera 还可以通过执行更改 owner(α) 的交易，将链的控制安全可靠地转移给另一个用户。将 owner(α) = ⊥ 设置为有效地永久停用该链。

Using unique identifiers is important so that the state of a deactivated microchain can be safely deleted and archived in cold storage while preventing the chain of blocks from being replayed.

使用唯一标识符是很重要的，这样在停用微块链的情况下，可以安全地删除并存档在冷库中，同时防止区块链被重放。

**Blocks.** A *block* is a data type *B* = **Block**(*id*, *n*, *h*, $\widetilde{T}$) made of the following data:

区块。一个区块是由以下数据组成的数据类型 B = Block(id, n, h, T):
- the unique identifier of the chain to extend *id*,
- 要扩展的链的唯一标识符 id,
- a block height *n* ≥ 0,
- 区块高度 n ≥ 0,
- the hash *h* of the previous block (or ⊥ if *n* = 0),
- 上一个区块的哈希 h（如果 n = 0，则为 ⊥）,
- a sequence of *transactions* $\widetilde{T}$.
- 一系列交易 T.

A transaction *T* is an instruction meant to be executed on a chain. Transactions are typically used to modify the *execution state* of the chain. In Linera, they may also have additional effects such as creating chains, sending messages to a *recipient* chain ${id}'$, or receiving messages.

交易 T 是指在链上执行的指令。交易通常用于修改链的执行状态。在 Linera 中，它们也可能具有额外的效果，比如创建链、向接收链发送消息，或者接收消息。

A microchain *id* with a current chain of blocks ⊥ → $B_0$ → . . . → $B_n$ is successfully extended by block *B* when validators receive a certified request *C* = **cert**[*B*] that contains id and the next expected block height *n* + 1. Validators track the current state of each chain *id* and only vote in favor of adding a block *B* after validating the correct chaining and the correct execution of *B*. Under BFT assumption, this ensures that validators eventually execute the same sequence of blocks on each chain, therefore agree on the execution state.

当验证者收到一个包含 id 和下一个预期区块高度 n + 1 的认证请求 C = cert[B] 时，微块链 id 的当前区块链 ⊥ → → . . . → 成功地被区块 B 扩展。验证者跟踪每个链 id 的当前状态，并只在验证正确的链接和正确执行 B 后投票赞成添加一个区块 B。根据 BFT 假设，这确保了验证者最终在每个链上执行相同的区块序列，因此对执行状态达成一致。

The execution of a block *B* consists in interpreting the transactions $\widetilde{T}$ listed in *B* in the given order. Transactions may produce outgoing messages for other chains and consume incoming messages. In practice, for auditing purposes, blocks *B* also include the hash of the state after executing the block, as well as the outgoing messages produced by transactions.

区块 B 的执行包括按照给定顺序解释 B 中列出的交易。交易可能会为其他链产生输出消息并消费输入消息。在实践中，出于审计目的，区块 B 还包括执行该区块后的状态哈希值，以及交易产生的输出消息。

### 2.5 Cross-chain requests   跨链请求

<a name='Section2.5'>The</a> state of a Linera application is usually distributed across many chains for scalability. To coordinate across chains, applications rely on asynchronous communication (see also Section <a href='#Section4.3'>4.3</a> on programmability).

Linera 应用程序的状态通常为了可扩展性分布在许多链上。为了跨链协调，应用程序依赖于异步通信（也请参阅第 4.3 节的可编程性）。

At the protocol level, asynchronous communication between chains relies on an important mechanism called *cross-chain requests*. Concretely, the execution of a transaction in a block on a chain *id* by a validator *α* may sometimes trigger a one-time, asynchronous interaction that will modify the state of another chain $id'$. (See Algorithm 1 for an example of pseudo-code with cross-chain requests.) Cross-chain requests are cheaply implemented using remote procedure calls (RPCs) in the internal network of each validator: the implementation needs only ensure that each request is executed exactly once.

在协议级别，链与链之间的异步通信依赖于一个重要的机制，称为跨链请求。具体来说，验证者 α 在链 id 上对区块中的交易进行执行时，有时可能会触发一次性的异步交互，这将修改另一个链的状态。（有关使用跨链请求的伪代码示例，请参见算法 1。）跨链请求通过在每个验证者的内部网络中使用远程过程调用（RPC）来便宜实现：实现只需要确保每个请求被执行一次。

Importantly, arbitrarily modifying the execution state of a target chain with a crosschain request is not possible in general because validators do not agree on the order of execution of cross-chain requests—in other words, this would break the *Safety* property. While FastPay <a href='#References7'>[7]</a> uses cross-chain requests for payments only, Linera uses this mechanism to create new chains and to deliver messages to the *inbox* of an existing chain.

重要的是，一般情况下不能通过跨链请求任意修改目标链的执行状态，因为验证者不会就跨链请求的执行顺序达成一致——换句话说，这将破坏安全性属性。虽然 FastPay [7] 只用于支付的跨链请求，但 Linera 使用这种机制来创建新链并向现有链的收件箱投递消息。

Inboxes allow Linera to support arbitrary messages because the modification is not applied to the target chain immediately. Rather, the message is placed in the target chain’s inbox, implemented as a commutative data structure (*i.e.* where the order of insertions does not matter) described in Section <a href='#Section2.6'>2.6</a>. The owner(s) of the receiving chain then executes a transaction that picks the message from the inbox and applies its effect to the chain state (Section <a href='#Section2.7'>2.7</a>).

收件箱允许 Linera 支持任意消息，因为修改不会立即应用到目标链上。相反，该消息被放置在目标链的收件箱中，这里实现为一个可交换的数据结构（即插入顺序无关），详见第 2.6 节。接收链的所有者随后执行一个从收件箱中提取消息并将其效果应用到链状态的交易（第 2.7 节）。

### 2.6 Chain states  链状态

<a name='Section2.6'>We</a> now describe the state of the Linera chains as seen by validators and clients. Every validator stores a map that contains the states of all the chains, indexed by their identifiers. Clients have a similar representation of the chains except that they act as a *full-node* (*i.e.* track the chain of blocks and execution state) only for a small subset of the chains relevant to them. Next, we focus on the state of a given validator, noted *α*.

我们现在描述 Linera 链的状态，这是验证者和客户端所看到的。每个验证者都存储一个包含所有链状态的映射表，通过它们的标识进行索引。客户端也有类似的链表示，只不过它们只对与自身相关的一小部分链 acting as a full-node（即跟踪链的区块和执行状态） 。接下来，我们将重点放在给定验证者α的状态上。

**Chain state.** The state of a chain *id* as seen by a validator *α* can be divided into (i) a *consistent part* which is a deterministic function of the chain of blocks ⊥ → $B_0$ → . . . → $B_n$ already executed by *α*; and (ii) a *localized* part on which validators may not agree. The consistent part of a chain state includes the following data:

链状态。验证者 α 所看到的链 id 的状态可以分为两部分：(i) 一个一致的部分，它是验证者α已经执行的链的区块 ⊥ → → . . . → 的确定性函数；(ii) 另一部分是本地化的部分，验证者可能在这部分上达不成一致。链状态的一致部分包括以下数据：

- A field **owner**$^{id}$(*α*) controlling the production of blocks in *id*, as seen before.
- 一个字段 owner(α) 控制着 id 链中区块的生成，如前所述。
- An integer value, written **next-height**$^{id}$(*α*), tracking the expected block height for the next block of *id*. (Here $n + 1$. Initially 0.)
- 一个整数值，记为 next-height(α)，用来追踪 id 链下一个区块的预期高度。（初始为0）
- The hash of the previous block **block-hash**$^{id}$(α) (initially ⊥.)
- 上一个区块的哈希值 block-hash(α)（初始为 ⊥）。
- The execution state, noted **state**$^{id}$(*α*).
- 执行状态，记为 state(α)。

The localized part of a chain state includes the following:

链状态的本地化部分包括以下内容：

- **pending**$^{id}$(*α*), an optional value indicating that a block on *id* is pending confirmation (the initial value being ⊥).
- pending(α)，一个可选值，指示 id 上的一个区块正在等待确认（初始值为 ⊥）。
- A list of certificates, written **received**$^{id}$(*α*), tracking all the certificates that have been confirmed by *α* and involving *id* as a recipient chain.
- 一份证书列表，记为 received(α)，跟踪所有已被验证者 α 确认的并且涉及 id 作为接收链的证书。
- A data-structure called an *inbox* and denoted by **inbox**$^{id}$(*α*) (see next paragraph).
- 一个名为收件箱的数据结构，记为 inbox(α)（见下一段）。

The field **pending**$^{id}$(*α*) is specific to single-owner chains and explained in Section <a href='#Section2.8'>2.8</a>. It is completed by additional data in the case of permissioned and public chains. The list of certificates **received**$^{id}$(*α*) is crucial for liveness (Section <a href='#Section3.3'>3.3</a>).

字段 pending(α) 是针对单所有者链的，并在第2.8节中进行了解释。在权限链和公共链的情况下，会补充额外的数据。而 received(α) 证书列表对于活跃性（第3.3节）至关重要。

**Inbox state.** An inbox $I = {inbox}^{id}(α)$ is a special data structure used to track the crosschain messages received by *id* and waiting to be consumed by a transaction. Specifically, messages are *added* to an inbox upon reception and *removed* from it after being executed by the receiving chain.

收件箱状态。收件箱是一种特殊的数据结构，用于跟踪 id 接收到的跨链消息，并等待被交易消耗。具体来说，消息在接收后被添加到收件箱中，在被接收链执行后从中移除。


An important property of an inbox is that adding or consuming distinct messages is commutative. In the simplest implementation, one can think of an inbox as two disjoint sets of messages $I = (I_+, I_−)$. We may define the addition of a message *m* to *I*, noted $I + m$, as $(I_+ ∪$ {m}, $I_−)$ if $m \notin I_−$ and $(I_+, I_−$\\{m}) otherwise. Similarly, the subtraction $I − m$ is $(I_+, I_− ∪$ {m}) if $m \notin I_+$ and $(I_+$\\{m}, $I_−)$ otherwise. In this setting, when ${inbox}^{id}(α) = (I_+, I_−)$, the set $I_+$ represents the messages m that have been received by *id* and are waiting to be executed in a next block; $I_−$ tracks the messages that have not been received by *id* yet (from the point of view of *α*) but were nonetheless executed by anticipation because of a certified block. In this simplified presentation, we are assuming that messages are never replayed identically, say, because they include a counter for each pair of sender and receiver $(id, id')$.

收件箱的一个重要性质是，添加或消耗不同消息是可交换的。在最简单的实现中，可以将收件箱看作两个不相交的消息集合。我们可以定义将消息 m 添加到 I（记为 ）的操作为 {m}，如果 m 不在 I 中，则为 {m}；类似地，将消息 m 从 I 中移除（记为 ）的操作为 {m}，如果 m 在 I 中，则为 {m}。在这种情况下，当为时，集合 表示已被 id 接收并等待在下一个区块中执行的消息 m；表示尚未被 id 接收（从验证者 α 的角度来看），但由于认证区块的预期执行而提前执行的消息。在这个简化的描述中，我们假设消息永远不会完全重放，比如因为它们包含了每对发送方和接收方的计数器。

The current implementation of Linera uses a more complex data structure enforcing an ordered delivery of messages for each pair of sender and receiver, and for each application. See Appendix A.1 for a detailed description. For simplicity, in what follows, we still use the notation ${inbox}^{id}_−$ to denote the equivalent of the set $I_−$ above, representing the executed messages waiting to be received by the chain *id* at a given moment.

Linera 当前的实现使用了一种更复杂的数据结构，对于每对发送方和接收方以及每个应用程序都强制执行消息的有序传递。详细描述请参见附录 A.1。为简单起见，在接下来的内容中，我们仍然使用符号 来表示上述集合的等价物，表示已执行的消息，正在等待在某一特定时刻被链 id 接收。

### 2.7 Block execution    区块执行

<a name='Section2.7'>We</a> now describe how to execute the sequence of transactions contained in a chain of blocks. The transactions *T* supported by a Linera deployment include the following commands:
我们现在描述如何执行包含在区块链中的交易序列。Linera 部署支持的交易 T 包括以下命令：

- $OpenChain(id', pk')$ to activate a new chain with a fresh identifier $id'$ and public key $pk'$—possibly on behalf of another user who owns $pk'$;
- $OpenChain(id', pk')$ 激活一个带有新标识符$id'$和公钥的新链  $pk'$ —— 可能代表另一个拥有者 $pk'$；
- $ChangeKey(pk')$ to transfer the ownership of a chain;
- $ChangeKey(pk')$转移链的所有权；
- $CloseChain$ to deactivate the chain *id*;
- $CloseChain$停用链 *id*；
- $Execute(o)$ to execute a *user operation o*;
- $Execute(o)$执行*user operation o*；
- $Receive(m)$ to pick a *cross-chain message m* from the chain inbox and execute it.
- $Receive(m)$从链收件箱中提取一条跨链消息 m 并执行它。

The first three types of transactions are examples of *system operations* that are predefined in the protocol. In constrast, user operations *o* are executed by user-defined applications (aka “smart contracts”). At a high level, operations are meant to be freely added by the producer of a block, whereas receiving a cross-chain message requires the message to be first sent by another transaction of another chain (2.5).

前三种类型的交易是预先在协议中定义的系统操作的示例。相反，用户操作 o 是由用户定义的应用程序（也称为“智能合约”）执行的。在高层次上，操作应该可以自由地被区块的生成者添加，而接收跨链消息则要求该消息首先通过另一个链的另一个交易发送出去（2.5 节）。

For simplicity, we have omitted transaction fees and additional logic required by multiowner chains and reconfigurations (Section <a href='#Section2.9'>2.9</a>). Formally, to execute user operations o, we assume a method $ExecuteOperation(id, o)$ that attempts to modify ${state}^{id}$ and may return either ⊥ or $(m, id')$ in case of success, the latter case being a request that a message *m* be sent to the chain $id'$. We also assume a method to modify ${state}^{id}$ by executing a crosschain message *m*, noted $ExecuteMessage(id, m)$. Importantly, receiving a message m may produce another message $m'$ in return.

为简单起见，我们省略了多所有者链和重新配置所需的交易费用和额外逻辑（2.9 节）。正式地说，为了执行用户操作 o，我们假设有一个方法 ，它试图修改 并可能返回 ⊥ 或 在成功的情况下，后者是请求将一条消息 m 发送到链 的情况。我们还假设有一个方法通过执行跨链消息 m 来修改 ，记为 。重要的是，接收消息 m 可能会产生另一条消息 作为回应。

This description translates to the pseudo-code in Algorithm 1. The execution of a block $B = Block(id, n, h, \widetilde{T})$ as suggested above corresponds to the function ExecuteBlock. The validation of blocks by the function BlockIsValid is similar to ExecuteBlock except that no change to the state is persisted, cross-chain queries are ignored, and messages cannot be executed by anticipation, that is, the validation fails if ${inbox}^{id}_-$ is not empty at the end of the call.

这个描述对应于算法1中的伪代码。如上所建议的区块 的执行对应于函数 ExecuteBlock。通过函数 BlockIsValid 对区块进行验证类似于 ExecuteBlock，只是不会将状态持久化，跨链查询会被忽略，并且消息不能通过预期执行，也就是说，如果 在调用结束时不为空，验证将失败。

### 2.8 Client/validator interactions    客户端/验证者交互

<a name='Section2.8'>We</a> can now describe the interactions between clients (aka chain owners) and validators in a Linera system. Clients to the Linera protocol run a local node, noted β, that tracks a small subset of chains relevant to them. These relevant chains typically include the ones owned by the client as well as direct dependencies, notably a special Admin chain in charge of tracking validators and their networking addresses (Section <a href='#Section2.9'>2.9</a>).

我们现在可以描述在 Linera 系统中客户端（又称链所有者）与验证者之间的交互。遵循 Linera 协议的客户端运行一个本地节点，记为 β，用来跟踪与其相关的一小部分链。这些相关的链通常包括客户端拥有的链以及直接依赖项，特别是负责跟踪验证者和它们的网络地址的特殊 Admin 链（2.9 节）。

Network interactions with validators are always initiated by a client. Clients may wish to either (i) extend one of their own chain(s) with a new block, or (ii) provide a *lagging* validator with the certificates that it is missing in a chain of interest to the client.

客户端始终会主动发起与验证者的网络交互。客户端可能希望要么（i）通过一个新区块扩展自己的链，要么（ii）向滞后的验证者提供其在客户端感兴趣的链中缺失的证书。

To support these two use cases, validators provide two service handlers described in Algorithm 2 and called HandleRequest and HandleCertificate. For simplicity, we omit the service handlers used by clients to query the state of a chain or to download a chain of blocks from a validator.

为了支持这两种用例，验证者提供了两个服务处理程序，在算法2中进行了描述，分别被称为 HandleRequest 和 HandleCertificate。为简单起见，我们省略了客户端用于查询链状态或从验证者下载区块链的服务处理程序。

We start with the interactions meant to update a lagging validator.

我们首先讨论更新滞后验证者的交互。

**Uploading missing certificates to a validator.** Any client may upload a new certificate $C = cert[B]$ with $B = Block(id, n, h, \widetilde{T})$ to a validator *α* using the HandleCertificate entry point, provided that the chain *id* is active and that *n* is the next expected block height from the point of view of *α (*i.e.* formally ${owner}^{id}(α) \not= ⊥$ and **next-height**$^{id}(α) = n$).

任何客户端都可以使用 HandleCertificate 入口点将一个新的证书 c 上传到验证者 α，前提是链 id 处于活跃状态，并且从验证者 α 的角度看，n 是下一个预期的区块高度（即形式上为 n = next-height(α)）。

If the validator *α* has not created the chain *id* yet or if it is lagging by more than one block, concretely the client should upload a sequence of multiple missing certificates ending with $C = cert[B]$. If necessary, the sequence may start with blocks of an *ancestor* chain $id'$ (that is, $id' = parent(parent(. . . id)))$. In this case, the sequence continues until the block of the parent chain that created the chain id is reached, then finishes with the chain of blocks ending with *C*.

如果验证者 α 尚未创建链 id，或者滞后超过一个区块，具体来说，客户端应该上传一系列多个缺失的证书，以 结尾。如有必要，这个序列可以以祖先链的区块开始（即 ）。在这种情况下，序列会持续直到达到创建了链 id 的父链的区块，然后以包含证书 C 结尾的区块链结束。

In practice, the need to upload such a sequence of certificates justifies that the local node *β* may track the chain *id* in the first place. The client can quickly find the exact block that created *id* by looking at the first block logged in the list ${received}^{id}(β)$.

实际上，需要上传这样一个证书序列的情况证明了本地节点 β 首先需要跟踪链 id。客户端可以通过查看列表 中记录的第一个区块来快速找到创建 id 的确切区块。

**Extending a single-owner chain.** In the common scenario where validators are sufficiently up-to-date, Linera clients may extend their chain with a new block *B* using a variant of reliable broadcast [<a href='#References7'>7</a>, <a href='#References12'>12</a>] illustrated in Figure 1 and going as follows.

扩展单所有者链。在验证者足够及时的常见情况下，Linera 客户端可以使用可靠广播的一种变体来添加一个新区块 B，这在图1中进行了说明，步骤如下。

- The client broadcasts the block *B* authenticated by its signature to each validator using the HandleRequest entry point $α(&#x2460;)$ and waits for a quorum of responses.
- 客户端使用其签名对区块 B 进行广播，并通过 HandleRequest 入口点 ① 将其发送给每个验证者，并等待获得一定数量的回应。
- A validator responds to a *valid* request $R = auth[B]$ of the expected height by sending back a signature on *B*, called a *vote*, as acknowledgment (&#x2462;). After receiving votes from a quorum of validators, a client forms a certificate $C = cert[B]$.
- 验证者对预期高度的有效请求 ② 做出响应，回复一个对 B 的签名，称为投票，作为确认（③）。
- When a certificate $C = cert[B]$ with the expected next block height is uploaded (&#x2463;), this triggers the one-time execution of the block *B* (&#x2464;).
- 在获得来自一定数量的验证者的投票后，客户端形成证书 ④。当上传带有预期下一个区块高度的证书 ④ 时，这将触发对区块 B 的一次性执行 ⑤。

A synchronization step is occasionally needed first (&#x24EA;) if some validator *α* is unable to vote right away for an otherwise-valid proposal $B = Block(id, n, h, \widetilde{T})$. This may happen for two reasons:

有时候首先需要进行同步步骤（⓪），如果某个验证者 α 无法立即对一个否则有效的提案 进行投票。这可能出现两种情况：

![image](https://github.com/kikakkz/linera-whitepaper/assets/13128505/46ef58c9-a9f0-40b7-ac8e-bfe704e36699)

- 1. either the chain *id* is not active yet or *α* is missing earlier blocks (*i.e.* formally ${owner}^{id}(α) = ⊥$ or **next-height**$^{id}(α) < n$);
  2. 要么链 id 尚未激活，要么 α 缺少较早的区块（即形式上为 next-block(α) < next-height(α)）。
- 2. *α* is missing cross-chain messages, that is: $I_− = {inbox}^{id}$ is not empty at the end of the staged execution of $\widetilde{T}$.
  3. α 缺少跨链消息，即在 对 staged 执行结束时不为空。

In the first case, the Linera client must upload missing certificates in the chain *id* (and possibly its ancestors) as described in the previous paragraph, until **next-height**$^{id}$(α) = n. In the second case, the client must upload missing certificates in the chains that have sent the messages $m ∈ I_−$ to *id*. When *B* has been correctly constructed (*i.e.* is not trying to receive messages that were never sent), the set $I_−$ is necessarily covered by the certificates listed in the union $\cup_α' {received}^{id}(α')$ where $α'$ ranges over any quorum of validators.

在第一种情况下，Linera 客户端必须根据前面的段落所述，在链 id（及可能其祖先）中上传缺失的证书，直到 next-height(α) = n。在第二种情况下，客户端必须上传发送消息 到 id 的链中缺失的证书。当 B 被正确构建时（即没有尝试接收从未发送的消息），集合 是由列在任何验证者的任意仲裁联盟的证书的并集 并且范围涵盖。

Importantly, uploading a missing block to a validator benefits all clients. To maximize liveness and decrease the latency of their future transactions, in practice, it is expected that users proactively update all the validators when it comes to their own chains, therefore minimizing the need for synchronization by other clients. However, the possibility of synchronization by everyone is important for liveness (Section <a href='#Section3.3'>3.3</a>). It also allows a certificate to act as a proof of finality for the certified block.

重要的是，向验证者上传缺失的区块对所有客户端都有好处。为了最大限度地提高活跃性并减少未来交易的延迟，在实践中，预计用户会在涉及自己链的情况下主动更新所有验证者，从而最大程度地减少其他客户端进行同步的需求。然而，每个人都有可能进行同步对于活跃性（3.3 节）非常重要。它也使证书能够作为被认证区块的最终性的证明。

In practice, a client should execute the optional synchronization step (&#x24EA;) and the voting step (&#x2460;) on a separate thread for each validator. To prevent denial-of-service attacks from malicious validators, a client may stop synchronizing validators as soon as enough votes are collected (&#x2461;).

在实践中，客户端应该为每个验证者在单独的线程上执行可选的同步步骤（⓪）和投票步骤（①）。为了防止恶意验证者发起拒绝服务攻击，一旦收集到足够的投票（②），客户端可以停止与验证者进行同步。

The steps &#x2460;&#x2461;&#x2462; used to decide a new block in a single-owner chain constitute a 1.5 round-trip protocol. Inspired by reliable broadcast, this protocol does not have a notion of “view change” <a href='#References12'>[12]</a> to support retries. In other words, a chain owner that has started submitting a (valid) block proposal *B* cannot interrupt the process to propose a different block once some validators have voted for *B*. Doing so would risk blocking their chain. For this reason, Linera also supports a variant with an extra round trip (Section <a href='#Section2.9'>2.9</a>).

决定单所有者链中的新区块的步骤 ①②③ 构成了一个1.5往返协议。受可靠广播启发，这个协议没有“视图更改” [12] 的概念来支持重试。换句话说，一旦某些验证者对 B 进行了投票，已经开始提交（有效的）区块提案 B 的链所有者就不能中断流程以提出不同的区块。这样做会有阻塞他们的链的风险。因此，Linera也支持一种额外往返的变体（2.9 节）。

### 2.9 Extensions to the core protocol    核心协议的扩展

<a name='Section2。9'>We</a> now sketch a number of important extensions to the core Linera multi-chain protocol.

我们现在概述一些对核心Linera多链协议的重要扩展。

**Permissioned chains.** The protocol presented in Section <a href='#Section2.8'>2.8</a> allows extending a singleowner microchain optimistically in 1.5 client-validator round trips. Linera also supports a more complex protocol with 2.5 round trips to address the following use cases:

许可链。在第2.8节中介绍的协议允许在1.5个客户端-验证者往返中乐观地扩展单所有者微链。Linera还支持一个更复杂的协议，需要2.5个往返来解决以下用例：

- A single chain owner wants to be able to safely interrupt ongoing block proposals while they are in progress.
- 一个单一链所有者希望能够安全地中断正在进行中的区块提案。
- Transactions in blocks depend on external oracles (*e.g.* Unix time) and include conditions that may become invalid after being valid.
- 区块中的交易依赖于外部预言机（例如Unix时间），并包括可能在有效后变得无效的条件。
- Multiple owners wish to operate the chain (assuming minimal off-chain coordination).
- 多个所有者希望经营该链（假设最小化链下协调）。
- A single chain owner wishes to delegate maintenance operations related to validator reconfigurations.
- 一个单一链所有者希望委托与验证者重配置相关的维护操作。

We omit the details of the 2.5 round-trip protocol for brevity. It can be seen as a simplified partially-synchronous BFT consensus protocol <a href='#References12'>[12]</a> with view changes (aka rounds) but without leader election or timeouts. In the absence of leader election, different owners may try to propose a different block at the same time (*i.e.* in the same block height and round) causing the current round to fail and another round to be needed. As a consequence, this mode of operation assumes that the owner(s) of a same chain maintain a sufficient level of (off-chain) cooperation so that ultimately only one of them proposes a block and succeeds.

出于简洁起见，我们省略了2.5个往返协议的细节。它可以被看作是一个简化的部分同步BFT共识协议 [12]，其中包括视图更改（也称为轮次），但不包括领导者选举或超时。在没有领导者选举的情况下，不同的所有者可能会同时尝试提议不同的区块（即在相同的区块高度和轮次），导致当前轮次失败并需要另一个轮次。因此，这种操作模式假定同一链的所有者或所有者之间保持足够水平的（链下）合作，以便最终只有其中一个提出并成功提交区块。

**Public chains.** Public chains are used in the remaining use cases: when a chain continuously produces new blocks with the help of validators. In this case, the transactions authorized in a block are likely to be only those receiving cross-chain messages from other chains. Examples of applications include:

公共链。公共链在其余的用例中被使用：当一条链借助验证者不断产生新区块时。在这种情况下，一个区块中被授权的交易可能只是那些接收来自其他链的跨链消息的交易。应用示例包括：

- Managing validators and stakes in one place (see reconfigurations below).
- 管理验证者和权益的一个地方（请参见下面的重新配置）。
- Running traditional blockchain algorithms (*e.g.* AMMs) that were not designed to take advantage of the multi-chain approach;
- 运行传统的区块链算法（例如 AMM），这些算法并不是为了利用多链方法而设计的；
- Facilitating the creation of microchains for new users.
- 促进为新用户创建微链。

Public chains in Linera will be based on a full BFT consensus protocol. This is the only case in the Linera infrastructure where Linera validators take an active role in block proposals. We plan to rely on user chains and cross-chain messages instead of a traditional mempool to gather user transactions into new blocks.

Linera中的公共链将基于完整的BFT共识协议。这是Linera基础设施中唯一一种情况，Linera验证者会在区块提案中发挥积极作用。我们计划依赖用户链和跨链消息，而不是传统的内存池，将用户交易收集到新的区块中。

**Pub/sub channels.** A common use case for cross-chain asynchronous messages is for an application instance on a chain id to create a channel and maintain a list of subscribers to it. Specifically, a channel operates as follows:

Pub/sub频道。跨链异步消息的一个常见用例是，链 id 上的应用程序实例创建一个通道并维护其订阅者列表。具体来说，通道的运作如下：

- Transactions executed on the chain *id* may push new messages to the channel;
- 在链 id 上执行的交易可能会向通道推送新消息；
- When this happens, the current subscribers receive a cross-chain message in their inbox;
- 当这种情况发生时，当前的订阅者会在其收件箱中收到一条跨链消息；
- The set of subscribers is managed on the chain *id* by receiving and executing messages $Subscribe(id')$ and $Unsubscribe(id')$ from subscribers $id'$.
- 订阅者的集合由在链 id 上接收和执行来自订阅者的消息和维护。


We have found pub/sub channels to be a useful abstraction when programming Linera applications (see also Section <a href='#Section4'>4</a>). The Linera protocol supports pub/sub channels natively in order to enable specific optimizations. For instance, newly accepted subscribers currently receive the last message of a channel without additional work from the owner of the channel.

我们发现 pub/sub 频道在编写 Linera 应用程序时是一个有用的抽象（也见第 4 节）。Linera 协议支持 pub/sub 频道的本地化，以便实现特定的优化。例如，新接受的订阅者当前可以在不需要频道所有者额外工作的情况下接收到频道的最后一条消息。

**Reconfigurations.** Being able to change the set of Linera validators (aka the *committee*) is crucial for the security of the system (see Section <a href='#Section5'>5</a>).

重新配置。能够更改 Linera 验证者集合（也称为委员会）对于系统的安全性至关重要（见第 5 节）。

To do so, Linera deploys a dedicated Admin public chain running the application for system management. This system application is in charge of keeping track of the successive sets of validators, aka *committees*, including their stakes and network addresses. The successive configurations produced by this application are identified by their *epoch* number.

为此，Linera 部署了一个专用的管理公共链来运行系统管理应用。这个系统应用负责跟踪连续的验证者集合，即委员会，包括它们的权益和网络地址。由该应用程序产生的连续配置通过它们的时代编号进行标识。

To safely disseminate the information that the set of validators is changing, the Admin publishes new configurations to a special channel that every Linera microchain is subscribed to when created. $^{2}$ A newly created microchain automatically receives the current validator set (*i.e.* the last message in the admin channel) and sets its current epoch number field.

为了安全地传播验证者集合正在更改的信息，管理者将新配置发布到一个特殊的频道，每个创建时都会订阅的 Linera 微链。一个新创建的微链会自动接收当前的验证者集合（即管理频道中的最后一条消息），并设置其当前的时代编号字段。

When a new committee is created, every microchain receives a message in its inbox. Importantly, microchain owners must include the incoming message in a new block to explicitly migrate their chain to the new set of validators. This must be done when both sets of validators are still operating, before the previous set stops.

当新的委员会被创建时，每个微链都会在其收件箱中收到一条消息。重要的是，微链所有者必须将传入的消息包含在一个新的区块中，以明确地将他们的链迁移到新的验证者集合上。这必须在两组验证者仍在运行，并且之前的验证者组停止之前完成。

Thanks to the scalable nature of Linera, migrating a large number of chains to a new configuration in a short period of time is doable in parallel provided that enough clients are active. To facilitate this process and allow chain owners to go offline for an extended period, we envision that many users will authorize a third party to create the migration blocks on their behalf. This will however require configuring the chain to use the 2.5 round-trip protocol mentioned above for the duration of the authorization.

由于Linera具有可伸缩性，可以并行在短时间内迁移大量的链到新的配置，只要足够多的客户端是活跃的。为了简化这个过程，并允许链所有者在长时间内离线，我们预计许多用户将授权第三方代表他们创建迁移块。然而，这将需要配置链在授权期间使用上述提及的 2.5 往返协议。

To prevent long-range attacks, the Admin chain will also regularly suggest old committees to be *deprecated*. After accepting such an update, microchains will ignore messages in blocks certified only by deprecated committees. The old messages will be accepted again only after they are included in a chain of blocks ending with a trusted configuration (hence *re-certified*).

为了防止远程攻击，管理公共链还将定期建议废弃旧的委员会。在接受此类更新后，微链将忽略仅由已废弃的委员会认证的区块中的消息。只有在这些消息被包含在一个以受信任配置结束的区块链中（因此重新认证）后，才会再次接受这些旧消息。

## 3 Analysis of the Multi-Chain Protocol   多链协议分析

<a name='Section3'>In</a> this section, we analyze the design goals set by the Linera blockchain, including responsiveness, scalability and security guarantees.

在这一部分，我们分析了Linera区块链设定的设计目标，包括响应性、可扩展性和安全性保证。

### 3.1 Responsiveness  响应性

A common problem when interacting with classical blockchains is the lack of performance guarantees. Transactions submitted to the mempool may be picked instantly, after a moment, or never, depending on the other user transactions posted around the same time. Canceling a pending transaction typically requires submitting another one with a higher gas fee. Furthermore, classical blockchains have a fixed and limited throughput: large enough bursts of submitted transactions (e.g. due to a popular airdrop) must eventually cause a backlog and/or a surge in transaction fees. Mempool systems also expose users to value drainage with Miner Extractable Value (MEV) techniques.

在与传统区块链进行交互时常见的问题是缺乏性能保证。提交到内存池的交易可能会立即被选中，或者在一段时间后被选中，也可能永远不会被选中，这取决于其他用户在同一时间提交的交易。取消待处理的交易通常需要提交另一个带有更高 gas 费用的交易。此外，传统区块链具有固定且有限的吞吐量：足够大的交易提交突发（如由于热门空投而引起的）最终必然会导致积压和/或交易费用激增。内存池系统还通过矿工可提取价值（MEV）技术让用户面临价值流失的风险。

Linera allows users to manage their own chain and work around these problems thanks to a lightweight block extension protocol inspired by client-based reliable broadcast (Section <a href='#Section2.8'>2.8</a>). This approach does not require a mempool, as users submit their transactions directly to the validators and fully control the processing time. The parallel communication with the validators means that the only processing delay is imposed by the network roundtrip time (RTT) between the client and the validators (usually a few hundred milliseconds). Finally, we anticipate that removing the mempool and diminishing latency should greatly reduce MEV opportunities.

Linera 允许用户管理自己的链，并通过受 client-based reliable broadcast (Section 2.8) 启发的轻量级区块扩展协议解决这些问题。这种方法不需要内存池，因为用户直接将其交易提交给验证者，并完全控制处理时间。与验证者的并行通信意味着唯一的处理延迟来自客户端和验证者之间的网络往返时间（RTT）（通常为几百毫秒）。最后，我们预计移除内存池并减少延迟应该会大大减少 MEV 机会。


### 3.2 Scalability    可扩展性

The microchain approach (Section <a href='#Section2.4'>2.4</a>) allows Linera validators to be efficiently sharded across multiple workers. Concretely, each worker in a validator is responsible for a particular subset of microchains. Clients communicate with the load balancer of each validator, which dispatches queries internally to the appropriate worker (Figure 2).

微链方法（第2.4节）允许 Linera 验证者在多个工作节点之间高效地进行分片。具体来说，验证者中的每个工作节点负责处理特定的微链子集。客户端与每个验证者的负载均衡器进行通信，负载均衡器将查询内部分配给适当的工作节点（图2）。

![image](https://github.com/kikakkz/linera-whitepaper/assets/13128505/a7654bf4-7c6b-4b90-8bcd-0a8e3b485c69)

This design allows Linera to scale horizontally as the load of the system increases: each validator only needs to add worker machines to cope with the traffic. Importantly, sharding is internal: the number of workers and the assignment of microchains to workers do not need to be consistent across validators.

这种设计使得 Linera 在系统负载增加时可以水平扩展：每个验证者只需要增加工作节点来应对流量。重要的是，分片是内部的：工作节点的数量和微链分配给工作节点的方式在不同的验证者之间并不需要保持一致。

Workers within a single validator belong to a single entity and thus trust one another. This makes the communication between workers—and therefore the cross-chain requests of Linera (Section <a href='#Section2.5'>2.5</a>)—quick and inexpensive.

单个验证者内的工作节点属于单一实体，因此彼此之间相互信任。这使得 Linera 的工作节点之间的通信——因此也就是 Linera 的跨链请求（第2.5节）——变得快速且廉价。

The sharding model of Linera is different from the approach called *blockchain sharding* [<a href='#References31'>31</a>, <a href='#References33'>33</a>]. In the latter, cross-chain messages are exchanged between groups of mutually distrusting nodes (i.e., the validators in charge of each shard) usually spread across the Internet. This incurs significant overhead. Linera uses point-to-point communication across co-located workers that trust each other and requires much fewer resources. At the same time, larger validators can be efficiently audited by clients who want to control their operations. We describe the audit operation in Section <a href='#Section5'>5</a>.

Linera 的分片模型与所谓的区块链分片方法[31, 33]不同。在后者中，跨链消息是在相互不信任的节点组（即每个分片的验证者）之间交换的，这些节点通常分布在互联网上。这会造成很大的开销。Linera 使用了相互信任的共同工作节点之间的点对点通信，并且需要的资源要少得多。同时，希望控制其操作的客户端可以对较大的验证者进行高效审计。我们将在第5节中介绍审计操作。

The elastic architecture of Linera allows validators to adapt to traffic fluctuations. When an increased number of transactions are submitted, it is easy to increase the number of cloud-based workers processing the transactions. The same workers can be quickly turned off when no longer needed to reduce costs.

Linera 的弹性架构允许验证者适应流量波动。当提交的交易数量增加时，很容易增加处理交易的基于云的工作节点的数量。不再需要时，这些工作节点可以迅速关闭以减少成本。

The *public chains* of Linera require a full BFT consensus protocol to order blocks submitted by multiple clients (Section <a href='#Section2.9'>2.9</a>). Yet, the consensus protocol is instantiated once per public microchain rather than once for the entire system. This has a number of benefits. First, users from different public microchains cannot degrade each other’s experience. Second, the transaction rate of a single microchain is not a limiting factor for the entire system. Ultimately, the throughput of Linera can be always increased by creating additional microchains and augmenting the size of validators.

Linera 的公共链需要使用完整的 BFT 共识协议来对多个客户端提交的区块进行排序（第2.9节）。然而，共识协议在每个公共微链中只被实例化一次，而不是针对整个系统。这有许多好处。首先，来自不同公共微链的用户不会降低彼此的体验。其次，单个微链的交易速率不会成为整个系统的限制因素。最终，通过创建额外的微链并增加验证者的规模，Linera 的吞吐量总是可以提高的。

### 3.3 Security  安全性

<a name='Section3.3'>In</a> this section, we provide an informal security analysis of the Linera multi-chain protocol. Following the description in Section <a href='#Section2'>2</a>, we focus on single-owner chains. The analysis will be extended to other types of accounts (Section 2.9) in future reports.

在本节中，我们对 Linera 多链协议进行了初步的安全性分析。根据第2节中的描述，我们侧重于单所有者链。未来的报告将会扩展分析到其他类型的账户（第2.9节）。

**Claim 1** (Safety). *For any microchain, every validator sees (a prefix of ) the same chain of blocks, therefore it applies the same sequence of modifications to the execution state of the chain and eventually delivers the same set of messages to the other chains*.

声明 1（安全性）。对于任何微链，每个验证者都看到（某段）相同的区块链，因此它们对链的执行状态应用相同的修改序列，并最终向其他链交付相同的消息集。

Indeed, per Algorithm 2, each honest validator votes for at most one valid block at a given height per microchain. By the quorum intersection property (Section <a href='#Section2.2'>2.2</a>), under BFT assumption, there can be only one block per height per chain certified by a quorum of validators. The set of outgoing messages from a chain (the cross-requests in Algorithm 1) is a deterministic function of the current chain of blocks.

确实，根据算法 2，每个诚实的验证者针对给定高度的每个微链最多投票支持一个有效的区块。基于 BFT 假设的择取交集属性（第2.2节），只能有一个经验证者择取认证的每个链高度的区块。从链中发出的消息集合（算法 1 中的跨链请求）是当前区块链的确定性函数。

Importantly, asynchronous cross-chain messages are delivered exactly once after they are scheduled. This allows applications to safely transfer assets.

重要的是，异步的跨链消息在调度后仅被传递一次。这使得应用可以安全地转移资产。

**Claim 2** (Eventual consistency of chains). *If a microchain is extended with a new certified block on an honest validator, any user can take a series of steps to ensure that this block is added to the chain on every honest validator*.

声明 2（区块链的最终一致性）。如果在一个诚实的验证者上对微链进行了新的认证区块扩展，任何用户都可以采取一系列步骤来确保这个区块被添加到每个诚实的验证者的链上。

Indeed, any user can retrieve the new certificate and its predecessors from the honest validator and deliver it to validators that still have not received it. The exact sequencing in which blocks can be uploaded to a validator is discussed in Section <a href='#Section2.8'>2.8</a>.

事实上，任何用户都可以从诚实的验证者那里检索新的认证以及它的前序，并将其交付给那些尚未收到的验证者。有关区块上传到验证者的确切顺序在第2.8节中有讨论。

**Claim 3** (Eventual consistency of asynchronous messages). *If a microchain receives a crosschain message on an honest validator, any user can take a series of steps to ensure that this message is received by the chain on every honest validator*.

声明 3（异步消息的最终一致性）。如果在一个诚实的验证者上的微链接收到了跨链消息，任何用户都可以采取一系列步骤来确保这个消息被每个诚实的验证者的链接收到。

An asynchronous message is received by a chain on a particular validator only after a block containing a transaction that triggers the message is signed by a quorum and added to the sender’s chain. When this happens, the state of the receiving chain is updated to track the origin of the message (see **received**$^{id}(α)$ in Section <a href='#Section2.6'>2.6</a>). This allows a client to download the corresponding block from the same validator if needed. Any honest validator adding the same block for the first time will add the same message to the recipient’s inbox.

异步消息只有在包含触发消息的交易的区块被择取并添加到发送者的区块链后，才会被特定验证者的链接收。当发生这种情况时，接收链的状态将会被更新以追踪消息的来源（请参见第2.6节中的received）。这允许客户端在需要时从同一验证者下载相应的区块。任何第一次添加相同区块的诚实验证者将把相同的消息添加到接收者的收件箱中。

**Claim 4** (Authenticity). *Only the owner(s) of a microchain can extend their microchain*.

声明 4（真实性）。只有微链的所有者才能扩展他们的微链。

Honest validators only accept block proposals if they are authenticated by an owner (Algorithm 2). This ensures that no one else can add blocks to the microchain. Other types of microchains (Section <a href='#Section2.9'>2.9</a>) implement similar verifications.

诚实的验证者仅在区块经过所有者认证时才接受区块提案（算法2）。这确保了没有其他人可以向微链添加区块。其他类型的微链（第2.9节）也实施了类似的验证。

**Claim 5** (Piecewise Auditability). *There is sufficient public cryptographic evidence for the state of Linera to be audited for correctness in a distributed way, one chain at a time*.

声明 5（分段可审计性）。对于 Linera 的状态，存在足够的公共加密证据，可以按照分布式方式逐个链进行正确性审计。

Any Linera client can request a copy of any microchain and re-execute the certified blocks. This allows verifying the successive execution states and the set of outgoing messages from the chain. Execution states are typically compared across validators by including execution hashes in blocks. The received messages of a chain should be compared to the outgoing messages from the other chains (Section <a href='#Section5.2'>5.2</a>).

任何 Linera 客户端都可以请求任何微链的副本并重新执行认证的区块。这允许验证连续的执行状态和区块链的发出消息集合。执行状态通常通过在区块中包含执行哈希来在验证者之间进行比较。一条链的接收消息应该与其他链的发出消息进行比较（第5.2节）。

**Claim 6** (Worst-case Efficiency). *In a single-owner chain, Byzantine validators cannot significantly delay block proposals and block confirmations by correct users*.

声明 6（最坏情况效率）。在单所有者链中，拜占庭验证者无法通过正确用户显著延迟区块提案和区块确认。

Linera clients contact all the validators in parallel and consider an operation as completed as soon as they receive signatures from a quorum of validators (Section <a href='#Section2.8'>2.8</a>).

Linera 客户端会并行地联系所有验证者，并且一旦他们收到来自验证者择取的签名，就会将操作视为已完成（第2.8节）。

**Claim 7** (Monotonic block validation). *In a single-owner chain, if a block proposal is the first one to be signed by the owner at a given block height and it is accepted by an honest validator, then with appropriate actions, the chain owner always eventually succeeds in gathering enough votes to produce a certificate*.

声明 7（单调区块验证）。在单所有者链中，如果一个区块提案是在给定高度首次由所有者签名，并且被诚实验证者接受，那么通过适当的操作，链所有者总能最终成功地聚集足够的选票来生成一个证书。

![image](https://github.com/kikakkz/linera-whitepaper/assets/13128505/d8aea5cc-67c6-440b-bbb1-b9d457b96c8a)

If the block proposal *B* for a chain *id* is accepted by a validator and is the first one ever signed at this height, this means that every other validator α has already accepted the proposal (*i.e.* ${pending}^{id}(α) = B$) or has not voted yet (*i.e.* ${pending}^{id}(α) = ⊥$). In the latter case, block validation may temporarily fail for *α* if some earlier blocks or messages are missing: this can be resolved by updating the validator with the missing blocks (see Section <a href='#Section2.8'>2.8</a>). After proper synchronization, in the absence of external oracle and nondeterministic behaviors, submitting the proposal *B* to the validator will eventually produce the expected vote for *B*.

如果区块提案 B 用于某个链 ID 被验证者接受，并且是在该高度首次被签名，这意味着每个其他验证者 α 已经接受了该提案（即 ）或尚未投票（即 ）。在后一种情况下，如果一些早期的区块或消息丢失，可能会导致 α 的区块验证暂时失败：这可以通过向验证者更新缺失的区块来解决（参见第2.8节）。经过适当的同步，在没有外部预言机和非确定性行为的情况下，将提案 B 提交给验证者最终将产生对 B 的预期投票。

## 4 Building Web3 Applications in Linera    在 Linera 中构建 Web3 应用程序

<a name='Section4'>The</a> programming model of Linera <a href='#References30'>[1]</a> is designed to provide rich, language-agnostic composability to application developers while taking advantage of microchains for scaling.

Linera[1]的编程模型旨在为应用程序开发人员提供丰富的、与语言无关的可组合性，并利用微链进行扩展。

### 4.1 Creating applications    创建应用程序

Linera uses the WebAssembly (Wasm) virtual machine [<a href='#References3'>3</a>, <a href='#References23'>23</a>] as the execution engine for user applications. The SDK to develop Linera applications will be initially targeting the Rust language.

Linera 使用 WebAssembly（Wasm）虚拟机[3, 23]作为用户应用程序的执行引擎。最初，开发 Linera 应用程序的 SDK 将针对 Rust 语言。

An application is created in several steps (Figure 3). First, a software module (aka smart contract) in Rust is compiled to Wasm bytecode. The bytecode is then published by its author on a microchain of its choice and receives a unique *bytecode identifier*. Next, the bytecode is instantiated using the bytecode identifier and specific application parameters (*e.g.* name of the token, token supply, etc). This operation creates a fresh application identifier (“APP 1” in Figure 3) and initializes the local state of the application (“APP INSTANCE $1_B$”). This initial local state may hold specific parameters to help administrating the new application in the future.

应用程序的创建分为几个步骤（见图3）。首先，使用 Rust 编写的软件模块（也称为智能合约）被编译成 Wasm 字节码。然后，该字节码由其作者发布到所选的微链上，并获得一个唯一的字节码标识符。接下来，使用字节码标识符和特定的应用程序参数（例如代币的名称、代币供应量等）实例化字节码。这个操作创建了一个新的应用程序标识符（图3中的“APP 1”），并初始化了应用程序的本地状态（“APP INSTANCE”）。这个初始本地状态可能包含特定的参数，以帮助将来管理新应用程序。

A single bytecode identifier can spawn across multiple, independent applications that share the same code but do not share the same configuration (“APP 1” and “APP 2” in Figure 3).

一个字节码标识符可以衍生出多个独立的应用程序，它们共享相同的代码但不共享相同的配置（图3中的“APP 1”和“APP 2”）。

![image](https://github.com/kikakkz/linera-whitepaper/assets/13128505/4b5b2b92-3439-4a08-9eef-685e81534547)

### 4.2 Multi-chain deployment    多链部署

Linera applications are multi-chain by default in the sense that their global state is generally split across several chains. In other words, the local instance of an application at a given chain holds only the subset of the application state that is located there. For instance, in an ERC-20-like token management application, the owner of a single-owner chain may want to hold their personal accounts on the chain that they own.

Linera 应用程序在默认情况下是多链的，这意味着它们的全局状态通常分布在几个链上。换句话说，应用程序在给定链上的本地实例只持有该链上所在位置的应用程序状态的子集。例如，在类似 ERC-20 代币管理的应用程序中，单所有者链的所有者可能希望在他们拥有的链上持有他们的个人账户。

The bytecode of an application is automatically downloaded and the application started when the owner of a microchain accepts an incoming message (Section <a href='#Section2.5'>2.5</a>) from the application for the first time (“APP INSTANCE $1_C$” in Figure 3).

当微链的所有者首次接受来自应用程序的传入消息时（参见第2.5节），应用程序的字节码会被自动下载，并开始运行（图3中的“APP INSTANCE”）。

### 4.3 Cross-chain communication   跨链通信

<a name='Section4.3'>Cross-chain</a> communication between applications is realized using asynchronous calls to allow microchains to run independently. The programming style for cross-chain coordination between Linera applications is inspired by the actor model [6]. The implementation relies on cross-chain requests described in Section <a href='#Section2.5'>2.5</a>. The fundamental point is that each actor has exclusive access to its own internal state and that actors cannot call each other directly.

应用程序之间的跨链通信是通过异步调用实现的，以允许微链独立运行。Linera 应用程序之间的跨链协调编程风格受到了 actor 模型[6]的启发。该实现依赖于第2.5节中描述的跨链请求。其基本观点是每个 actor 都有对其自己内部状态的独占访问权，并且 actor 不能直接相互调用。

**Cross-chain messages.** Cross-chain messages allow an application to transfer arbitrary data asynchronously from one chain to another (Figure 4). To make sense of the data, the same application must be on the sending end and on the receiving end of a cross-chain message. In practice, the local instance of an application maintains an inbox per origin that the instance has communicated with. When an application wants to send a message to a destination, it returns a value containing the message so that the runtime can execute the appropriate cross-chain request.

跨链消息。跨链消息允许应用程序将任意数据异步从一条链传输到另一条链（图4）。为了理解这些数据，发送端和接收端必须是同一个应用程序。在实践中，应用程序的本地实例会维护与其通信的每个来源的收件箱。当应用程序希望向目标发送消息时，它返回一个包含消息的值，以便运行时可以执行适当的跨链请求。

Contrary to FastPay <a href='#References7'>[7]</a> and Zef <a href='#References8'>[8]</a>, Linera is not limited to payment requests and can deliver arbitrary cross-chain messages defined by user applications. The effects of cross-chain messages do not generally commute, therefore in Linera, the ordering in which incoming messages are received and then executed by a recipient’s chain is important. We solve this issue by relying on block proposers to specify the ordering of incoming messages when picking the messages from the chain inboxes.

与 FastPay [7] 和 Zef [8] 不同，Linera 不仅限于支付请求，还可以传递用户应用程序定义的任意跨链消息。跨链消息的影响通常不可交换，因此在 Linera 中，接收方链接收并执行传入消息的顺序很重要。我们通过依赖区块提议者来指定从链收件箱中提取消息的顺序来解决这个问题。

In general, messages are not guaranteed to be picked on the receiving side. When they are, the current implementation forces messages to be picked in order. This general policy will likely be refined in the future to account for specific use cases, notably for public chains where block production never stops (Section <a href='#Section2.9'>2.9</a>).

总的来说，在接收端，并不能保证消息一定会被提取。当它们被提取时，当前的实现强制消息按顺序提取。这种一般性策略可能会在未来得到进一步完善，以考虑特定的用例，特别是对于区块生产从不停止的公共链（第2.9节）。

**Pub/sub channels.** On top of the one-to-one communication, Linera supports one-tomany communication using *channels*. A user can create a channel within an application, while the same application’s instances residing on other microchains can subscribe to it by sending a subscribe message with the publisher application and chain identifiers. Importantly, a subscriber is added to a channel only when the publisher accepts the subscription by adding the registration message to its chain. Under the hood, channels act as a set of one-to-one connections. A message sent to a channel is delivered to all the inboxes that are subscribed to the channel and can be picked up by the subscribers. By design, a late subscriber, once accepted by the publisher, receives the last message sent to the channel—rather than the entire history of messages.

发布/订阅频道。除了一对一通信外，Linera 还支持使用频道进行一对多通信。用户可以在应用程序内创建频道，而驻留在其他微链上的同一应用程序实例可以通过发送带有发布者应用程序和链标识符的订阅消息来订阅它。需要注意的是，只有当发布者通过在其链上添加注册消息来接受订阅时，订阅者才会被添加到频道。在底层，频道充当一组一对一连接。发送到频道的消息会传递到所有订阅该频道的收件箱，并且能够被订阅者接收。设计上，一旦被发布者接受，晚订阅者会接收到发送到频道的最后一条消息，而不是整个消息历史。

### 4.4 Local composability  本地可组合性

**Synchronous calls.** On the same microchain, different Linera applications can be composed using synchronous calls similar to smart-contract calls in classical blockchains such as Ethereum <a href='#References32'>[32]</a> (see the top part of Figure 4). The state modifications resulting from a sequence of application calls and originating from a single user transaction are atomic. In other words, either all of the calls succeed or all of them fail. Calling an application creates a virtual copy of its internal state and executes the call on the cached state. At this point, the new state is not yet written to storage. If any of the transactions fails, all the staged modifications are discarded.

同步调用。在同一微链上，不同的 Linera 应用程序可以使用类似于经典区块链（如以太坊[32]）中的智能合约调用的同步调用进行组合（请参阅图4的顶部部分）。由一系列应用程序调用产生的状态修改是原子性的，并源自单个用户交易。换句话说，所有调用要么全部成功，要么全部失败。调用一个应用程序会创建其内部状态的虚拟副本，并在缓存状态上执行调用。此时，新状态尚未写入存储。如果任何事务失败，所有暂存的修改将被丢弃。

**Sessions.** In some cases, it is desirable to delegate the management of a piece of state from one application to another. We call the temporary object managing such a detached state a *session*. A typical example of a use case may go as follows: (i) an application B calls into the token-management application A; (ii) some tokens are withdrawn from the ledger of A and put into a new session; (iii) B receives ownership of the session; (iv) B calls into the session to move the tokens back to the ledger of A, say, under another account; this effectively consumes and terminates the session.

会话。在某些情况下，希望将一个状态片段的管理委托给另一个应用程序。我们将管理这样的分离状态的临时对象称为会话。一个典型的用例可能如下：（i）应用程序 B 调用代币管理应用程序 A；（ii）从 A 的分类帐中提取一些代币并放入一个新的会话；（iii）B 接收会话的所有权；（iv）B 调用会话将代币移回 A 的分类帐，例如，到另一个账户；这实际上消耗并终止了会话。

Sessions are guaranteed to be owned by a single application (no duplication). Consuming a session is not optional: sessions must be properly consumed before the end of the current transaction, otherwise, the transaction will fail. In addition to assets, sessions are thus suitable for managing temporary obligations, for instance, the obligation to pay back a flash loan <a href='#References25'>[25]</a>.

会话保证由单个应用程序拥有（不重复）。消耗会话是强制性的：会话必须在当前交易结束之前正确地被消耗，否则交易将失败。除了资产外，会话因此适用于管理临时义务，例如偿还闪电贷款的义务[25]。

### 4.5 User authentication   用户认证

Applications often need to authenticate end users in order to authorize certain actions. For instance, transferring an asset should require the permission of its owner.

在应用程序中，通常需要对最终用户进行身份验证以授权执行某些操作。例如，转移资产应该需要其所有者的许可。

In Linera, users are authenticated when they propose a block in a chain that they own (Section <a href='#Section2.8'>2.8</a>). During execution, the identity of the user that signed the current block, called the *authenticated signer*, is visible to all the operations contained in the block by default.

在Linera中，当用户在拥有的链上提出一个区块时会被认证（见第2.8节）。在执行过程中，签署当前区块的用户身份，默认情况下对该区块中包含的所有操作都是可见的，称为经过认证的签名者。

An operation creating a cross-chain message may optionally propagate the current authenticated signer along with the message. This is important so that assets temporarily placed on another chain (say, a public chain) may be claimed by their owner.

创建跨链消息的操作可以选择性地将当前经过认证的签名者与消息一起传播。这很重要，因为暂时放置在另一条链上（比如公共链）的资产可能需要它们的所有者来索取。

Similarly, authenticated signers may be propagated when calling another application on the same chain. This allows applications to program new categories of assets and make them available to other applications using abstract APIs.

同样，在调用同一链上的另一个应用程序时，经过认证的签名者也可以被传播。这使得应用程序能够编写新类别的资产，并使用抽象API使其对其他应用程序可用。

### 4.6 Ephemeral chains    瞬时链

Another specificity of the programming model of Linera is the ability to create short-lived permissioned chains (Section <a href='#Section2.9'>2.9</a>) meant for a short interaction between a small number of loosely coordinated users.

Linera编程模型的另一个特点是能够创建短暂的权限链（第2.9节），用于少数松散协调用户之间的短期交互。

For instance, two users may create a microchain for swapping two assets atomically. The shared microchain will have (up to) two owners and its parameters will be adapted to the exchange process. To use the chain, both users must transfer the assets that they want to exchange from their primary microchains to the shared chain, then one of the users must create a block to confirm or cancel the swap. Importantly, once the swap is concluded, the shared microchain is deactivated. This prevents any further extension of the temporary chain and allows archiving it in the future.

例如，两个用户可以为了原子地交换两种资产而创建一个微链。共享的微链将有（最多）两个所有者，并且其参数将适应交换过程。要使用该链，两个用户必须将他们想要交换的资产从各自的主微链转移到共享链上，然后其中一个用户必须创建一个区块来确认或取消交换。重要的是，一旦交换完成，共享的微链就会被停用。这样可以防止临时链的进一步延伸，并允许在将来对其进行归档。

To optimize liveness in the case of an ephemeral permissioned chain (Section <a href='#Section2.9'>2.9</a>), operations may interact with the user permissions to propose blocks as seen by the consensus protocol. For instance, in the case of a temporary chain for an atomic swap, it is desirable to restrict the ability to propose blocks to those owners who have already locked their assets. Another example is a temporary microchain dedicated to a game of chess between two users. Here, the application can determine which player needs to move and update the microchain consensus layer to accept the next block only from the chosen user. A more realistic chess application may also include a referee as an owner of the temporary chain to enforce progress.

为了优化瞬时的权限链（第2.9节）的活跃性，在操作中可以与用户权限交互以提出区块，如共识协议所见。例如，在用于原子交换的临时链的情况下，希望将提出区块的能力限制在那些已经锁定其资产的所有者身上。另一个例子是专门用于两个用户之间下国际象棋的临时微链。在这里，应用程序可以确定哪位玩家需要移动，并更新微链共识层，只接受来自所选用户的下一个区块。更实际的国际象棋应用程序可能还包括裁判作为临时链的所有者，以强制执行游戏的进展。

## 5 Decentralization   去中心化

<a name='Section5'>Linera</a> encourages validators to use cloud infrastructure to unlock elastic scaling and benefit from standard production environments. To maximize decentralization, Linera relies on two key features: delegated proof of stake (DPoS) and audits by the community.

Linera鼓励验证者使用云基础设施来实现弹性扩展，并从标准的生产环境中获益。为了最大程度地实现分散化，Linera依赖于两个关键特性：委托权益证明（DPoS）和社区的审计。

### 5.1 Delegated proof of stake   委托权益证明

To ensure the long-term security of the system, Linera relies on delegated proof of stake (DPoS): the voting rights of validators are functions of their stakes in the system, together with the stakes that are delegated to them by end users. For DPoS to function correctly, users must be able to change their delegation preferences, and validators must have an automated procedure to join and leave the system. Both operations require a public chain where any user can submit transactions. Reconfiguring validators also requires a carefullydesigned migration protocol for every chain. Both mechanisms were sketched in Section <a href='#Section2.9'>2.9</a>.

为了确保系统的长期安全性，Linera依赖于委托权益证明（DPoS）：验证者的投票权取决于他们在系统中的股权，以及最终用户委托给他们的股权。为了使DPoS能够正确运行，用户必须能够更改其委托偏好，并且验证者必须有一个自动化程序来加入和离开系统。这两项操作都需要一个公共链，任何用户都可以提交交易。重新配置验证者还需要为每条链设计一个精心设计的迁移协议。这两个机制已在第2.9节中概述。

Token delegation and economics will be made more precise in a separate document. To address long-range attacks—where old committees become corrupt <a href='#References17'>[17]</a>—, Linera allows microchains to refuse cross-chain messages (*e.g.* payments) from committees that are not trusted anymore (see Section <a href='#Section2.9'>2.9</a>).

代币委托和经济学将在另一份文件中作出更精确的规定。为了解决长程攻击（即旧的委员会变得腐败的情况），Linera允许微链拒绝来自不再受信任的委员会的跨链消息（例如支付）（参见第2.9节）。

### 5.2 Auditability    可审计性

<a name='Section5.2'>Auditing</a> a blockchain traditionally requires running a *full node* that locally holds a copy of the entire transaction history. However, in the case of a high-throughput system, this may require significant amounts of disk space and CPU resources. When regular users—those using commodity hardware—need days or weeks to fully audit a decentralized system, the community may not be able to credibly deter a coalition of rogue validators from altering the protocol. Light clients <a href='#References14'>[14]</a> reduce resource usage but only check the block headers and do not provide the same level of verification.

传统上，对区块链进行审计需要运行一个完整节点，该节点本地保存了整个交易历史的副本。然而，在高吞吐量系统的情况下，这可能需要大量的磁盘空间和CPU资源。当普通用户（即使用普通硬件的用户）需要数天甚至数周才能完全审计一个分散系统时，社区可能无法可信地阻止一组不良验证者改变协议。轻客户端[14]可以减少资源使用，但只检查区块头，并且不提供相同级别的验证。

In contrast, the microchain approach makes it possible for the community to continuously audit Linera validators. In Linera, an auditor is similar to a client (Section <a href='#Section2.8'>2.8</a>) in that it only needs to track a small subset of microchains. Because scalability in Linera relies on having many chains rather than larger blocks, it is always feasible to replay the execution of a single chain in real-time on commodity hardware.

相比之下，微链方法使得社区能够持续审计Linera的验证者。在Linera中，审计者类似于客户（第2.8节），他们只需要追踪微链的一个小子集。因为Linera的可扩展性依赖于拥有许多链而不是更大的区块，所以始终可以在普通硬件上实时重放单个链的执行过程。

For the Linera community to continuously verify all the chains, a distributed protocol can be put in place on top of a shared distributed storage such as IPFS <a href='#References5'>[5]</a> as follows. Executing the blocks in a chain allows to verify the execution state and the outgoing messages. Blocks should typically be marked as audited and the outgoing messages indexed in the distributed storage. To complete the verification of a chain, the client must also verify that each incoming message was indeed produced by its sender chain. This can be done by looking up incoming messages in the shared storage to see if they have been verified already, and otherwise, schedule their verification.

为了让Linera社区持续验证所有的链，可以在像IPFS [5]这样的共享分布式存储之上建立一个分布式协议。执行链中的区块可以验证执行状态和外发消息。区块通常应标记为已审计，并且外发消息应索引在分布式存储中。为了完成对链的验证，客户还必须验证每个传入消息确实是由其发送链产生的。这可以通过查找共享存储中的传入消息来完成，以查看它们是否已经被验证，否则安排它们的验证。

## Conclusion  结论

Linera aims to deliver the first multi-chain infrastructure with predictable performance, responsiveness, and security at the Internet scale. To do so, Linera introduces the idea of operating many parallel chains, called *microchains*, in the same set of validators, and using the internal network of each validator to quickly deliver the asynchronous messages between chains. This architecture has a number of advantages:

Linera旨在在互联网规模上提供可预测的性能、响应能力和安全性的第一个多链基础设施。为了实现这一目标，Linera引入了在相同一组验证者中运行许多并行链（称为微链）的概念，并利用每个验证者的内部网络快速传递链之间的异步消息。这种架构具有许多优势：

- **Elastic scaling.** In Linera, scalability is obtained by adding chains, not by increasing the size or the rate of blocks. Each validator may add and remove capacity (aka internal workers) at any time to maintain nominal performance for multi-chain applications.
- 弹性扩展。在Linera中，可伸缩性是通过增加链的方式获得的，而不是通过增加区块的大小或速率。每个验证者可以随时添加和移除容量（即内部工作人员），以维持多链应用的名义性能。
- **Responsiveness.** When microchains are operated by a single user, Linera uses a simplified mempool-free consensus protocol inspired by reliable broadcast [<a href='#References7'>7</a>,<a href='#References12'>12</a>]. This reduces block latency and ultimately makes Web3 applications more responsive.
- 响应能力。当微链由单个用户操作时，Linera使用了受可靠广播[7,12]启发的简化无内存池共识协议。这降低了区块延迟，并最终使Web3应用更具响应性。
- **Composability.** Compared to other multi-chain systems, low block latency also helps with composability: it allows receivers of asynchronous messages from another chain to quickly answer by adding a new block.
- 可组合性。与其他多链系统相比，低区块延迟还有助于可组合性：它允许来自另一条链的异步消息的接收者通过添加新区块来快速回复。
- **Chain security.** Compared to traditional multi-chain systems, a benefit of running all the microchains in the same set of validators is that creating chains does not impact the security model of Linera.
- 链安全。与传统的多链系统相比，在同一组验证者中运行所有微链的好处在于创建链不会影响Linera的安全模型。
- **Decentralization.** Linera relies on delegated proof of stake (DPoS) for security. Each microchain can be separately executed on commodity hardware. This allows clients and auditors to continuously run their own verifications and hold validators accountable.
- 分散化。Linera依赖于委托权益证明（DPoS）来确保安全性。每个微链可以在普通硬件上单独执行。这使得客户和审计者可以持续运行自己的验证并追究验证者的责任。
- **Language agnostic.** The programming model of Linera does not depend on a specific programming language. After careful consideration, we have decided to concentrate our efforts on Wasm and Rust for the initial execution layer of Linera.
- 语言无关。Linera的编程模型不依赖于特定的编程语言。经过慎重考虑后，我们决定将我们的工作集中在Wasm和Rust上，用于Linera的初始执行层。

In future reports, we will formalize the protocols to support multi-owner chains as well as the other extensions mentioned in Section <a href='#Section2.9'>2.9</a>. In particular, we plan to incorporate a state-of-the-art consensus mechanism (e.g. [<a href='#References16'>16</a>, <a href='#References22'>22</a>, <a href='#References27'>27</a>]) on top of our existing multi-chain infrastructure. We also plan to describe the economic models for the fair remuneration of validators and incentivization of users separately. Linera’s ability to deactivate and archive microchains provides an elegant venue to control the storage costs of validators in the future. In general, we anticipate that Linera’s integrated architecture and the minimization of validator interactions will be extremely helpful when it comes to optimizing the costs of operating validators at scale.

在未来的报告中，我们将正式制定支持多所有者链的协议，以及第2.9节中提到的其他扩展。特别是，我们计划在我们现有的多链基础设施之上纳入最先进的共识机制（例如[16, 22, 27]）。我们还计划分别描述用于公平酬金验证者和激励用户的经济模型。Linera停用和存档微链的能力为将来控制验证者的存储成本提供了一个优雅的方法。总的来说，我们预计当涉及到优化大规模运营验证者的成本时，Linera集成的架构和最小化验证者交互将会非常有帮助。

<h1>References</h1>
<a name='References1'>[1]</a> Linera developer manual. https://linera.dev.

<a name='References2'>[2]</a> Linera github repository. https://github.com/linera-io/linera-protocol.

<a name='References3'>[3]</a> WebAssembly. https://webassembly.org/.

<a name='References4'>[4]</a> The Bytecode Alliance. https://bytecodealliance.org/, 2022.

<a name='References5'>[5]</a> The InterPlanetary File System. https://ipfs.tech/, 2022.

<a name='References6'>[6]</a> Gul Agha. Actors: a model of concurrent computation in distributed systems. MIT press, 1986.

<a name='References7'>[7]</a> Mathieu Baudet, George Danezis, and Alberto Sonnino. Fastpay: High-performance Byzantine fault tolerant settlement. In Proceedings of the 2nd ACM Conference on Advances in Financial Technologies, pages 163–177, 2020.

<a name='References8'>[8]</a> Mathieu Baudet, Alberto Sonnino, Mahimna Kelkar, and George Danezis. Zef: Low-latency, scalable, private payments. arXiv preprint arXiv:2201.05671, 2022.

<a name='References9'>[9]</a> Sam Blackshear, Evan Cheng, David L. Dill, Victor Gao, Ben Maurer, Todd Nowacki, Alistair Pott, Shaz Qadeer, Rain, Dario Russi, Stephane Sezer,Tim Zakian, and Runtian Zhou.  Move: A language with programmable resources.  https://diem-developers-components.netlify.app/papers/diem-move-a-language-with-programmable-resources/2020-05-26.pdf, 2020.

<a name='References10'>[10]</a> Vitalik Buterin. Endgame. https://vitalik.ca/general/2021/12/06/endgame.html, 2021.

<a name='References11'>[11]</a> Vitalik Buterin. An incomplete guide to rollups. https://vitalik.ca/general/2021/01/05/rollup.html, 2021.

<a name='References12'>[12]</a> Christian Cachin, Rachid Guerraoui, and Luı́s Rodrigues. Introduction to reliable and secure distributed programming. Springer Science & Business Media, 2011.

<a name='References13'>[13]</a> Miguel Castro, Barbara Liskov, et al. Practical Byzantine fault tolerance. In OsDI, volume 99, pages 173–186, 1999.

<a name='References14'>[14]</a> Panagiotis Chatzigiannis, Foteini Baldimtsi, and Konstantinos Chalkias. Sok:Blockchain light clients. Cryptology ePrint Archive, 2021.

<a name='References15'>[15]</a> Philip Daian, Steven Goldfeder, Tyler Kell, Yunqi Li, Xueyuan Zhao, Iddo Bentov,Lorenz Breidenbach, and Ari Juels. Flash boys 2.0: Frontrunning in decentralized ex-changes, miner extractable value, and consensus instability. In 2020 IEEE Symposium on Security and Privacy (SSP’20), pages 910–927. IEEE, 2020.

<a name='References16'>[16]</a> George Danezis, Lefteris Kokoris-Kogias, Alberto Sonnino, and Alexander Spiegelman. Narwhal and Tusk: a DAG-based mempool and efficient BFT consensus. In Proceedings of the Seventeenth European Conference on Computer Systems, pages 34–50, 2022.

<a name='References17'>[17]</a> Evangelos Deirmentzoglou, Georgios Papakyriakopoulos, and Constantinos Patsakis. A survey on long-range attacks for proof of stake protocols. IEEE Access, 7:28712–28725,2019.

<a name='References18'>[18]</a> Ittay Eyal, Adem Efe Gencer, Emin Gün Sirer, and Robbert Van Renesse. {Bitcoin-NG}: A scalable blockchain protocol. In 13th USENIX symposium on networked sys-tems design and implementation (NSDI 16), pages 45–59, 2016.

<a name='References19'>[19]</a> Rati Gelashvili, Alexander Spiegelman, Zhuolun Xiang, George Danezis, Zekun Li,Dahlia Malkhi, Yu Xia, and Runtian Zhou. Block-STM: Scaling blockchain execution by turning ordering curse to a performance blessing, 2022.

<a name='References20'>[20]</a> Arthur Gervais, Ghassan O Karame, Karl Wüst, Vasileios Glykantzis, Hubert Ritzdorf,and Srdjan Capkun. On the security and performance of proof of work blockchains. In Proceedings of the 2016 ACM SIGSAC conference on computer and communications security, pages 3–16, 2016.

<a name='References21'>[21]</a> Arthur Gervais, Hubert Ritzdorf, Ghassan O Karame, and Srdjan Capkun. Tampering with the delivery of blocks and transactions in bitcoin. In Proceedings of the 22nd ACM SIGSAC Conference on Computer and Communications Security, pages 692–705, 2015.

<a name='References22'>[22]</a> Neil Giridharan, Lefteris Kokoris-Kogias, Alberto Sonnino, and Alexander Spiegelman. Bullshark: DAG BFT protocols made practical. arXiv preprint arXiv:2201.05677,2022.

<a name='References23'>[23]</a> Andreas Haas, Andreas Rossberg, Derek L Schuff, Ben L Titzer, Michael Holman, Dan Gohman, Luke Wagner, Alon Zakai, and JF Bastien. Bringing the web up to speed with webAssembly. In Proceedings of the 38th ACM SIGPLAN Conference on Programming Language Design and Implementation, pages 185–200, 2017.

<a name='References24'>[24]</a> Jae-Yun Kim, Junmo Lee, Yeonjae Koo, Sanghyeon Park, and Soo-Mook Moon. Ethanos: efficient bootstrapping for full nodes on account-based blockchain. In Pro-ceedings of the Sixteenth European Conference on Computer Systems, pages 99–113,2021.

<a name='References25'>[25]</a> Krešimir Klas. Smart contract development — Move vs. Rust. https://medium.com/@kklas/smart-contract-development-move-vs-rust-4d8f84754a8f, 2022.

<a name='References26'>[26]</a> Michal Król, Onur Ascigil, Sergi Rene, Alberto Sonnino, Mustafa Al-Bassam, and Etienne Rivière. Shard scheduler: object placement and migration in sharded account-based blockchains. In Proceedings of the 3rd ACM Conference on Advances in Financial Technologies, pages 43–56, 2021.

<a name='References27'>[27]</a> Dahlia Malkhi and Kartik Nayak. Hotstuff-2: Optimal two-phase responsive bft. Cryp-tology ePrint Archive, 2023.

<a name='References28'>[28]</a> Du Mingxiao, Ma Xiaofeng, Zhang Zhe, Wang Xiangwei, and Chen Qijun. A review on consensus algorithm of blockchain. In 2017 IEEE international conference on systems,man, and cybernetics (SMC), pages 2567–2572. IEEE, 2017.

<a name='References29'>[29]</a> nanfengpo. A design of decentralized ZK-rollups based on EIP-4844. https://ethresear.ch/t/a-design-of-decentralized-zk-rollups-based-on-eip-4844/12434, 2022.

<a name='References30'>[30]</a> Slashdot. Best blockchain apis of 2022. https://slashdot.org/software/blockchain-apis/, 2022.

<a name='References31'>[31]</a> Alberto Sonnino. Chainspace: A sharded smart contract platform. In Network andDistributed System Security Symposium 2018 (NDSS 2018), 2018.

<a name='References32'>[32]</a> Gavin Wood et al. Ethereum: A secure decentralised generalised transaction ledger.Ethereum project yellow paper, 151(2014):1–32, 2014.

<a name='References33'>[33]</a> Mahdi Zamani, Mahnush Movahedi, and Mariana Raykova. Rapidchain: Scaling blockchain via full sharding. In Proceedings of the 2018 ACM SIGSAC conference on computer and communications security, pages 931–948, 2018.
