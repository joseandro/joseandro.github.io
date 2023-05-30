---
title: "Different approaches to zero-knowledge protocol design"
date: 2023-05-29T11:30:03+00:00
weight: 1
aliases: ["/zk-intro"]
showToc: true
TocOpen: false
math: true
draft: false
hidemeta: false
comments: true
description: "Desc Text."
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
## Introduction
### Approaches to zero-knowledge protocol design 
Argument systems are typically developed in a two-step process. First, an information-theoretically secure protocol, such as an IP, multi-prover interactive proof (MIP), or probabilistically checkable proof (PCP), is developed for a model involving one or more provers that are assumed to behave in some restricted manner (e.g., in an MIP, the provers are assumed not to send information to each other about the challenges they receive from the verifier). Second, the information-theoretically secure protocol is combined with cryptography to “force” 	a (single) prover to behave in the restricted manner, thereby yielding an argument system. This second step also often endows the resulting argument system with important properties, such as zero-knowledge, succinctness, and non-interactivity. If the resulting argument satisfies all of these properties, then it is in fact a zk-SNARK. 

By now, there are a variety of promising approaches to developing efficient zk-SNARKs, which can be categorized by the type of information-theoretically secure protocol upon which they are based. These include (1) IPs, (2) MIPs, (3) PCPs, or more precisely a related notion called interactive oracle proofs (IOPs), which is a hybrid between an IP and a PCP, and (4) linear PCPs. 

IPs, MIPs, and PCPs/IOPs can all be transformed into succinct interactive arguments by combining them with a cryptographic primitive called a polynomial commitment scheme (PCS); the interactive arguments can then be rendered non-interactive and publicly verifiable by applying a cryptographic technique called the Fiat-Shamir transformation (Section 5.2), yielding a SNARK. Transformations from linear PCPs to arguments are somewhat different, though closely related to certain polynomial commitment schemes. 		

## Proofs and proof systems
This survey covers different notions of mathematical proofs and their applications in computer science and cryptography. Informally, what we mean by a proof is anything that convinces someone that a statement is true, and a “proof system” is any procedure that decides what is and is not a convincing proof. That is, a proof system is specified by a verification procedure that takes as input any statement and a claimed “proof” that the statement is true, and decides whether or not the proof is valid. 
- Any true statement should have a convincing proof of its validity. This property is typically referred to as completeness.
- No false statement should have a convincing proof. This property is referred to as soundness.
- Ideally, the verification procedure will be “efficient”. Roughly, this means that simple statements should have short (convincing) proofs that can be checked quickly.
- Ideally, proving should be efficient too. Roughly, this means that simple statements should have short (convincing) proofs that can be found quickly. 					

## Argument Systems
Argument systems are IPs, but where the soundness guarantee need only hold against cheating provers that run in polynomial time. Argument systems make use of cryptography. Roughly speaking, in an argument system a cheating prover cannot trick the verifier into accepting a false statement unless it breaks some cryptosystem, and breaking the cryptosystem is assumed to require superpolynomial time. 					

## Multi-Prover Interactive Proofs
An MIP is like an IP, except that there are multiple provers, and these provers are assumed not to share information with each other regarding what challenges they receive from the verifier. A common analogy for MIPs is placing two or more criminal suspects in separate rooms before interrogating them, to see if they can keep their story straight. Law enforcement officers may be unsurprised to learn that the study of MIPs has lent theoretical justification to this practice. Specifically, the study of MIPs has revealed that if one locks the provers in separate rooms and then interrogates them separately, they can convince their interrogators of much more complicated statements than if they are questioned together. 	

## Probabilistically Checkable Proofs
In a PCP, the proof is static as in a traditional mathematical proof, but the verifier is only allowed to read a small number of (possibly randomly chosen) characters from the proof. This is in analogy to a lazy referee for a mathematical journal, who does not feel like painstakingly checking the proofs in a submitted paper for correctness. The PCP theorem essentially states that any traditional mathematical proof can be written in a format that enables this lazy reviewer to obtain a high degree of confidence in the validity of the proof by inspecting just a few words of it. 
