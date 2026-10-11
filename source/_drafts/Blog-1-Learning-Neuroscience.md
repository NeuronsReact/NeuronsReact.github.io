---
title: 
tags:
---

回想23年写的回答，刷着AI“驱逐”人类的新闻，笔者感觉AI与人类的冲突发展有些超越预期，似乎不远的未来，人类就要选择是被击败还是加入了。笔者一直认为，人类和AI应当结合，才能避免零和博弈。为了实现这点，应当渐进地实现人和AI在认知层面的耦合，比如通过一些基于LLM的认知增强产品，还能并行解决人和人之间沟通低效的问题。针对实现认知增幅的目标，笔者开展了学习和研究。

## 找到切入点

之前几乎没接触过神经科学，先跟GPT讨论一轮。

<details>
<summary>提示词</summary>

Research topic: Use LLMs as the accelerator and sub-processor of human brains
Need: Tranform this concept to one or several scientific problems or research problems.
Problems:
1. I have a master degree of mechanics, but I don't know much about brain science. I need to understand brain science and its sub-fields, to get to know where to start.
2. I don't know the research progress about connecting computers to human brains.

</details>

GPT把这个topic转化成了3个主要的Problem：

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

虽说暴露语言是相对比较好实现功能的。不过注意到，这些信息并非必须是语言。比如同样一件事情，不同的语言都能表达出来，因此一定有一些信息是语言无关的。这些信息可以是：

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

不过，该 problem 的目标还不算完全体。以上改进并未涉及 encoding，也就是输出仍然使用传统方式，通过声音、视觉、触觉等信号反馈给人体。

### Problem C — Closed-loop brain ↔ LLM interaction

这一层相比 problem B 加入了 encoding，对应的 BMI 结构是

$$
\text{Problem}
\rightarrow
\text{Human cognitive system}
\leftrightarrow
\text{LLM}
$$

事情变得更有趣了。首先是同时具有 decoding（输入）、encoding（输出），也就是具有了完整闭环的神经链路，理论上可以代替一部分脑区的功能，从而获得一些超过人脑本身的硬件能力，比如容量更大的工作记忆、更精确和快速的计算功能、更强大的并行任务能力。并且引出一些有趣的问题：
- LLM 或者计算机应当接受、输出哪些信息。这会引出非常多的组合；
- 计算机和大脑应当如何配合，使得协同时的效果超过传统协同方式和单一使用时的效果。


## 建立素材库

根据以上 3 个 problem，GPT 也给出了一批关键词。直接构建 WOS 检索表达式:

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

为了减少一些 tag 整理前文献阅读的精力和踩坑的数量，采用 AI 按照 Problem C BMI 的结构和以上 460 篇文献的摘要来建立 tag，并给 zotero 中的文献打上对应的 tag（大致方法是用一个主 Agent 调用 1 个子 Agent 阅读 460 篇文章的标题和摘要生成 tag 表，然后让 web 版 chatgpt 逐篇读摘要、从 tag 表中选择合适的 tag，并由主 Agent 整理结果）。这 460 篇的 tagging 结果如下：


