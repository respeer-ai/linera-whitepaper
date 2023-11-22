## Linera: a Blockchain Infrastructure for Highly Scalable Web3 Applications
#### Version 2 – August 16, 2023
### Abstract

We present Linera, a blockchain infrastructure that aims to support the most demanding Web3 applications by providing them with predictable performance, security, and responsiveness at the Internet scale. To do so, Linera solves the blockspace scarcity problem by introducing a new, integrated, multi-chain paradigm built upon elastic validators. Linera puts users at the center of the protocol by allowing them to manage the production of blocks in their own chains—called microchains—for optimal performance. To help Web3 developers make the most of the Linera infrastructure, we have developed a rich, language-agnostic, multi-chain programming model. Linera applications communicate across chains using asynchronous messages. Within the same microchain, applications are composed using synchronous calls and ephemeral sessions (aka resources). The initial SDK of Linera will target Rust programmers, thanks to the Wasm virtual machine. The Linera infrastructure is based on delegated proof of stake. It will ensure robust decentralization using state-of-the-art economic incentives and auditing at scale by the community[[1](<> 'Legal Disclaimer: This document and its contents are not an offer to sell, or the solicitation of an offer to buy, any tokens. We are publishing this white paper solely to receive feedback and comments from the public. Nothing in this document should be read or interpreted as a guarantee or promise of how the Linera infrastructure or its tokens (if any) will develop, be utilized, or accrue value. Linera only outlines its current plans, which could change at its discretion, and the success of which will depend on many factors outside of its control. Such forward-looking statements necessarily involve known and unknown risks, which may cause actual performance and results in future periods to differ materially from what we have described or implied in this document. Linera undertakes no obligation to update its plans. There can be no assurance that any statements in the document will prove to be accurate, as actual results and future events could differ materially. Please do not place undue reliance on future statements.')].

## Contents
1 [**Introduction**](<>)<br>
&ensp;1.1 [The need for predictable performance and responsiveness in Web3](<>)<br>
&ensp;1.2 [The blockspace scarcity problem](<>)<br>
&ensp;1.3 [Shortcomings of existing approaches](<>)<br>
&ensp;1.4 [Our mission](<>)<br>
&ensp;1.5 [Overview of the project](<>)<br>
&ensp;&ensp;1.5.1 [An integrated multi-chain system with elastic validators](<>)<br>
&ensp;&ensp;1.5.2 [Making multi-chain programming mainstream](<>)<br>
&ensp;&ensp;1.5.3 [Robust decentralization for elastic validators](<>)<br>
2 [**The Linera Multi-Chain Protocol**](<>)<br>
&ensp;2.1 [Participants: users, validators, chain owners](<>)<br>
&ensp;2.2 [Security model](<>)<br>
&ensp;2.3 [Notations](<>)<br>
&ensp;2.4 [Microchains](<>)<br>
&ensp;2.5 [Cross-chain requests](<>)<br>
&ensp;2.6 [Chain states](<>)<br>
&ensp;2.7 [Block execution](<>)<br>
&ensp;2.8 [Client/validator interactions](<>)<br>
&ensp;2.9 [Extensions to the core protocol](<>)<br>
3 [**Analysis of the Multi-Chain Protocol**](<>)<br>
&ensp;3.1 [Responsiveness](<>)<br>
&ensp;3.2 [Scalability](<>)<br>
&ensp;3.3 [Security](<>)<br>
4 [**Building Web3 Applications in Linera**](<>)<br>
&ensp;4.1 [Creating applications](<>)<br>
&ensp;4.2 [Multi-chain deployment](<>)<br>
&ensp;4.3 [Cross-chain communication](<>)<br>
&ensp;4.4 [Local composability](<>)<br>
&ensp;4.5 [User authentication](<>)<br>
&ensp;4.6 [Ephemeral chains](<>)<br>
5 [**Decentralization**](<>)<br>
&ensp;5.1 [Delegated proof of stake](<>)<br>
&ensp;5.2 [Auditability](<>)<br>
6 [**Conclusion**](<>)<br>
A [**Cross-chain communication**](<>)<br>
&ensp;A.1 [Messages and inboxes](<>)<br>
&ensp;A.2 [Cross-chain requests and outboxes](<>)<br>

## 1 Introduction
### 1.1 The need for predictable performance and responsiveness in Web3

Thanks to blockchain technologies, the next iteration of the Internet, Web3, will empower users with a new generation of asset-aware applications and give them more democratic control over the digital economy. However, developing Web3 applications with a great user experience is currently a challenging task. One of the issues is reliability and responsiveness at scale: when too many users are active, blockchains may stop responding or demand punishing fees. In general, application developers want their infrastructure programming interfaces to be easy to use and predictable, disregarding the traffic caused by other applications. Centralized API providers [30] have been proposed to facilitate programming on top of popular blockchains, but such providers need to be trusted and will not improve the performance and the fees of the underlying blockchains. Linera aims to close the gap between centralized and decentralized applications by delivering a blockchain infrastructure that guarantees performance and responsiveness at scale.

