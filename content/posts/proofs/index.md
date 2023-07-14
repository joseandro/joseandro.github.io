---
title: "What are zero knowledge proofs?"
date: 2023-07-13T01:30:03+00:00
weight: 1
aliases: ["/proofs"]
showToc: false
TocOpen: false
math: true
draft: false
hidemeta: false
comments: true
description: "Before the \"how\", here is an introduction to the \"what\" and the \"why\"."
disableHLJS: true # to disable highlightjs
disableShare: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: false
cover:
    image: "img/zk_snarks_intro.png" # image path/url
    alt: "An introduction to zero knowledge proofs" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---
My goal with this post is to explain zero knowledge proofs using first principles as I incrementally introduce more theory to make sense of it. Here I describe things in a way that most people with some basic comfort level with mathematics can comprehend. The reason why I decided to write about it comes from a personal struggle, most of the material I found available online was either too shallow or too heavy for me to understand zero knowledge proofs. Despite all my efforts to break everything down into chunks that are easier to digest, I must admit that it is a difficult topic built on a lot of theory, but I hope you stick to it. See you on the other side! 

# Proof models
Instead of jumping into the definition of zero knowledge proofs directly, we will start from the very beginning: let's think about how **proofs** are generally defined. There are a number of different ways for making proofs (i.e., proof models), but one that easily comes to mind is the model used for mathematical proofs of theorems. [Mathematical proofs](https://en.wikipedia.org/wiki/Mathematical_proof) can take many forms and use multiple strategies. For example, one can prove a theorem using [induction](https://en.wikipedia.org/wiki/Mathematical_induction), [contradiction](https://en.wikipedia.org/wiki/Proof_by_contradiction), [contraposition](https://en.wikipedia.org/wiki/Contraposition), etc. In essence, once the mathematical proof is written down, anyone can follow its instructions step-by-step to verify its validity. 