| Stage | Tag | # | Description |
|-------|-----|---|-------------|
| **Signal Acquisition / Recording** | `eeg-recording` | 230 | EEG signal acquisition |
| | `fnirs-recording` | 20 | fNIRS signal acquisition |
| | `ecog-recording` | 18 | ECoG / sEEG signal acquisition |
| | `intracortical-recording` | 31 | Intracortical / implantable microelectrode |
| | `endovascular-recording` | 49 | Endovascular / stentrode recording |
| | `fmri-recording` | 4 | fMRI signal acquisition |
| | `meg-recording` | 7 | MEG signal acquisition |
| | `us-recording` | 2 | Functional ultrasound recording |
| | `hybrid-bci` | 44 | Hybrid / multi-modal signal fusion |
| | `hardware-system` | 55 | Hardware / wearable / wireless / implantable |
| **BCI Paradigm / Evoked Response** | `mi-paradigm` | 114 | Motor imagery paradigm |
| | `ssvep-paradigm` | 57 | SSVEP paradigm |
| | `p300-paradigm` | 41 | P300 ERP paradigm |
| | `visual-paradigm` | 54 | Visual / spatial attention paradigm |
| | `auditory-paradigm` | 4 | Auditory evoked / auditory BCI |
| | `cognitive-monitoring` | 80 | Cognitive load / attention / mental-state |
| | `emotion-recognition` | 31 | Emotion / affective BCI |
| **Signal Processing** | `feature-extraction` | 109 | Feature extraction / signal processing |
| | `artifact-removal` | 25 | Artifact removal / denoising |
| **Decoding & Classification** | `machine-learning` | 137 | Traditional ML classification |
| | `deep-learning` | 96 | Deep learning / neural network decoding |
| | `transfer-learning` | 60 | Transfer learning / domain adaptation |
| **Decoding Targets** | `speech-decoding` | 31 | Speech decoding / synthesis |
| | `speller-communication` | 36 | Speller / text communication |
| | `motor-learning` | 32 | Motor learning / visuomotor adaptation |
| | `neurofeedback` | 21 | Neurofeedback / brain-state training |
| | `consciousness-assessment` | 4 | Consciousness detection |
| | `disorder-diagnosis` | 22 | Disorder diagnosis via BCI |
| **Output / Control** | `robot-control` | 49 | Robot / prosthetic / exoskeleton / wheelchair |
| | `vehicle-drone-control` | 46 | Drone / UAV / vehicle / game / VR / AR |
| **Stimulation (Encoding)** | `functional-stimulation` | 36 | FES / tDCS / TMS / neurostimulation |
| **Clinical Translation** | `clinical-trial` | 104 | Clinical trial / patient study |
| | `motor-rehabilitation` | 102 | Motor rehabilitation / stroke recovery |
| | `stroke-application` | 71 | Stroke patient application |
| | `als-application` | 93 | ALS application |
| | `sci-application` | 25 | Spinal cord injury application |
| | `animal-model` | 8 | Animal models / preclinical |
| **Ethics, Security & Society** | `ethics-privacy` | 29 | Ethics / mental privacy / societal |
| | `security` | 22 | Security / cyberattack / adversarial |
| | `commercial-industry` | 8 | Commercial / industry / business |
| **Review** | `review-survey` | 95 | Review / survey / tutorial |

还生成了这些 tag 的关系图：


