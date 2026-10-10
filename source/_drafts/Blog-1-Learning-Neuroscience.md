---
title: Blog 1 - Learning Neuroscience
tags:
---

回想23年写的回答，刷着AI“驱逐”人类的新闻，笔者感觉AI与人类的冲突发展有些超越预期，似乎不远的未来，人类就要选择是被击败还是加入了。笔者一直认为，人类和AI应当结合，才能避免零和博弈。为了实现这点，应当渐进地实现人和AI在认知层面的耦合，比如通过一些基于LLM的认知增强产品，还能并行解决人和人之间沟通低效的问题。针对实现认知增幅的目标，笔者开展了学习和研究。


之前几乎没接触过神经科学，先跟GPT讨论一轮。

<details>
<summary>提示词</summary>

Research topic: Use LLMs as the accelerator and sub-processor of human brains
Need: Tranform this concept to one or several scientific problems or research problems.
Problems:
1. I have a master degree of mechanics, but I don't know much about brain science. I need to understand brain science and its sub-fields, to get to know where to start.
2. I don't know the research progress about connecting computers to human brains.

</details>

GPT把这个topic转化成了4个主要的Problem：

### Problem A — Cognitive offloading
人类借助外部认知资源完成任务，比如笔记、计算器、地图、计算机软件。这在认知神经学中叫作“认知卸载”。实验研究表明，认知卸载可以显著提升任务表现，但也会建立对外部认知资源的依赖。人利用LLM进行认知卸载的关系式：
$$
\text{Human}
\rightleftharpoons
\text{LLM}
$$

用提问句表达这个问题则是：

What computational functions should remain inside the biological cognitive system, and what functions should be delegated to an LLM?

这样，该 Problem 就可以转化为一个任务分配优化问题，通过衡量任务准确度、完成时间、错误率、认知负载、记忆召回水平等评价。

### Problem B — The brain–LLM interface
这一层次变得有趣起来。brain-LLM interface 和 brain-machine interface（BMI）是很接近的。BMI 的结构是：

$$
\text{Brain}
\rightarrow
\text{Sensor}
\rightarrow
\text{Decoder}
\rightarrow
\text{Computer}
$$

而 brain-LLM 接口的结构是：

$$
\text{Brain}
\rightarrow
\text{Sensor}
\rightarrow
\text{Decoder}
\rightarrow
\text{LLM}
\rightarrow
\text{External computation/action}
$$

这项改变的价值在于，人与 LLM 沟通的成本将会更低。以往还需要打字、语音来进行输入，结合 BMI 之后就可以做到“想到什么，就给 LLM 输入什么”。这也引出更加严肃的研究问题：

**大脑中哪些信息要暴露给 LLM？**

虽说暴露语言是相对比较好实现功能的。不过注意到，这些信息并非必须是语言。同样一件事情，不同的语言都能表达出来，因此一定有一些信息是语言无关的。这些信息也可以是：

* motor intention
* semantic intention
* working-memory state
* attention
* uncertainty
* goal
* visual information
* task state
* emotional/arousal state
* latent conceptual representation

在这个 problem 中，decoding 和 computational neuroscience 就变得非常有用了。

不过，该 problem 的目标远非完全体。以上改进并未涉及 encoding，也就是输出仍然使用传统方式，通过声音、视觉、触觉等信号反馈给人体。

### Problem C — LLM as a cognitive co-processor



## 建立素材库

根据以上 4 个 problem，GPT 也给出了一批关键词。直接构建 WOS 检索表达式:

<details>
<summary>keyword</summary>

TS=(
  "brain-computer interface"
  OR "brain-machine interface"
  OR "neural interface"
  OR neurotechnology
  OR neuroengineering
  OR "neural prosthesis"
  OR neuroprosthesis
  OR "neural prosthetic system"
  OR "bidirectional brain-computer interface"
  OR "closed-loop BCI"
  OR "bidirectional neural interface"
  OR "brain-computer interaction"
  OR NeuroAI
  OR "brain-inspired AI"
  OR "AI-augmented cognition"
  OR "AI cognitive augmentation"
  OR "neural augmentation"
  OR "cognitive augmentation"
  OR "cognitive prosthesis"
  OR "cognitive neuroprosthesis"
  OR "neural co-processor"
  OR "brain co-processor"
  OR "artificial cognitive prosthesis"
)

</details>

25000+ results，很好，批量导入 zotero。根据“鲸吞法”文献调研的思路，要给这些文献打 tag，之后根据 tag 分组，再挑出值得下全文的文献。

根据上一次做素材库的经验，笔者先分离出最感兴趣的 BCI 相关文章，不过仍然很多，~5800 文章。改变方法，先按年份分类出 22 年或更新年份的文章，再分离出 JCR Q1 的文章，就剩 460 篇了。文章数量虽然小了很多，但对于进一步细分 tag，影响不大。

![Fig1. 22年或更新的Q1文章](Blog-1-Learning-Neuroscience-Figure1.png){width=1200px}

<details>
<summary>弯路</summary>
按年份分类出 22 年或更新年份的文章，有 1663 篇。此时进行了第一次的粗分类，分为了 10 个粗分类，但许多文章都有多个标签，导致一个 tag 仍有数百篇文章。分类失败。之后回忆以前做素材库的过程，里面有一步是根据目标期刊分类。基于此思路，先只选出 JCR Q1 的文章（并不是唯 Q1 独尊的意思，只是从理解该领域的角度讲，这样更有效），再按 idea、field分类。
</details>

为了减少一些 tag 整理前文献阅读的精力和踩坑的数量，采用 AI 来整理 tag，prompt 如下：
·······

tag 整理好了。这个“hybrid-bci”看起来非常 juicy，我倒要看看有多 hybrid、怎么 hybrid。其中一篇文章的 Abstract 写得很清楚：hBCI, is a device formed by the amalgamation of control signal and bio-signal combination of control signal with one or more bio-signals that enhances system performance and usability. (Amalgamation / Combination：结合 / 融合; Usability：易用性 / 可用性)



接着，先浅读了 feature extraction 的文章，似乎主要讲的是……。似乎不是那么 interesting
之后看到 speech-decoding，有意思。看过摘要后，选出 6 篇文章全文下载。其中 …… 的演示视频非常精彩。这些文章讨论如何通过电信号识别语言，同时还要分辨被测对象是打算发言还是自言自语。