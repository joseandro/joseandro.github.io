---
title: "Welcome!"
date: 2023-06-01T11:30:03+00:00
weight: 1
aliases: ["/welcome"]
showToc: false
TocOpen: false
math: true
draft: false
hidemeta: false
comments: true
description: "Wondering where to start with zk-cryptography?"
disableHLJS: true # to disable highlightjs
disableShare: false
hideSummary: true
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "img/welcome.jpg" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

Back when I was an undergrad computer science student, I had a hard time understanding its fundamental concepts, but when things finally clicked, I found very rewarding to understand how theory connected to practice. I started to see the beauty in [automata theory](https://en.wikipedia.org/wiki/Automata_theory), the cleverness behind the simplicity of [Cook's theorem](https://en.wikipedia.org/wiki/Cook%E2%80%93Levin_theorem), and the possible real world repercussions behind the [P vs NP](https://en.wikipedia.org/wiki/P_versus_NP_problem) problem. 

I recently had a similar experience learning zero knowledge cryptography (aka **zk-cryptography**). The learning journey was far from being simple or a straight line. Unfortunately, it was difficult for me to ramp up because I didn't have enough foundation knowledge to understand the papers I read or some of the tutorials I found online. This is fair, **this stuff is hard**, so hard that until recently it was known as ["moon math"](https://vitalik.ca/general/2021/01/26/snarks.html). To be honest, after ramping up in zk-cryptography I realized that just a few people in our industry **really** understand it. So, I wanted to start writing about it to share what I learned (and I still am!) and some of the foundation that I was missing, with the goal of (hopefully 🤞) making it more accessible to anyone with enough determination to learn it is and how things work under the hood.

![You can do it!](img/math.png#center)

Don’t beat yourself if you don’t understand things the first time you read it, stay strong at it! You will probably have to read the same paragraph or rewatch a lecture multiple times to understand a concept, that's OK, that is expected. In all honesty, when I first started studying this topic I felt lost, many things sounded innocuous to me. To really understand and make sense of what zero-knowledge proofs (aka **ZKPs**) were, I needed to take some steps back and learn about its underlying fundamental concepts first. Now, I humbly hope to help others to learn it too. The path involves computer science, mathematics, statistics, and cryptography, but don't worry! We will walk it together from first principles, building a solid foundation as I introduce the math and theory needed for a good understanding of ZKPs. My goal is to write a series of posts, where I will work with you through these concepts and progressively reach the goal of defining zero-knowledge cryptography in a way that will actually make sense.

My goal is that as we explore what zk-cryptography is, you will start acquiring the knowledge you need to understand and appreciate how proof-systems work under the hood. Hopefully, as you learn more about it, you will also see the beauty in it. Start with the [first]({{< ref "/posts/proofs" >}}) post in this series to understand what zero knowledge proof is, I hope you enjoy it! 



