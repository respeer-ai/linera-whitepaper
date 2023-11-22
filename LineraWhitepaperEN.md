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