```
                           HUMAN / PATIENT
                    ( ALS · Stroke · SCI · Healthy )
                                │
                ┌───────────────┼─────────────────────────┐
                │               │                         │
          Cognitive state   Motor intent            Sensory input
                │               │                         │
                ▼               ▼                         │
         ┌──────────────────────────────────┐             │
         │     SIGNAL ACQUISITION  (READ)   │             │
         │                                  │             │
         │  eeg-recording     [230]  ●●●●●  │             │
         │  endovascular-rec.  [49]  ●●●●   │             │
         │  intracortical-rec  [31]  ●●●    │             │
         │  fnirs-recording    [20]  ●●     │             │
         │  ecog-recording     [18]  ●●     │             │
         │  meg-recording       [7]  ●      │             │
         │  fmri-recording      [4]  ●      │             │
         │  us-recording        [2]  ●      │             │
         │  hybrid-bci         [44]  ●●●    │             │
         │  hardware-system    [55]  ●●●●   │             │
         └──────────────────────────────────┘             │
                                │                         │
                                ▼                         │
         ┌──────────────────────────────────┐             │
         │       BCI PARADIGM / STIMULUS    │             │
         │                                  │             │
         │  mi-paradigm       [114]  ●●●●●  │             │
         │  cognitive-monitor. [80]  ●●●●   │             │
         │  visual-paradigm    [54]  ●●●    │             │
         │  ssvep-paradigm     [57]  ●●●    │             │
         │  p300-paradigm      [41]  ●●     │             │
         │  emotion-recognition[31]  ●●     │             │
         │  auditory-paradigm   [4]  ●      │             │
         └──────────────────────────────────┘             │
                                │                         │
                                ▼                         │
         ┌──────────────────────────────────┐             │
         │     SIGNAL PROCESSING (DECODE)   │             │
         │                                  │             │
         │  feature-extraction[109]  ●●●●●  │             │
         │  machine-learning  [137]  ●●●●●  │             │
         │  deep-learning      [96]  ●●●●   │             │
         │  transfer-learning  [60]  ●●●    │             │
         │  artifact-removal   [25]  ●●     │             │
         └──────────────────────────────────┘             │
                                │                         │
                                ▼                         │
         ┌──────────────────────────────────┐             │
         │        DECODING TARGETS          │             │
         │                                  │             │
         │  speech-decoding    [31]  ●●●    │             │
         │  speller-comm.      [36]  ●●●    │             │
         │  motor-learning     [32]  ●●●    │             │
         │  neurofeedback      [21]  ●●     │             │
         │  disorder-diagnosis [22]  ●●     │             │
         │  consciousness-ass.  [4]  ●      │             │
         └──────────────────────────────────┘             │
                                │                         │
                    ┌───────────┴───────────┐             │
                    │                       │             │
                    ▼                       ▼             │
     ┌──────────────────────┐   ┌──────────────────────┐  │
     │   OUTPUT / CONTROL   │   │  STIMULATION (WRITE) │◄─┘
     │                      │   │                      │
     │  robot-control  [49] │   │ functional-stim [36] │
     │  vehicle-drone  [46] │   │   (FES · tDCS · TMS) │
     │    (UAV · VR · AR)   │   │   (neurostimulation) │
     │  exoskeleton · wheel │   │                      │
     └──────────────────────┘   └──────────────────────┘
                    │                       │
                    └───────────┬───────────┘
                                │
                                ▼
              ┌──────────────────────────────────┐
              │     CLINICAL TRANSLATION         │
              │  (CLOSED-LOOP BCI IN PRACTICE)   │
              │                                  │
              │  clinical-trial    [104]  ●●●●●  │
              │  motor-rehab.      [102]  ●●●●●  │
              │  als-application    [93]  ●●●●   │
              │  stroke-applic.     [71]  ●●●    │
              │  sci-application    [25]  ●●     │
              │  animal-model        [8]  ●      │
              └──────────────────────────────────┘
                                │
               ┌────────────────┼────────────────┐
               │                │                │
               ▼                ▼                ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │ ETHICS &     │  │  SECURITY    │  │  COMMERCIAL  │
    │ PRIVACY      │  │              │  │  INDUSTRY    │
    │              │  │              │  │              │
    │ ethics-      │  │ security     │  │ commercial-  │
    │ privacy [29] │  │ [22]         │  │ industry [8] │
    │ (mental      │  │ (cyberattack │  │ (start-ups,  │
    │  privacy,    │  │  adversarial │  │  market,     │
    │  societal)   │  │  robustness) │  │  translation)│
    └──────────────┘  └──────────────┘  └──────────────┘
                                │
                                ▼
              ┌──────────────────────────────────┐
              │   META / REVIEW [95]             │
              │   (systematic review, survey,    │
              │    tutorial, perspective,        │
              │    state-of-the-art)             │
              └──────────────────────────────────┘
                                │
                                ▼
                     COGNITIVE AUGMENTATION
                    (the ultimate goal of BCI)
```
以及对应的 summary。
<details>
<summary>Stage-by-Stage Summary</summary>

### Stage-by-Stage Summary

##### 1. Signal Acquisition (READ)
The majority of the BCI literature (230/460, 50%) uses **EEG** as the recording modality — by far the most common due to non-invasiveness and affordability. **Intracortical** (31) and **endovascular** (49) represent the invasive frontier, while **fNIRS** (20) and **ECoG** (18) serve as middle-ground semi-invasive options. **MEG** (7), **fMRI** (4), and **functional ultrasound** (2) are niche modalities. **Hybrid BCI** (44) fuses multiple signal sources, and **hardware-system** (55) addresses wearable, wireless, and implantable system design.

### 2. BCI Paradigm / Stimulus
**Motor imagery** (114) dominates as the most-studied paradigm — imagining movement without executing it. **SSVEP** (57) and **P300** (41) are the two dominant evoked-potential paradigms for spelling and communication. **Cognitive monitoring** (80) covers attention, mental workload, and fatigue detection. **Emotion recognition** (31) is a growing affective-BCI subfield. **Visual paradigms** (54) and **auditory paradigms** (4) define the sensory channel.

### 3. Signal Processing
**Feature extraction** (109) and **artifact removal** (25) form the front-end processing layer — extracting discriminative features from raw neural signals and rejecting eye/muscle artifacts. **Common spatial patterns**, **filter banks**, **wavelet decomposition**, and **independent component analysis** are the dominant techniques.

### 4. Decoding & Classification
The algorithmic core: **Traditional ML** (137, including SVM, LDA, Bayesian classifiers) remains the most common approach, but **deep learning** (96 — CNNs, transformers, spiking neural networks) is growing rapidly. **Transfer learning** (60 — domain adaptation, cross-subject generalization, zero-calibration) addresses the critical "BCI illiteracy" and cross-session variability problem.

