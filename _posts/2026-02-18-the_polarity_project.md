---
title: "Polarity - History and Current Development"
date: 2026-02-18
categories: [Research, machine-learning]
tags: [AI-ML, Behavioural Analysis]
description: "How it started and where we're at now"
author: tr4ceang3l
comments: false
published: true
author: ashlynn
---

# What the project is
Polarity is a Machine Learning projected that had initially started out as a collection of small Python snippets of code dating back to my high-school laptop in my teenage years. It has since evolved into developed over the years into a C++ library for behavioural analysis, utilising Facial Emotion Recognition (FER), heart rate monitoring (rPPGA) for fear and stress statistics, and the implementation of an RNN to allow for live training and analysis of movement data to predict the target's next destination.

Throught the countless years of development of the project, I've gone through at least four different iterations -- three of which can be found on the historical [Github Repo: Ashthetik/Polarity-](https://github.com/Ashthetik/Polarity-) that date back to October 2023 -- until I settled on the final iteration that was designed to be an independent library for developers and researchers to utilise in their own products, or even develop off further in their own projects. I settled on making Polarity into a library for two pretty simple reason:

1. It was easier to distribute and implement into various projects
2. Designing the [Memory](https://mermaid.ai/play#pako:eNqFj09rwzAMxb-KyGk7-NAewyh0CdkugUB3S8vQZHUNJHbwn42x7LvPiU0DhTGdxHs_PUnfGWnJWZ6de_1JFzQOXsqjglDWv70bHC_wzDhGaa66pY88r9FBZXBgKNHhabUPTWtDDMvX0ZkH0so6SAO7hLGSRxXbqmmXlMZo6YlNIqpiE_UizPuBDeyvzvbGeTytYSDEbsK-14SO7QR1NOpZD5fdgKTHji2s507z4v-R7TWn2CyUYZRCq_4LkIjtujiwfwMROTSJOJP2ygkxwVPR3u290wO6jqDk9FCn1f3ya_bzCzd_i1U) and [Threading](https://mermaid.ai/play#pako:eNp1j89qwzAMh19F5JQe-gJmzSUjt0IOgV0KRthq4y2RPf8Zg7F3XxyHdrSbL8byp08_fVXKaqpEFeg9ESt6NnjxOJ8YluPQR6OMQ45wRMOAodzD6An1I9T1GekWAfXe6qTIP0JDZlp0MXn619ReRa3lkGbyddiduIA5w75pul5AiEtTvSv1rl-qQy5qIeJqrlUZJCdr3VWQH_AymonAJ2bDFwmHA0SfqAD5DNuIxSChaeCc09y-12H5f8Y3kmFET_pJfQhxxNjcYa0Ay3IV1IWULvotNbH-czHr7ve6ZYUzTmELM-y3lrIx0KeJ4Vdjlgp4tYazr_r-AWf5nnE) models became more consistent

Because of this, it made life significantly easier to progress the already slow and painful development of such an inherently sophisticated project. 

# What the goals are
At the time of writing this, my current goals for Polarity are to have a thread and memory safe solution to allow for scalable, reactive data flow without locking the performance directly to the game.

In future, Polarity will begin to provide better decision making capacities, planning logic, and eventually more sophisticated learning systems to extend off the current Execution Model design and bridge the gap into an Execution-Oriented Intelligence Model. The first step in bridging this gap is to implement a Goal-Oriented Action Planning (GOAP) model underneath the current data interpretation models (RNN, rPPGA, FER, etc.) to autonomously determine and acheive self-provided goals based off the context given by the environment; such goals should also be provided statically by the developers if they choose to do so.

# Current Progress
Currently, Polarity has achieved a -- relatively -- lightweight stance as a framework, which has been a long term goal for the project overall; and with the latest commit ([fc2a323777](https://codeberg.org/Ashthetik/Polarity/commit/fc2a323777e09bd5a8912918315067d86f81194b)), Polarity has given access to scalable, event-driven, cadence that can be ran isolated to the main game's thread, as well as handling next location prediction (via an RNN) within tolerable measures -- although further performance and fine-tuning will continue as development proceeds. 

Since Polarity no longer relies solely on the frame rates and ticks of the game, we can transform, replay, and analyse data with granualar throttling to the models. This basically means that we linearly scale to the hardware and reduce the amount of ticks required per frame, in fact, we make next to no impact on the TPS. Although we make minimal impact on the TPS, it should be cautioned that the FPS can potentially decrease for older systems, an issue currently in the works to fixing, as this requires heavily modifying core components of each backend agent and very close monitoring of execution times.

Current continued development will focus identifying and securing issues within the memory and threading of the project before moving onto the design stage for a GOAP model.

# potential future developments
Future developments are currently unpredictable, primarily due to personal situtations, but planned developments are the aforementioned GOAP, Learning Systems, and developing and designing a Voice Recognition system capable of analysing vocal data that correlates into emotions to assist the GOAP in making better situational decisions.

Help is always welcome, even if it's for correcting documentation, so if you're interested feel free to take a look at the current latest code here: https://codeberg.org/Ashthetik/Polarity

Thanks 🩶