### 1.2 The blockspace scarcity problem

The main reason why traditional blockchains have unpredictable worst-case outcomes in terms of fees and delays can be explained as the blockspace scarcity problem. Namely, in a blockchain composed of a single chain of blocks, users must compete to have their transactions selected into the next block. Yet, at the same time, the production rate and the size of blocks are limited by the performance of the consensus protocol, the network, and the execution layer. As a result, during a peak of traffic (say, an NFT airdrop), users may be outpriced by others or be delayed for long periods of time—during which the infrastructure is effectively unavailable to them [21].

### 1.3 Shortcomings of existing approaches

Unsurprisingly, many blockchain infrastructures have been proposed over the years with scalability improvements in mind. We provide here a high-level summary of the most common approaches, without attempting to be exhaustive.

**Faster single chain.** The production rate of blocks in a single chain is typically limited by the data propagation delay between validators [18]. Historically, block size has been the first parameter to be adjusted to maximize transaction throughput in function of the security requirements and the network constraints [18, 20]. Thanks to recent advances in BFT consensus protocols (e.g. [22]), nowadays the new bottleneck for the transaction rate appears to be the sequential execution of transactions rather than consensus ordering.

Anticipating that many transactions contained in a block should be independent in practice, several recent projects have developed architectures able to execute a subset of transactions in parallel on several processing units [19]. While this certainly results in higher transaction rates, such systems are still characterized by a maximum number of transactions per second in the low 6 digits. Moreover, the effective transaction rate greatly depends on the proportion of transactions that are actually independent in each block [26]. Altogether, this makes it impossible to guarantee fees and/or delays in advance for a user without any assumption about the activity of the other users.

Lastly, in a high-throughput chain, auditing validators is made harder by the combination of CPU requirements for execution and networking requirements for data synchronization. Concretely, the sheer number of sequential transactions may prevent members of the community with only commodity hardware from replaying transactions fast enough to verify the work of validators in a meaningful way [24].

**Blockchain sharding.** Another popular direction to address blockchain scalability has consisted in dividing the execution state between a fixed number of parallel chains, each being run independently by a separate set of validators. This is called blockchain sharding.

While this approach is still being continuously improved, it has historically suffered from several challenges. First, using separate sets of validators creates a security tradeoff in so far as an attacker may selectively attack the weakest set in the system (e.g. to mint coins). Second, reorganizing the shards, i.e. the way user accounts are distributed across chains, is a complex operation that necessitates extensive network communication [33]. Lastly, when the number of shards is increased to support additional traffic, so does the amount of cross-chain messages that need to be exchanged [26]. In a system where each shard has a separate set of validators, cross-chain messages create significant delays that ultimately cancel out the effect of adding new chains [31, 33].

**Rollups.** Finally, a popular approach to solve blockspace scarcity has been rollup protocols, either optimistic or based on validity proofs (aka ZK rollups) [11]. At a high-level, both optimistic and validity (“ZK”) rollups consist of a layer-2 protocol that builds a sequence of large blocks, meant to be executed, compressed and confirmed on layer 1. Unfortunately, the process of confirming transactions on layer 1 takes a long time in both cases. Optimistic rollups must wait several days to allow for dispute resolution. Validity rollups must compress many layer-2 transactions at a time to pay for the layer-1 gas. In practice, gathering enough layer-2 transactions, computing a validity proof, and archiving transactions to enforce rigorous data availability takes several hours per layer-2 block.

Long layer-1 confirmation times may encourage certain users to accept a security tradeoff and trust the finality of layer 2 for certain applications. In general, rollups must be trusted to carry on the protocol (i.e. for liveness) and to select transactions fairly (see Miner Extractable Value [15]). This concern is visible in the recent efforts to design decentralized rollup protocols [29].

### 1.4 Our mission

Motivated by these observations, the Linera project was created to develop a new type of Web3 infrastructure based on three key principles:

- (**i**) Build a secure infrastructure with predictable performance and responsiveness — by operating many chains in a single set of elastic validators;
- (**ii**) Enable a rich ecosystem of scalable Web3 applications — by working on a new execution layer to make multi-chain programming mainstream;
- (**iii**) Maximize decentralization — by ensuring that elastic validators are optimally incentivized and audited at scale by the community.

### 1.5 Overview of the project

Linera is dedicated to delivering the following innovations to the blockchain community.

#### 1.5.1 An integrated multi-chain system with elastic validators

