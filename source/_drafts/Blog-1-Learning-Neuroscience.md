---
title: Blog 1 - Learning Neuroscience
tags:
---

回想23年写的回答，刷着AI“驱逐”人类的新闻，笔者感觉AI与人类的冲突发展有些超越预期，似乎不远的未来，人类就要选择是被击败还是加入了。笔者一直认为，人类和AI应当结合，才能避免零和博弈。为了实现这点，应当渐进地实现人和AI在认知层面的耦合，比如通过一些基于LLM的认知增强产品，还能并行解决人和人之间沟通低效的问题。针对实现认知增幅的目标，笔者开展了学习和研究。

之前几乎没接触过神经科学，先跟GPT讨论一波。
<details>
<summary>提示词</summary>

## Research topic: Use LLMs as the accelerator and sub-processor of human brains
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

这样，该Problem就可以转化为一个任务分配优化问题，通过衡量任务准确度、完成时间、错误率、认知负载、记忆召回水平等评价。

之前完全没接触过神经科学？不慌，先跟GPT讨论一波，拿到了一批关键词:

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

根据上一次做素材库的经验，笔者先分离出最感兴趣的 BCI 相关文章，不过仍然很多，~5800 文章。接着，按年份分类出 22 年或更新年份的文章，再分离出 JCR Q1 的文章，就剩 460 篇了。文章数量虽然小了很多，但对于进一步细分 tag，影响不大。

第一个问题来了，笔者现在不是专业的，并不知道哪些 tag 是关键的、有分离度的。这样一方面容易挑出不应作为 tag 的词语作为分类，另一方面也可能因为选 tag 不专业，导致分类中的文章没法结合起来说明一类问题。想到也许要分类 2~3 次才能抓住一部分正确的分类逻辑，这个过程中肯定要精读不少摘要、原文，还不可避免要踩坑，笔者还是请了 AI 来整理 tag，prompt 如下：
·······


接着，先浅读了 feature extraction 的文章，似乎主要讲的是……。似乎不是那么 interesting
之后看到 speech-decoding，有意思。看过摘要后，选出 6 篇文章全文下载。其中 …… 的演示视频非常精彩。这些文章讨论如何通过电信号识别语言，同时还要分辨被测对象是打算发言还是自言自语。