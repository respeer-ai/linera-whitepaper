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