To fulfill our vision of a Web3 infrastructure with predictable performance and responsiveness at scale, we have developed a new multi-chain protocol designed to take advantage of modern cloud infrastructures:

- (**1**) In Linera, a validator is an elastic Web2-like service that validates and executes blocks of transactions in many chains in parallel. Because the number of chains (active and inactive) present in a Linera system is meant to be unlimited, we also call them microchains.
- (**2**) The task of actively extending a microchain with new blocks is separate from validation or execution and is assumed by the owner(s) of each chain. Every Linera user is encouraged to create a chain that they own and place their accounts there.
- (**3**) Every validator manages all the microchains. (We call this the integrated multi-chain approach.) Microchains interact using asynchronous messages and otherwise run independently. As a result, validators can scale elastically by dividing their workload between many internal workers (aka shards). Asynchronous messages between chains are implemented efficiently using the internal network of each validator.
- (**4**) Microchains may differ in the way they accept new blocks. When extending their own chains, users submit new blocks directly to validators using a low-latency, mempool-free protocol inspired by reliable broadcast [7, 12]. Applications that require more complex interactions between users may also rely on ephemeral microchains created on demand. In practice, only the public microchains owned by the Linera infrastructure necessitate a full BFT consensus protocol [12].
- (**5**) As a rule, validators do not interact—except for public chains owned by the infrastructure. Synchronization of microchains between validators is delegated to chain owners. This means that inactive microchains (those not creating blocks) have no cost for validators other than storage.

Using elastic validators is a distinctive assumption of Linera. We intend for the Linera community to support a variety of cloud providers that new validators can choose from. Linera was initially inspired by the academic low-latency payment protocol FastPay developed at Meta [7]. Linera generalizes FastPay notably by turning user accounts into microchains, adding smart contracts, and supporting arbitrary asynchronous messages between chains. A more detailed description of the Linera multi-chain protocol is given in Section 2. We analyze the protocol in Section 3.

#### 1.5.2 Making multi-chain programming mainstream

Linera integrates many chains in a unique set of validators. This greatly facilitates crosschain communication thanks to the internal network of each validator. For the first time, a variety of Web3 applications have the opportunity to scale elastically by taking advantage of a cheap and efficient multi-chain architecture. To promote the adoption of multi-chain programming, we have made the following design choices:

- (**1**) The execution model of Linera is designed to be language-agnostic and developerfriendly. The initial SDK of Linera will be based on Wasm and will target the Rust programming language.
- (**2**) Linera applications are composable and multi-chain. Once an application is created, it can run on demand on any chain. The running instances of the same application coordinate across chains using asynchronous messages and pub/sub channels. Applications that are running in the same microchain interact using cross-contract calls and ephemeral session objects.

Session objects in Linera are inspired by resources in the Move language [9]. Staticallytyped resources in Move have been proposed to help with composability [25]. In Linera, resource-like composability is achieved using session handles and runtime checks. For instance, to send tokens, a Linera contract will be able to transfer ownership of a temporary session containing the tokens.

In general, building a large community of developers is a major factor in the adoption of blockchain infrastructures. Because the Wasm ecosystem is continuously improving its multi-language tooling [4], it offers the long-term possibility for Linera to serve several developer communities. See Section 4 for a more detailed discussion of the programming model of Linera.

#### 1.5.3 Robust decentralization for elastic validators

The classical “blockchain trilemma” [10] asserts the difficulty of simultaneously achieving scalability, security, and decentralization. While this observation certainly holds for validators of fixed capacity, we believe that insufficient efforts have been made in the definition and implementation of a satisfying notion of decentralization for elastic validators.

- (**1**) Linera relies on delegated proof of stake (DPoS) for security and supports regularly changing sets of validators. Thanks to the chaining of blocks, the past transactions, the cross-chain messages, and the execution state of each microchain are tamper-resistant.
- (**2**) Microchains are designed to be auditable independently. This means that Linera as a whole will be auditable in a distributed way by the community, using only commodity hardware.

Using large validators for performance and maintaining decentralization using communitydriven auditors has been discussed by the blockchain community in the context of rollups [10]. As the Linera project makes progress, we will continue to monitor the technical advances in the field of validity (“ZK”) proofs and chain compression. Decentralization of Linera is further discussed in Section 5.

## 2 The Linera Multi-Chain Protocol

We now introduce the multi-chain protocol at the core of the Linera infrastructure. This technical description is meant to illustrate the main ideas of the protocol without being exhaustive. We analyze the protocol informally in Section 3 and discuss the programming model in Section 4.

### 2.1 Participants: users, validators, chain owners

The Linera protocol aims to provide a computing infrastructure where developers create decentralized applications and end users interact with them in a secure and efficient way.

