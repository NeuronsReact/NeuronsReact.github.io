---
title: 神经科学学习 —— Step 2：建立教材知识库和感兴趣的知识点列表
tags:
  - Neuroscience
categories:
  - Academic
  - Neuroscience
date: 2026-10-13
updated: 2026-10-13
mathjax: true
---

书接上篇([神经科学学习 —— Step 1：建立基础素材库]({% post_path Blog-1-Learning-Neuroscience %}))，尽管已经有了论文素材库，但在知识基础方面，只有论文素材库是不够的，还需要整个领域的深厚知识；在研究目标方面，只有基础没有方向也是不行的，要梳理 problem B & C 涉及的研究、技术方向，以及其进展、成熟度、瓶颈点。

<!-- more -->

## 教材知识库
知乎上有一份入门神经科学的书单：[入门 | 学习神经科学用什么教材？ - 凝眸 NeuroMoment的文章 - 知乎](https://zhuanlan.zhihu.com/p/271676465) 。选 10 本教材，用`book-to-skill`转成了 skill。选择的书目如下：

| Book |
|------|
| Purves et al., *Neuroscience* |
| Kandel et al., *Principles of Neural Science* |
| Nicholls/Martin et al., *From Neuron to Brain* | 
| Luo, *Principles of Neurobiology* |
| Gerstner et al., *Neuronal Dynamics* |
| Dayan & Abbott, *Theoretical Neuroscience* |
| Arbib & Bonaiuto, *From Neuron to Cognition via Computational Neuroscience* | 
| Banich & Compton, *Cognitive Neuroscience* |
| Gazzaniga, Ivry & Mangun, *Cognitive Neuroscience* |
| Poeppel, Mangun & Gazzaniga, *The Cognitive Neurosciences* |


## 应用教材知识库

接下来让 AI 生成设计认知增强产品涉及的知识点。产品工作流程可以分为“意图识别 - 解码 - 外部推理 - 以传统方式输出 - 使用者看到反馈”。

### 意图识别

> 写现有的eeg，dcg，fMRI 技术的优劣。
> 可以发现现有的二分法、方向解码的准确度不高，因此可能需要设置一个确认环节，也许可以做成 pragmeta 那种简单解谜的操作

意图识别的主要功能是判断使用者是否想要调用该产品、识别在给产品发话还是在对别人讲话、检测输入结束和撤销之类的“动作”。

这方面已有一定研究，比如


### 解码（decoding）

解码的作用是将代表言语的神经电信号解读成要使用者要表达的内容，作为认知增强产品的输入。得益于植入式脑机接口和基于皮层脑电图的脑机接口的技术发展，这方面已有先进的临床研究 [\[1\]](10.1016/j.celrep.2026.117162) 做到了比较可用的解码速率。

【方向的选取依据，并不是挑最容易做出东西的，而是挑最薄弱的】和科研略微不同，从产品角度来讲，各项技术的现状都与最终产品的效果直接相关，因此资源要向薄弱的环节倾斜。