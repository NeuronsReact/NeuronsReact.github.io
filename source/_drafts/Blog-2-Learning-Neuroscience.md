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


## 产品技术路线设想

先考虑一个较为简单的产品。其“读取”使用者的意图，然后输入给外部智慧（比如 LLM），最后通过传统输出方式（屏幕、语音、动作等）输出给使用者，完成一次功能循环。产品工作流程可以分为“意图识别 - 解码 - 外部推理 - 以传统方式输出 - 使用者看到反馈”。

后两个环节借用现有成熟技术，因此我们重点关注前两个环节。由于外部推理使用 LLM，解码环节选择对言语进行解码。要实现意图识别和言语解码，先要解决用什么采集、采集哪里、如何分离有效内容（门控信号、算法）、如何与 LLM 集成，以及伦理问题。

## 用什么采集

意图识别、解码两步骤主要与脑机接口（Brain-machine interface，BMI）紧密相关。根据《神经科学基础》[\[1\]](https://users.ece.cmu.edu/~byronyu/papers/PNS-6thEdition-SectionV-Motor-Chapter39-BMIs.pdf) 第 39 章的定义，BMI 是一类在神经元尺度下测量和改变生物电活动的医学设备，可大致分为 4 种类型，分别是用于恢复感知能力、恢复运动能力、调节异常神经和化学活动、恢复大脑处理功能的 BMI。认知增强产品并不通过 BMI 恢复损伤的神经系统，仅仅借助其测量功能提供输入。

BMI 有几个技术维度：使用什么传感器、使用什么 scaling-up 方式、使用哪种方式供电和传输、使用什么监控分析系统。

#### 神经元传感器分类

传感器的作用是采集原始的神经电信号以供解码，最终获得使用者的意图和言语表达内容。根据采集位置、所采集信号的不同，分为以下类型：

| 神经元传感器类型 | 采集位置 | 收集什么样的信号 | 引用 |
|---|---|---|---|
| 脑电图 (electroencephalogram, EEG) | 皮肤表面放置电极片 | 一大片神经元的平均电信号 | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md); [Poeppel ch. 24](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch24-part12-mind-reading-privacy-cognitive-enhancement-bmi-neurorehab-aesthetics-music.md); [Banich ch. 3](C:/Users/Scott/.agents/skills/banich-cognitive-neuroscience/chapters/ch03-methods.md) |
| 皮质脑电图 (electrocorticography, ECoG) | 大脑皮层表面放置电极，比 EEG 到神经元的距离近得多 | 一小片神经元的平均电信号 | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md); [Poeppel ch. 15](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch15-part8-representational-models-feature-fallacy-mvpa-decoding-encoding-deep-learning.md) |
| 穿刺式皮质内电极 (penetrating intracortical electrodes) | 电极深入皮层表面以下 1~2mm | 非常小范围的神经元信号，可能对应单个神经元 | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md); [Poeppel ch. 11](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch11-part6-corticospinal-tract-bmi-somatosensory-control-cortical-reorganization.md) |




The skills also cover non-invasive **fMRI brain decoding** and **fNIRS** studies, but don’t present them as the main motor-control BMI sensors. fMRI is used to decode constrained stimulus or percept categories; fNIRS appears in infant and hyperscanning examples. **MEG**, measured with SQUID sensors, is another non-invasive brain-recording method, though the notes don’t identify it as a BMI controller. See [Gazzaniga ch. 6](C:/Users/Scott/.agents/skills/gazzaniga-cognitive-neuroscience/chapters/ch06-object-recognition.md), [Poeppel ch. 22](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch22-part11-social-threat-learning-value-guided-development-cooperation-interpersonal-neuroscience.md), and [Banich ch. 3](C:/Users/Scott/.agents/skills/banich-cognitive-neuroscience/chapters/ch03-methods.md).





### 意图识别

> 写现有的eeg，dcg，fMRI 技术的优劣。
> 可以发现现有的二分法、方向解码的准确度不高，因此可能需要设置一个确认环节，也许可以做成 pragmeta 那种简单解谜的操作

意图识别的主要功能是判断使用者是否想要调用该产品、识别在给产品发话还是在对别人讲话、检测输入结束和撤销之类的“动作”。

这方面已有一定研究，比如


### 解码（decoding）

解码的作用是将代表言语的神经电信号解读成要使用者要表达的内容，作为认知增强产品的输入。得益于植入式脑机接口和基于皮层脑电图的脑机接口的技术发展，这方面已有先进的临床研究 [\[1\]](10.1016/j.celrep.2026.117162) 做到了比较可用的解码速率。

【方向的选取依据，并不是挑最容易做出东西的，而是挑最薄弱的】和科研略微不同，从产品角度来讲，各项技术的现状都与最终产品的效果直接相关，因此资源要向薄弱的环节倾斜。
