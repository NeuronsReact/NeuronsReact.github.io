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

我们优先解决前两个环节。这两个环节主要用到 BMI (Brain-machine interface) 技术。

## Brain-machine interface
脑机接口（Brain-machine interface，BMI）是一种在神经元尺度下测量和改变生物电活动的医学设备。根据《神经科学基础》[\[1\]](https://users.ece.cmu.edu/~byronyu/papers/PNS-6thEdition-SectionV-Motor-Chapter39-BMIs.pdf) 第 39 章，BMI 可大致分为 4 种类型，分别是用于恢复感知能力、恢复运动能力、调节异常神经和化学活动、恢复大脑处理功能的 BMI。

认知增强产品所需的 BMI 的设计功能并非恢复损伤的神经系统功能，而是仅读取神经电活动，提高整体认知系统的性能，所以不属于以上的分类，但技术方面是共同的。几种 BMI 技术的具体形式、特点和优劣汇总如下：

| 技术路线 | 硬件 | 收集什么样的信号 | Where it appears |
|---|---|---|---|
| **Non-invasive EEG BCI** | Scalp cap with disk electrodes; high-density EEG systems are also mentioned. EEG-based motor-imagery control is used in the Walk Again neurorehabilitation example. | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md); [Poeppel ch. 24](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch24-part12-mind-reading-privacy-cognitive-enhancement-bmi-neurorehab-aesthetics-music.md); [Banich ch. 3](C:/Users/Scott/.agents/skills/banich-cognitive-neuroscience/chapters/ch03-methods.md) |
| **Cortical-surface recording** | Subdural ECoG grids/strips and micro-ECoG arrays. These are implanted on the brain’s surface; the notes describe high-gamma recordings used for decoding. | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md); [Poeppel ch. 15](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch15-part8-representational-models-feature-fallacy-mvpa-decoding-encoding-deep-learning.md) |
| **Penetrating intracortical arrays** | Microelectrode arrays implanted in motor, premotor, or parietal cortex; record action potentials and LFPs. Kandel describes a 96-channel clinical example. | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md); [Poeppel ch. 11](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch11-part6-corticospinal-tract-bmi-somatosensory-control-cortical-reorganization.md) |
| **Closed-loop implanted devices** | Recording contacts can detect neural activity for seizure or other activity-regulating systems; some devices then stimulate. The recording contacts are sensors, while the stimulation contacts also serve as actuators. | [Kandel ch. 39 skill notes](C:/Users/Scott/.agents/skills/kandel-principles-neural-science/chapters/ch39-brain-machine-interfaces.md) |

The skills also cover non-invasive **fMRI brain decoding** and **fNIRS** studies, but don’t present them as the main motor-control BMI sensors. fMRI is used to decode constrained stimulus or percept categories; fNIRS appears in infant and hyperscanning examples. **MEG**, measured with SQUID sensors, is another non-invasive brain-recording method, though the notes don’t identify it as a BMI controller. See [Gazzaniga ch. 6](C:/Users/Scott/.agents/skills/gazzaniga-cognitive-neuroscience/chapters/ch06-object-recognition.md), [Poeppel ch. 22](C:/Users/Scott/.agents/skills/poeppel-cognitive-neurosciences/chapters/ch22-part11-social-threat-learning-value-guided-development-cooperation-interpersonal-neuroscience.md), and [Banich ch. 3](C:/Users/Scott/.agents/skills/banich-cognitive-neuroscience/chapters/ch03-methods.md).




### 意图识别

> 写现有的eeg，dcg，fMRI 技术的优劣。
> 可以发现现有的二分法、方向解码的准确度不高，因此可能需要设置一个确认环节，也许可以做成 pragmeta 那种简单解谜的操作

意图识别的主要功能是判断使用者是否想要调用该产品、识别在给产品发话还是在对别人讲话、检测输入结束和撤销之类的“动作”。

这方面已有一定研究，比如


### 解码（decoding）

解码的作用是将代表言语的神经电信号解读成要使用者要表达的内容，作为认知增强产品的输入。得益于植入式脑机接口和基于皮层脑电图的脑机接口的技术发展，这方面已有先进的临床研究 [\[1\]](10.1016/j.celrep.2026.117162) 做到了比较可用的解码速率。

【方向的选取依据，并不是挑最容易做出东西的，而是挑最薄弱的】和科研略微不同，从产品角度来讲，各项技术的现状都与最终产品的效果直接相关，因此资源要向薄弱的环节倾斜。