### 5. Decoding Targets
What is being decoded: **Speech** (31 — synthesizing naturalistic speech from cortical signals), **speller communication** (36 — P300/SSVEP spellers for text), **motor learning** (32 — adapting BCI control with practice), **neurofeedback** (21 — self-regulation of brain states), **disorder diagnosis** (22 — using BCI as a diagnostic tool for depression, autism, Alzheimer's), and **consciousness assessment** (4 — detecting awareness in disorders of consciousness).

### 6. Output / Control
The decoded intent drives external devices: **robot control** (49 — prosthetics, exoskeletons, wheelchairs, assistive robots, shared autonomy) and **vehicle/drone control** (46 — UAVs, VR/AR environments, gaming, autonomous vehicles).

### 7. Stimulation (WRITE — the "other side" of BCI)
**Functional stimulation** (36) covers FES, tDCS, TMS, and neurostimulation — delivering current to the brain or muscles. This is substantially smaller than the decoding literature (36 vs. 460), reflecting that the "write" side of BCI is less mature than the "read" side.

### 8. Clinical Translation (CLOSED-LOOP)
**Clinical trials** (104) and **motor rehabilitation** (102) are the dominant application context — BCI as a therapy for stroke recovery. **ALS** (93) is the other major patient population — restoring communication for locked-in patients. **Stroke** (71) and **SCI** (25) applications target motor restoration. **Animal models** (8) support preclinical testing.

### 9. Ethics, Security & Society
**Ethics & privacy** (29 — mental privacy, autonomy, stigma, legal frameworks), **security** (22 — cyberattacks, adversarial perturbations, biometric authentication), and **commercial/industry** (8 — start-ups, market translation, business models) form the socio-technical perimeter.

### 10. Meta / Review
**Review articles** (95 — systematic reviews, surveys, tutorials, perspectives) constitute a substantial fifth of the literature — reflecting that this is a maturing field consolidating its knowledge.
</details>

至此，素材库基本建立完成。不过 tag 如此多的素材库，使用起来也需要相当的技巧。


## 尝试使用素材库

挑几个 tag 稍作深入。此时还没有到精读文章内容的阶段，仍然通过阅读 abstract 涉猎。有时也借助 AI 阅读 abstract 来涉猎。Prompt 大致如下：

<details>
<summary> Prompt </summary>
`Quiz4/zotero_skill/zotero.prompt.md` is the prompt about reading, writing zotero database.
Under the subcollection `TestSorting260823\2022-260823\1.Brain-computer interface`, there are items with tags. I only care about Q1 journals. `Quiz4/ZoteroRound0/BCI_System_Diagram.md` shows the locations of tags in the whole BCI system. You will find a tag "hybrid-bci". 

You read all items with the tag "hybrid-bci", verify if this tag is reasonable. If true, tell me what is "hybrid-bci".

---

> 除了给出总结，AI 还会顺带找出一些 tag 不合适的文章，持续更新 tag 表

---

Keep the tag `hybrid-bci` only for 30 currently solid `hybrid-bci` items. Remove the `hybrid-bci` for the weak ones and wrong ones. For the weak and wrong ones, tag them with suitable tag(s) in `Quiz4/ZoteroRound0/BCI_Classification_Table.md`. If the main topic of an item does not fit any tag in that table, you consider to add one, and update `Quiz4/ZoteroRound0/BCI_Classification_Table.md` and `Quiz4/ZoteroRound0/BCI_System_Diagram.md`.

If you have any question, ask first.
</details>

试一下“hybrid-bci”。一篇文章的 Abstract 这样解释：hBCI, is a device formed by the combination of control signal with one or more bio-signals that enhances system performance and usability. (Amalgamation / Combination：结合 / 融合; Usability：易用性 / 可用性) 。这可能会在

接着再试一下 feature extraction，即”模式识别“：这是 BCI 结构中处于原始信号采集（raw brain signals）和分类器（classifier）中间的阶段。该阶段将复杂、高维且低信噪比的信号转换为低阶、高区分度的向量，再输入分类器。

之后看到 speech-decoding，有意思。看过摘要后，选出 6 篇文章全文下载。其中 …… 的演示视频非常精彩。这些文章讨论如何通过电信号识别语言，同时还要分辨被测对象是打算发言还是自言自语。