> What is intuitively required from a theorem-proving procedure? First, that it is possible to “prove” a true theorem. Second, that it is impossible to “prove” a false theorem. Third, that communicating the proof should be efficient, in the following sense. It does not matter how long must the prover compute during the proving process, but it is essential that the computation required from the verifier is easy.” [Goldwasser, Micali, Rackoff 1985](https://www.cs.princeton.edu/courses/archive/spr06/cos522/ip.pdf).

In the real world though, people prove to others that they know things by interacting with one another. The person who judges if a proof to a claim is true (i.e., the **Verifier**) normally asks questions to the person who is providing the proof (i.e., the **Prover**) until she is finally convinced. We generically refer to the Verifier and Prover roles as **actors**. In the real world model, the proof is the set of questions and answers, which the Verifier analyzes before she accepts or rejects. We will refer to this generic way of making proofs in real life as the **verifiable proof model**, where valid proofs only exist for true claims. Here is a depiction of this model:

![Verifiable proofs](img/verifiable_proofs.png#center)

This natural, albeit naïve, definition seems easier to implement as an algorithm than the more broad definition of mathematical proofs. This model also gives us an idea of how efficient computation through interaction can take shape: the Prover can bear the load of heavy computation while the Verifier can be tasked with simply checking if the proof is valid or not. Nevertheless, this real world model is still too loosely defined. It would be interesting to define it more formally using computer science theory. Then, perhaps we could show how powerful and efficient it is. For example, using a formal definition, we would know if it is possible to quickly prove to anyone that we know the answer to a game of chess! It would also be interesting to come up with a proof model that works well in untrusted environments, where any actor can cheat to gain personal advantage. Fortunately, this is an active area of research in computer science, the foundation on which zero-knowledge proofs are based on. To understand it, let's start by digging into complexity classes and the Big O notation.

# Complexity theory
[According to Wikipedia](https://en.wikipedia.org/wiki/Complexity_class), complexity classes split computation problems (e.g., **decision, search, counting, optimization, or function problems**) solvable by a specific model of computation (e.g., **Turing machines, interactive proof systems, boolean circuits, and quantum computers**) in resource-based (e.g., **time or memory/space**) requirements. I know, it is complex (pun not intended), let's approach it step-by-step:

## Computation problems
Decision problems simply are problems that expect a **yes** or **no** answer. The subset of inputs to which decision problems return an **yes** answer form together a formal **language** set. We only make proofs for problems with inputs that lead to a **yes** answer (i.e., inputs that are in the **language**). This fact is extremely important since valid proofs only exist for true claims. Furthermore, we always represent the input of computation problems with binary strings.

We will not drill down into the other problems here, but feel free to continue exploring [them](https://en.wikipedia.org/wiki/Computational_problem) if you are interested.

## Models of computation
Generally speaking, a model of computation describes the output of a computation from its inputs. Here I will describe a few models that will be important in our toolkit:

### Turing machines
The [Turing machine (\\(TM\\))](https://simple.wikipedia.org/wiki/Turing_machine) is one of the most popular models of computation, created by Sir Alan Turing himself! The theory behind a \\(TM\\) can get quite abstract and complex. Honestly, if you've never heard of \\(TM\\) before, don't bother. You can think of it as a very simple model of computation that can implement any algorithm, like a simple computer. The deterministic version of a \\(TM\\) simply states that the machine will always output the same answer (e.g., \\(1\\), \\(0\\) or `halt`) from the same inputs. The probabilistic version of a \\(TM\\) may or may not output a different answer from the same input. A deterministic \\(TM\\) can be turned into non-deterministic by using a [random number generator (or RNG)](https://en.wikipedia.org/wiki/Random_number_generation) to make decisions.

### Boolean circuits
Boolean circuits (or \\(BC\\)) are a generalization of Boolean functions (e.g., functions that output either \\(0\\) or \\(1\\), or `true` or `false`). Modern silicon chips used in computers started out implementing \\(BC\\), they still do. \\(BC\\) are defined using a composition of logic gates, such as `AND`, `OR`, and `NOT`. The \\(BC\\) model of computation is mathematically simpler than the \\(TM\\) model. We will not use \\(BC\\) here, but it is worth noting it because it will be used in future posts.

### Interactive proof systems
Interactive proof systems are at the core of zero knowledge proofs. Their importance is so significant that I have dedicated an entire section a few paragraphs down to thoroughly explain what they are. Roughly speaking, they shape the verifiable proof model into a model of computation. For now, just keep in mind that they are another kind of computation model. 

## Big O notation
Before we talk about complexity classes in more detail, I need to describe the [**Big O notation**](https://en.wikipedia.org/wiki/Big_O_notation). It is a mathematical notation used in computer science to describe the **run time** behavior of a program as the length of its inputs grow. You can think of it as the number of instructions an algorithm executes on a problem expressed as a function of its input. The Big O notation is an upper bound estimate, it classifies algorithms according to their growth rates, which may execute in time that is constant \\( O(1) \\), logarithmic \\( O(\log n) \\), quasilinear \\( O(n \log n) \\), polynomial \\( O(n^c) \\), factorial \\( O(n!) \\), etc, where \\( n \\) is the binary input to the algorithm. In computer science, [**we say an algorithm is efficient if it has polynomial run time**](https://medium.com/probably-approximately-correct/polynomial-time-and-efficient-algorithms-16481666827b). This is a complex topic that I described too briefly, so if you are not really familiar with it, I recommend you to watch [this well paced video](https://www.youtube.com/watch?v=Q_1M2JaijjQ) that will take you step-by-step through the whole story behind the Big O notation.

## Complexity classes
### \\(P\\) and \\(NP\\)
\\( P \\) and \\( NP \\) are two fundamental classes. Informally, they separate problems into *easy to solve* and *easy to verify*, respectively. More formally: 
- \\( P \\) is the class of **decision problems** to which the answer is **yes** and can be solved in **polynomial time** (i.e., can be solved efficiently) by a deterministic \\( TM \\) relative to the size of its inputs. 
- \\( NP \\) is the class of **decision problems** to which the answer is **yes** with a **certificate** that can be verified in **polynomial time** (i.e., can be verified efficiently) by a deterministic \\( TM \\) relative to the size of its inputs. The verification of a solution is done using the inputs to the problem (i.e., the binary string) and the solution **certificate** (or **witness**). As we will soon see, you can also think of the certificate as the proof! The witness certifies (i.e., proves) that the inputs to the problem are in the \\(NP\\)-language.
- \\( NPC \\) (or **\\(NP\\)-Complete**) is the class of the hardest problems in \\( NP \\). If you can solve an \\( NPC \\) problem you can solve any problems in \\( NP \\) using a technique called **reduction** or \\( NPC \\)-reducibility (keep this fact in mind, it will be useful later). Interestingly, if you can find a solution to an **\\(NP\\)-Complete** problem in polynomial time, you may also be solving one of the most notorious open problems in computer science and perhaps even get a [US$1 million prize](https://en.wikipedia.org/wiki/Millennium_Prize_Problems)💰🤑💰

If this is your first time reading about complexity classes, I recommend you to spend some time reflecting about them before continuing. Do you have a feeling that the definitions above might overlap each other? Well, think about it, a problem in \\( P \\) is also a problem in \\( NP \\): if you can solve a decision problem in polynomial time, during verification you can solve it again and you will be verifying the solution in polynomial time! In fact, this is how most computer scientists see the interplay between these complexity classes.
![P, NP, and NPC](img/p_np_npc.png#center)

If you want to know more about \\( P \ vs \ NP \\) without going into crazy loads of math to understand it, I recommend this great two videos: [*\\(P\\) vs. \\(NP\\) and the Computational Complexity Zoo*](https://www.youtube.com/watch?v=YX40hbAHx3s) and [*\\(P\\) vs. \\(NP\\) - The Biggest Unsolved Problem in Computer Science*](https://www.youtube.com/watch?v=EHp4FPyajKQ).

### Bounded-error probabilistic polynomial-time (BPP)
While both \\(P\\) and \\(NP\\) use a deterministic \\(TM\\), the \\( BPP \\) complexity class contains the languages that can be solved by a **probabilistic \\(TM\\)** running in polynomial time with a bounded error probability. In other words, the execution result will depend on the results of fair coin tosses with a very small (but quantifiable) probability of error. Probabilistic efficient \\(TM\\) are also called \\( PPT \\), probabilistic polynomial-time machines. 

In statistics, we use the "fair coin" metaphor to refer to any event that has only **two possible** outcomes with equal chances of happening (i.e., 50:50). If you count the results of **many** fair coin tosses, the odds of heads and tails will converge to 50:50. Furthermore, we also use the fair coin flip as a good source of randomness, as it is a physical event with many variables that are difficult to control and repeat: air speed and drag, force and angle of the flip, time to catch the coin in the air, etc. In practice, the fair coin can be implemented as an algorithm using **RNG**.

The \\( BPP \\) class in a summary:
- Is allowed to flip a fair coin to make random decisions. 
- Runs in polynomial time.
- Has a bounded probability of error. We arbitrarily use \\( \frac{1}{3} \\) but it doesn't really matter as long as it is strictly less than \\( \frac{1}{2} \\), as algorithms that make wrong decisions half of the time can be simply replaced with a fair coin.

Formalizing the error bound definition a bit more, for decision problems with:
- **Yes answers**: the input is in the \\( BPP \\) language, the \\( PPT \\) will **accept the valid input** with a probability greater than or equal to \\( \frac{2}{3} \\) and **reject the valid input** with probability less than or equal to \\( \frac{1}{3} \\).
- **No answers**: the input is not in the \\( BPP \\) language, the \\( PPT \\) will **accept the invalid input** with a probability less than or equal to \\( \frac{1}{3} \\) and **reject the invalid input** with probability greater than or equal to \\( \frac{2}{3} \\).

At first, it might seem counter-intuitive (and arguably, a bad decision) to allow errors, after all, \\( \frac{1}{3} \\) is still a high probability, but stick with me. In statistics, the resulting probability of multiple events happening on a row compounds with the of the odds of each event. So, we run the probabilistic algorithm multiple times to drive down the chances of error. Remember, the **right** or **wrong** outcome is being decided using a fair coin. Let's see how the odds of making wrong decisions stack up as we repeat the same probabilistic algorithm \\(k\\) times:
- \\(k = 1 \rightarrow \frac{1}{3} = 0.333 \\)
- \\(k = 2 \rightarrow \frac{1}{2}*\frac{1}{2} = \frac{1}{2^{2}} = 2^{-2} = \frac{1}{4} = 0.25\\)

The second time the \\( PPT \\) runs the probabilistic algorithm, the probability of consecutively making two wrong decisions goes down to \\( \frac{1}{4} \\), which is already below the \\( \frac{1}{3} \\) error bound. The odds of making consecutive wrong decisions is \\(2^{-k}\\):

- \\(k = 5  \rightarrow 2^{-5}  = 0.031\\)
- \\(k = 10 \rightarrow 2^{-10} = 0.00098\\)
- \\(k = 20 \rightarrow 2^{-20} = 0,00000095\\)
- \\(k = 30 \rightarrow 2^{-30} = 0.00000000093\\)

The catch in \\(BPP\\) is that we can drive the odds of error to as low as we want by re-running the problem on a \\(PPT\\) machine with the same inputs. In this different scenario, enabled by the probabilistic model of computation, how do we **accept** or **reject** a computation? One possibility is to decide at the end of all runs by the majority of the answers. However, it doesn't matter how much we drive down the error, a tiny probability will still exist regardless of how miniscule it will be. \\( BPP \\) is a great option if we are have enough capacity to run the model multiple times. The \\( BPP \\) class can model more realistic algorithms, give efficient approximations to computationally infeasible problems, provide better handling of errors and noise, and even analyze security in cryptography! It turns out that most problems in \\( BPP \\) can run quickly on a modern computer. The benefits of having a \\( PPT \\) machine are so great that we will later incorporate it into proof systems to make our proofs more practical in real life! This is fantastic! 🤯 

The complexity class \\( P \\), problems solvable in polynomial time using a deterministic machine, is contained in \\( BPP \\) (\\( P \subseteq BPP \\)) because deterministic machines are just a special case of probabilistic machines. Today is unknown if \\( BPP=P \\), since many problems that computer scientists believed to be in \\( BPP \\), but not in \\( P \\), are decreasing. 

It is worth trying to connect the dots on your own before we continue. With all the new tools in our toolbox, how would you come up with a model of computation, based on the verifiable proof model, that allows us to define proofs in an efficient and practical way? Using \\(NP\\), maybe? I have introduced a lot of key ingredients that we will now use to define interactive proof systems, let's dig into it.

# Interactive proof systems
The verifiable proof model I introduced in the beginning of this post was, intentionally, loosely defined. In computer science's realm we need to better define this interaction. It is useful to model new discoveries in a way that we can tap into a larger pool of proven fundamental theory. Fortunately, we have all the theory we need under our belts to expand our proof model with new ingredients that will help us with decision problems of different complexity classes! 

We started our journey by modeling real world proofs using the interaction between two actors. Interestingly, until very recently there was no formal definition of computation through interaction. It was only in 1985 that two independent groups of researchers formally defined that concept using complexity theory. Babai defined the [**Arthur–Merlin (AM)** complexity class](https://dl.acm.org/doi/10.1145/22145.22192) and Goldwasser, Micali, and Rackoff defined the [**interactive proofs (IP)** complexity class](https://dl.acm.org/doi/10.1145/22145.22178). Many other complexity classes that defined different flavours of proof systems unfolded from their seminal work. 

To understand what the interactive proof system computational model is, let's begin start with the verifiable proof model. We will not worry about the Prover time here (i.e., it has unbounded computational power). We will define the new proof model's efficiency in terms of the Verifier's run time and the size of the proof (i.e., certificate). For this to be possible, both the Verifier and the size of the proof itself must be **efficient**. You may be wondering: *wait, so now we are also defining efficiency for storage space?* Yes, it turns out that we can reuse the definition of time efficiency to define the space efficiency. We will refer to this property as succinctness. Therefore, the new version of our model will have the requirement that the Verifier has to run in, at most, polynomial time and the witness must be, at most, polynomial in size, all of which should be relative to the size of the input (i.e., keep in mind that the input here is a binary string). The requirements for efficiency are what we needed to start formally defining the model of computation known as the **interactive proof system**! 

We say that, all [**interactive proofs systems**](http://people.seas.harvard.edu/~salil/cs121/fall12/lecnotes/InteractiveProofs.pdf) must have the following characteristics:
- **Completeness**: a true **claim** has **proof** that is short in size (i.e., succinct) and is **always accepted** by the Verifier. This characteristic ensures honest Provers will always convince the Verifier.
- **Soundness**: a false **theorem** has no proofs. The Verifier will **always reject** an invalid proof. This characteristic ensures that even a cheating Prover will not be able to convince the Verifier of an invalid proof, regardless of what she tries to do.

Here, the actors are allowed to exchange a pre-determined number of messages. These messages are also known as **proof steps** (i.e., they all refer to the same proof). In this new way, the Verifier is no longer a passive actor and engages with the Prover by sending her a pre-defined **k** number of messages, where **k** itself also must be efficient with respect to the input length. After the **k-rounds** of interactions ends, the Verifier analyses all the answers the has Prover provided to her queries (also known as the interaction **transcript**) to accept or reject the proof. The transcript is the proof.

Due to the definitions and requirements above, **all** interactive proof systems are capable of efficiently verifying **\\(NP\\)-languages**, a fantastic feat on its own! If you can not convince yourself this is true, we will explain it in the next section.

Most of the different complexity classes enabled by this model of computation play with the following ingredients:
- **Randomness**: actors can either act deterministically or randomly. 
- **Interactions**: actors can exchange a pre-defined number of messages, or proof steps.

Notice that these ingredients all refer to the behavior of actors within this computational model. 

Now that we know what interactive proof systems are all about, we will incrementally tweak the ingredients above to analyze what class of problems they can handle. We will move step by step until we can use it to zero-knowledge proofs!

## \\(NP\\) (revisited)
With a fresh model of computation under our sleeves, we can now revisit the [\\(NP\\) complexity class](#complexity-classes). Before you continue reading though, I recommend you to pause and think about it for a minute: how would you re-define the \\(NP\\) complexity class using interactive proof systems?

Well, it turns out that the \\(NP\\) complexity class may be viewed as the most simplistic interactive proof system we can think of! In this system, the actors are deterministic (i.e., they can not act randomly) and the number of interactions is limited to 1 (i.e., the witness itself). The Prover computes the certificate of polynomial size in an unbounded time (i.e., we don't care how long it takes). The Verifier is a P machine that checks that the proof is valid in polynomial time.

![Interactive proof system](img/eff_v_p.png#center)

This complexity class fills out the necessary characteristics of interactive proofs:
- **Completeness**: when a proof exists, its input is in the \\(NP\\)-language, and the Prover is always capable of making the Verifier accept it.
- **Soundness**: when a valid \\(NP\\)-proof does not exist (e.g., an \\(NP\\) decision problem with a **no** answer), its inputs are **NOT** in the \\(NP\\)-language. Therefore, no Prover, regardless of how much effort she puts into cheating, can convince the Verifier to accept it.

Next, we will see where we can get to by adding one more ingredient to this model: more rounds of interactions!

## Deterministic interactive proofs (dIP)
The **deterministic interactive proofs (\\(dIP\\))** complexity class contains all languages with an interactive proof system with **\\(k(n)\\)** rounds of deterministic interactions, where **\\(k\\)** is a polynomial function in the input size determining the number of interactions (so that it interacts efficiently too). Here, the Verifier continues to be a **deterministic** \\(TM\\) as in \\(NP\\).

![Deterministic interactive proof system](img/dIP.png#center)

To make it easier to classify messages, we refer to the messages coming from the Verifier to the Prover as queries `q` and messages coming the other way as responses or answers `a`. The entire set of answers and questions is known as **transcript**. The transcript accepted by the Verifier is the witness that the input is in the language.

We say that \\(dIP=NP\\) because they are the same proof system when \\(k = 1\\), it is believed that the added interaction doesn't make it more powerful than \\(NP\\). Since all interactions must be verified correctly. Completeness and Soundness properties hold in the same it was described in \\(NP\\). Furthermore, the multiple rounds of interactions we added here don't really help: what is the point of this whole interaction if the Verifier is always going to act deterministically? The Prover could simply send all values at once! Models with deterministic verifiers really just need a single round of interaction.

In the \\( BPP \\) class definition, we saw the power of non-deterministic TMs and how they unlock the classification of the complexity of multiple real life applications with a small error probability. It turns out that, for interactions to provide any benefit in this model of computation, the Verifier has to act probabilistically! In the next sections, we will explore what nice properties lie in probabilistic interactive proof systems.

## Probabilistic interactive proofs (IP)
The \\( dIP \\) proof system introduced interactivity. It expanded \\( NP \\) with \\( k(n) \\) rounds of interaction, but it is not really better than the old and good \\( NP \\). To make \\( dIP \\) probabilistic, we will allow the Verifier to ask some random questions. The Verifier flips coins to decide what questions to ask, the probability is defined over these flips. In \\(IP\\), all flips are private (known as the private coin model), only the Verifier knows them, while in \\( AM \\) they are public (known as the public coin model). This way, we will make much harder for an untrusted Prover to cheat! Which computation model we introduced earlier we could aggregate here to make this possible? Did those coin flips ring a bell? You guessed it correctly, a non-deterministic \\( TM \\)! This way, the Verifier's questions can be modeled with a probabilistic algorithm. This small change will take us much further than the proof system in \\( dIP \\) did, it will move us in the direction that the \\( BPP \\) complexity class did. Some literatures refer to the \\( IP \\) class as \\( IP[k] \\) to show more explicitly that there are k-rounds of interactions in it, but the terminologies are the same.

Before I introduce \\( IP \\) more formally, let's start with an intuitive example which will demonstrate how powerful a system with a non-deterministic Verifier could be in a more "real" situation. Alan wants to prove to his color-blind friend, Brandon, that his two socks, green and red, have different colors. Like in proof systems, Alan is the Prover and Brandon, the Verifier. In this scenario, the Verifier, being color-blind, has fewer capabilities (i.e., less power) than the Prover, who can see colors. Similarly, in interactive proof systems, the Verifier, upper bounded by polynomial time, has less computing power than the Prover. How can Alan prove his socks have different colors using probability and interaction?
One way is for Alan to provide both socks to Brandon, telling which sock is which. Brandon, holding the green sock on the right hand and the red one on the left, asks Alan to close his eyes. Next, Brandon flips a coin to decide which action to take. If the coin flips tails, Brandon switches socks between hands, if it comes "heads", Brandon does not take any action and the socks remain where they are. Brandon then asks Alan to open his eyes and tell if he switched socks. Here's the catch, if the socks were the same color and Alan tried to cheat saying they were not, he would need to make a guess between green and red. Since there only two options available (Brandon either switched the socks or not), Alan would have \\(\frac{1}{2}\\) chance of answering it correctly. If they keep repeating this interaction, say 100 times, and Alan gets all answers correctly, Brandon will be convinced that the colors are, in fact, different. A similar calculation was done for the \\( BPP \\) class.

Let's now look into the formal definition of the \\( IP \\) class. The \\( BPP \\) class used a non-deterministic \\(TM\\) to compute answers. When there's probability involved in the game, the Verifier (our probabilistic \\(TM\\)) needs to be allowed to, with very small probability, come to wrong conclusions (e.g., accepting a false proof or rejecting a valid one). So, we can't demand perfect completeness and soundness, or else we will fallback to \\( dIP \\). Therefore, instead of requiring that the Verifier always accept valid certificates and reject invalid certificates, we have the following as:
- **Completeness**: if the string is in the language, the Verifier will accept the certificate with probability \\( \geq \frac{2}{3} \\).
- **Soundness**: if the string is not in the language, no prover, however malicious, will be able to convince the Verifier to accept the string, except with probability \\( \leq \frac{1}{3} \\) in the length of the input.

All these probabilities are over the coin flips. As in \\( BPP \\), the Verifier here is our non-deterministic \\(TM\\) bounded by \\( PPT \\). Similarly to the other proof systems we have seen so far, here the Prover will also be able to convince the Verifier of correct theorems and unable to convince of incorrect ones, but now probabilities are involved. Like in \\( BPP \\), the soundness probability constant can be made so small that the Prover will only be able to cheat the Verifier once in a lifetime. This extremely rare event is a great cost to pay for all the benefits the \\(IP\\) class introduces.

![The IP class](img/IP.png#center)

Today, \\(IP\\) is also defined using perfect completeness (probability \\(=1\\)) and soundness using negligible probability. Proving perfect completeness is harder than proving completeness with some chances of error. If you're interested in proving completeness and soundness for \\( IP \\) on your own, you can take a similar approach used to boost \\( BPP \\) probabilities: start by assuming that completeness holds more than \\(\frac{2}{3}\\) and soundness doesn't at most \\(\frac{1}{3}\\) of the time, then use *boosting* (the name of the technique) to get them close to \\( 1 \\) or \\( 0 \\). Perfect completeness and \\(\frac{2}{3}\\) completeness don't really change the \\(IP\\) class. However, we will fall back to the \\( dIP \\) class if we demand perfect soundness, which will reduce the class \\( IP \\) to \\( NP \\), similar to having a deterministic Verifier in the model. This is how the model looks like with perfect completeness:

![The IP class with perfect completeness](img/perfect_IP.png#center)

All languages in \\( NP \\) have \\( IP \\) proofs, so we can say that they are at least equivalent, but some other languages not known to be in \\( NP \\) or \\( NPC \\), such a [graph non-isomorphism (or \\( \overline{ISO} \\))](https://people.csail.mit.edu/ronitt/COURSE/F17/NOTES/lec6-scribe.pdf), are in \\( IP \\). It's believed that \\( NP \subseteq IP \\), in fact it was proven that \\( IP = PSPACE \\). The following image was extracted from [this article in Wikipedia](https://en.wikipedia.org/wiki/PSPACE) and represents the final class hierarchy we were looking for. 

![All complexity classes](img/complexity_classes.png#center)

To understand how \\( \overline{ISO} \\) is an \\( IP \\) language, you need to understand graphs first. If you are unfamiliar with them or if you need a refresher on graph theory, I recommend you to check out [this short video](https://www.youtube.com/watch?v=LFKZLXVO-Dg). Next, I also recommend you to check out [this chunk of the video](https://youtu.be/TSI3LR5WZmo?t=169) for the proof that \\( \overline{ISO} \notin NP\\) and [this part of the video](https://youtu.be/TSI3LR5WZmo?t=1598) for the proof that \\( \overline{ISO} \in IP \\). These examples are important, be sure to understand these proofs as I will use them later to contrast \\( IP \\) and \\( AM \\)  and illustrate zero-knowledge interactive proofs.

## Arthur Merlin (AM)
As I briefly mentioned before, the Verifier in \\( IP[k] \\) proof system uses private coins that aren't shared with the Prover. \\( AM \\), on the other hand, uses public coins where the Prover can see all the coin flips. The \\( AM \\) class is also called \\( AM[2] \\). The most evident differences between \\( AM[2] \\) and \\( IP[k] \\) is that \\( AM \\) is only defined for 2 interactions and produces proofs with public coin.

In \\( AM \\), Arthur is the Verifier and Merlin the Prover. Their interaction is defined as the following:
1. Arthur flips the coin and sends the result of all tosses (i.e., a random string) to Merlin, because all coin tosses are public.
2. Merlin creates a proof and sends it back (i.e., responds) to Arthur. 
3. Arthur deterministically determines if the proof is valid using the proof and the result of his coin tosses.

Interestingly, the literature adds more letters to the original \\( AM \\) class to refer to more rounds of this protocol. For example: \\( AM[3] \\) is also known as \\( AMA \\) and \\( AM[4] \\) as \\( AMAM \\). There also exists the class \\( MA \\), where Merlin starts the interaction by sending the proof directly to Arthur. Arthur then deterministically decides using the inputs, proof, and his coin flips.

It's important to highlight that in \\( AM \\), the coin flips are revealed to the Prover step by step after each new message from the Verifier. It's proven that for every \\(k, \ AM[k] \subseteq IP[k] \\), can you see why? If you watched the video that shows the proof for \\( \overline{ISO} \in IP \\), you probably remember that for it to work, the Prover can not see the coin flip. If the Prover knew the result of the flip she could easily guess the graph permutation correctly. Therefore, keeping the coins private adds more power to interactive proofs systems with the same number of interactions. However, you can still make public coin proof systems as powerful as private ones by adding more rounds of interactions to it, but I will not dive into the details here. Next, we will dive into the cherry on top of the private interactive proofs cake, \\(ZKP\\)!

## Zero-knowledge interactive proofs (ZKP)
One of the key motivations for the creation of interactive proofs was to obtain zero-knowledge proofs. In a very informal way, we can say that the main idea behind zero-knowledge proofs is ["to prove that I **could** prove something if I felt like it"](https://zk-learning.org/assets/Lecture1-2023-slides.pdf). The key here is that you are going to be proving that you **could prove a claim** without sharing your knowledge with the Verifier. The idea of proving that you have a valid password for a safe without revealing the password to someone else is mind blowing. 

\\( ZKP \\) are built on top of \\( IP \\) : it *simply* adds the *zero-knowledge* ingredient to it. Informally, the zero-knowledge property can be framed as a requiring that the Verifier learns nothing from the interaction with the Prover, except the information to conclude that the claim is true. Due to the complexity introduced by the zero-knowledge ingredient, it's very difficult, although not impossible, to come up with \\( ZKP \\) for languages outside of \\( NP \\), so here is the formal characterization of \\( ZKP \\) of languages in \\( NP \\):

- **Completeness**: if the string is in the language, the Verifier will accept the certificate with probability \\( \geq \frac{2}{3} \\).
- **Soundness**: if the string is not in the language, no Prover, with whichever strategy \\(P^*\\), will be able to convince the Verifier to accept the string, except with probability \\( \leq \frac{1}{3} \\) in the length of the input.
- **Zero Knowledge**: for any interactive strategy \\( V^* \\) (honest or evil) the Verifier decides to use, there exists a \\( PPT \\) Simulator algorithm \\( S^* \\) with no access to the proof that can simulate the result of the interaction between Prover and \\( V^* \\).

Nowadays it is common to find the characteristics above defined using **perfect completeness** and **soundness** with negligible probability. However, as explained in \\(IP\\), you can work on the definitions above to get equal results. The use of the \\( V^* \\) strategy is to ensure that not even malicious Verifiers can't cheat to gain new knowledge from the interaction with the Prover.

After the interaction ends, the Verifier learns that the Claim is true and a **view** of the interaction (interaction transcript + the coins flipped by the Verifier). The coin flips generates a probability distribution, which encompasses all the assignments to the variables involved in the view. This distribution is also called the **real distribution**. Nonetheless, the \\( PPT \\) algorithm \\( S^* \\) also has a probability distribution called the **simulated distribution**.

There are three different flavours of zero knowledge:
1. **Computational zero knowledge (CZK)**: when the real and simulated distributions are computationally indistinguishable. 
2. **Perfect zero knowledge (PZK)**: when the real and simulated distributions are completely identical. 
3. **Statistical zero knowledge (SZK)**: when the real and simulated distributions are statistically close to each other. It is believed that this class lies in between \\( P \\) and \\( NP \\).

For an example of \\( PZK \\) using graph isomorphism, go checkout [this video](https://youtu.be/LU4GTwII9Ug?t=878) lectured by the great Shafi Goldwasser herself.

In 1986, [Goldreich et al. demonstrated that](https://www.math.ias.edu/~avi/PUBLICATIONS/MYPAPERS/GMW86/GMW86.pdf), if perfect one-way hash functions really exist, then all of \\( NP \\) has a \\( CZK \\) proof! First, the authors proved that the [3-coloring problem](https://en.wikipedia.org/wiki/Graph_coloring), a well known \\( NPC \\) problem, had a \\( ZKP \\). Next, employing a technique called \\( NPC \\) reduction, they showed that all languages in \\( NP \\) have a \\( CZK \\). The key ingredient here was the use of a cryptographic primitive called **bit commitment scheme** (i.e., a one-way function implemented in software). Essentially, a bit commitment scheme is the digital analog of a sealed envelope, it involves two protocols:
* **Commit(m)**: the sender takes the bit \\( m \\) and puts it in a metaphorical opaque envelope and seals it. No one, but the sender, can tell what's in the envelope because it's opaque. You can also think of this protocol as the sender creating a hash value \\( h \\) through the  encryption the bit \\( m \\).
* **Decommit**: the sender opens the envelope to reveal the bit \\( m \\) to everyone. You can also think of this protocol as the sender decrypting the hash \\( h \\) to get the bit \\( m \\).

![Commitment scheme](img/commitment_scheme.png#center)
There are two important properties that commitment schemes satisfy:  
* **Hiding**: no one can tell what the committed value is with probability bigger than \\( \frac{1}{2} \\), when the message is a bit. The Receiver can only see the opaque envelope, not the message \\( m \\) inside of it.
* **Binding**: whenever the value that gets committed is also the value that will get decommitted. If the sender commits to \\(1\\) he will be bound to reveal \\(1\\) when he opens the envelope.

The catch is that **Commit(m)** is computationally indistinguishable from **Commit(n)**! I am not going to drill down into the details here, but in essence, this is why commitment schemes are used to create computational zero-knowledge proofs! This was a groundbreaking idea that is used nowadays in \\( CZK \\) proof schemes such as **SNARKs** and **STARKs**. 

Until very recently, \\( ZKP \\) were theoretical and not practical to implement. This all changed in the last 10 years with the creation of new systems such as Pinnochio, Gepetto, Ligero, Groth16, PLONK, etc. You can read more about these protocols [here](https://en.wikipedia.org/wiki/Zero-knowledge_proof#Zero-Knowledge_Proof_protocols).

We have reached the end of this long first sprint! This introduction gave you some tools to understand the "what" and the "why" for \\( ZKP \\) to be designed the way they are. In the next post we will go over the different designs of SNARKs systems. Até breve!
