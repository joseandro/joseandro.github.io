---
title: "What is zero knowledge cryptography?"
date: 2023-06-05T01:30:03+00:00
weight: 1
aliases: ["/zk-intro"]
showToc: true
TocOpen: false
math: true
draft: false
hidemeta: false
comments: true
description: "Before the \"how\", here is the \"what\""
disableHLJS: true # to disable highlightjs
disableShare: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "/img/zk_snarks_intro.png" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---
# Zero knowledge proofs
Zero knowledge proofs (aka ZKP) are a central component of zk-cryptography. They prove you have knowledge of an answer without directly providing that answer. Sounds quite innocuous, right? Let's step back and define what proofs are. We will not worry about the zero knowledgeless here because they can be easily added at a later stage, without loss of generality, when we know more about how more foundational pieces of zk-cryptography work together. 

## Proofs
Differently from the classical mathematical proofs, the proofs in ZKP always correspond to a proof that a claim between the **Prover** and the **Verifier** is true. The communication between these actors form an interactive protocol. 

## Interactive proofs
![Proofs in the interactive protocol context](img/interactive_protocol.png#center)

The picture above introduces two actors: **Prover (aka P)** and **Verifier (aka V)**. You can think of them both as algorithms. These actors interact with each other following a protocol. This interactive protocol is the basis of the whole ZKP theory. It can take many shapes and variations, but we will start by thinking about it in the following way: there exists a **Claim** that is public to both the **Prover** and the **Verifier** (some people also describe the **Claim** as being an "input" to both actors). The **Prover** communicates with with the **Verifier** (by sending **Proofs**) until the Verifier has enough confidence to either accept or reject the **Proofs** that the **Claim** is being proven or not. In this protocol the **Prover** is very powerful, much more than the **Verifier** is. We say that the **Prover** has unbounded computing time, while the **Verifier** is bounded to polynomial time in the size of the **Claim**.

# zk-SNARKs
**SNARK** (succinct arguments of knowledge) are succinct non-interactive proofs that a statement is true. Here, the interactive protocol above is rendered non-interactive with the Prover communicating with the Verifier a single time to prove with enough confidence that a short proof of a claim (i.e., statement) is true.