As usual for blockchain systems, the state of an application in Linera is replicated across several partially-trusted nodes called validators. Modifying the state of an application is done by inserting a transaction into a new block and submitting the new block to the validators.

To support scalability requirements, Linera is designed from the start as an integrated multi-chain system: instead of using a single chain, transactions are organized in many parallel chains of blocks, called microchains. This means that the state of Linera applications is typically distributed across chains. Importantly, unless a reconfiguration is in progress (Section 2.9), a single set of validators is in use for all the microchains.

In Linera, the task of extending a chain with new blocks is separate from the task of validating blocks. Proposing blocks is assumed by the owners of a microchain. In practice, the owner(s) of a chain can be any participant to the protocol. Because Linera validators act as a block validation service, chain owners may also be referred to as clients. Examples of chain owners include:

- End users who wish more control over their accounts in different applications;
- End users who wish to operate a temporary chain (e.g. for an atomic swap);
- Developers who wish to publish code or manage applications;
- Validators who collectively run a public chain (e.g. for infrastructure purposes).

The last use case is how Linera manages the current set of validators, also known as the committee. The programming model of Linera is presented in Section 4. The additional role of auditors is discussed in Section 5.

### 2.2 Security model

Linera is designed to be Byzantine-Fault Tolerant (BFT) [13]. All participants generate a key pair consisting of a private signature key and the corresponding public verification key. Linera uses a delegated proof of stake (DPoS) model [28], where the voting power of each validator is bound to its stake and the stake delegated to it by users.

**Assumptions.** We present the Linera protocol for a total voting power of *N*. A fixed, unknown subset of *Byzantine* (aka *dishonest*) validators may deviate from the protocol. It is assumed that they control at most *f* voting power for some value *f* such that 0 ≤ *f* < $\frac{ N }{ 3 }$. This is similar to many BFT protocols [7, 13]. In practice, one chooses the largest possible value for *f*, namely *f* = $\lfloor \frac{ N - 1 }{ 3 } \rfloor$.

We do not make any assumptions about users, chain owners, or on the networking layer when it comes to safety properties. Unless specified otherwise, liveness properties do not depend on network delays or message ordering. In other words, the network is *asynchronous* [13].

We use the word *quorum* to refer to a set of signatures issued by validators with a combined voting power of at least *N − f*. An important property of quorums, called quorum *intersection*, is that for any two quorums, there exists an honest validator *α* that is present in both. When data (typically a block) is signed by a quorum of validators, it is said to be *certified*. Certified data is also called a *certificate* for short.

**Goals.** Linera aims to guarantee the following security properties:
- *Safety:* For any microchain, every validator sees (a prefix of) the same chain of blocks, therefore it applies the same sequence of modifications to the execution state of the chain and eventually delivers the same set of messages to the other chains.
- *Eventual consistency of chains:* If a microchain is extended with a new certified block on an honest validator, any user can take a series of steps to ensure that this block is added to the chain on every honest validator.
- *Eventual consistency of asynchronous messages:* If a microchain receives a cross-chain message on an honest validator, any user can take a series of steps to ensure that this message is received by the chain on every honest validator.
- *Authenticity:* Only the owner(s) of a microchain can extend their microchain.
- *Piecewise Auditability:* There is sufficient public cryptographic evidence for the state of Linera to be audited for correctness in a distributed way, one chain at a time.

For single-owner chains (Section 2.4), Linera also guarantees the following properties:

- *Monotonic block validation:* In a single-owner chain, if a block proposal is the first one to be signed by the owner at a given block height and it is accepted by an honest validator, then with appropriate actions, the chain owner always eventually succeeds in gathering enough votes to produce a certificate.
- *Worst-case Efficiency:* In a single-owner chain, Byzantine validators cannot significantly delay block proposals and block confirmations by correct users.

### 2.2 Notations

We assume a collision-resistant hash function, noted **hash**(·), as well as a secure public-key signature scheme **sign**[.]. A quorum of signatures on a block *B* forms a certificate noted *C* = **cert**[*B*]. In the rest of this report, we identify certificates on the same block *B* and simply write *C* = **cert**[*B*] when *C* is any certificate on *B*.

The state of the Linera system is replicated across all validators. For a given validator, noted *α*, we use the notation *X*(*α*) to denote the current view of *α* regarding some replicated data *X*. A *data type D* = $<Tag, arg1, . . . , argn> is a sequence of values starting with a distinct marker **Tag** and meant to be sent over the network. We use capitalized names to distinguish data type markers from mathematical functions (e.g. **hash**) or data fields (e.g. **owner**$^{id}$(*α*)), and simply write **Tag**(arg1, . . . , argn) for a data type. We write $\widetilde{D}$ for a sequence of data types (*D1, . . . Dn*).

