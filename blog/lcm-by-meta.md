# AI 论文｜LCM by Meta：预测下一个概念，而非 Token

*首发于飞书 · 2025年3月17日*

## 论文信息

1. LCM 论文链接：https://ai.meta.com/research/publications/large-concept-models-language-modeling-in-a-sentence-representation-space/
2. LCM Github 链接：https://github.com/facebookresearch/large_concept_model?tab=readme-ov-file
3. SONAR 论文链接：https://ai.meta.com/research/publications/sonar-sentence-level-multimodal-and-language-agnostic-representations/
4. SONAR Github 链接：https://github.com/facebookresearch/SONAR
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

## 论文解读

### 1 LCM 研究背景

目前主流的模型（ChatGPT、Claude、Llama 等）都是基于 Transformer 架构训练的语言模型，这些模型的特点是通过预测下一个 Token 来进行预训练。由于这种预测性的模型架构的“缺陷”，使得得它们难以进行高层次的抽象推理和规划。

人类在理解和生成文本时，通常会先在高层次规划整体结构，然后再逐步添加细节。Meta的科学家们，提出了LCM的概念，旨在让模型能够像人类一样，在更高层次上进行语义处理。

LCM的核心在于它不再执着于预测下一个词，而是在更高的语义层级——“概念”上进行思考。它把句子看作一个概念单元，并用一种叫做SONAR的句子嵌入技术来表示这些概念。这意味着LCM处理的不再是单个的词语，而是整句话的含义

一个简单的对比：

- LLM：Large Language Model（大语言模型）
- LCM：Large Concept Models（大概念模型）

### 2 LCM 技术方案

#### 2.1 LCM 推理机制

LCM的最大特点在于，它不再像传统语言模型那样逐词预测，而是在句子表征空间中进行建模。这意味着，LCM将句子视为一个概念单元，并利用句子嵌入（sentence embeddings）来表示这些概念。LCM的目标是预测下一个句子的嵌入向量，也就是下一个“概念”。这种方法能够更好地捕捉文本的整体语义结构，使模型能够在更高的抽象层面上进行推理。

> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

- 左图： SONAR 概念嵌入空间中推理的可视化
- 右图：大型概念模型（LCM）的基本结构

#### 2.2 SONAR 简介

> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

LCM 的核心组件是句子嵌入模型 SONAR。利用 SONAR 将句子编码为固定大小的向量表示，这些向量表示被视为「概念」。SONAR 支持 200 种语言的文本和 76 种语言的语音输入。

#### 2.3 训练方法

1. 训练方法： a. 自回归句子预测：模型被训练为在嵌入空间中自回归地预测下一个句子。 b. 多种损失函数：包括均方误差（MSE）回归、扩散模型以及量化方法。 c. Noise Schedule: 论文探索了多种噪声调度方法，包括 Cosine, Quadratic 以及 Sigmoid 噪声调度方法，以及不同的 Loss Weighting 策略。
- a. 自回归句子预测：模型被训练为在嵌入空间中自回归地预测下一个句子。
- b. 多种损失函数：包括均方误差（MSE）回归、扩散模型以及量化方法。
- c. Noise Schedule: 论文探索了多种噪声调度方法，包括 Cosine, Quadratic 以及 Sigmoid 噪声调度方法，以及不同的 Loss Weighting 策略。
2. 训练数据：使用 1.3T token 的数据训练 1.6B 参数模型，使用 2.7T token 的数据训练 7B 参数模型。
3. 推理策略： a. Classifier-free guidance: 用于提升生成质量和多样性 b. Epsilon scaling: 用于缓解扩散模型生成时常出现的过曝问题。
- a. Classifier-free guidance: 用于提升生成质量和多样性
- b. Epsilon scaling: 用于缓解扩散模型生成时常出现的过曝问题。
4. 句子分割: 论文探索了 Spacy 和 Segment Any Text (SaT) 两种句子分割方法，并在此基础上增加了最大长度限制。

#### 2.4 模型架构

基础模型（Base-LCM）

> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

Base-LCM： 一个标准的 decoder-only Transformer 模型，直接预测下一个概念的嵌入向量。

扩散模型（Diffusion-LCM）

1. One-Tower Diffusion LCM： 用单个 Transformer 模型，通过扩散过程预测下一个概念的嵌入向量。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

2. Two-Tower Diffusion LCM： 使用两个 Transformer 模型，一个负责编码上下文，另一个负责通过扩散过程生成下一个概念的嵌入向量。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

量化模型（Quant-LCM）

> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

Quant-LCM： 这个模型将 SONAR 空间量化为离散单元，然后在这些离散单元上进行建模。这种方法可以在不损失太多性能的情况下显著降低计算成本。

### 4 实验过程

#### 4.1 实验验证方法

论文通过以下实验验证了 LCM 的有效性：

1. 预训练评估：在多个数据集上评估模型的下一个句子预测能力，使用 L2 距离、往返 L2 距离、对比准确率、复述和互信息等指标。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

2. 指令微调：在 Cosmopedia 数据集上对预训练模型进行微调，并评估模型生成故事的连贯性和流畅性。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

3. 摘要生成：在 CNN DailyMail, XSum 和 LCFO 数据集上评估模型的摘要生成能力
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

本文暂未被其它文档引用

> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

4. 摘要扩展：在 CNN DailyMail 和 XSum 数据集上评估模型将摘要扩展为长文本的能力。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

5. 跨语言零样本泛化：在 XLSum 数据集上评估模型在未见过的语言上的摘要生成能力。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

6. 显式规划：探索在 LCM 中加入显式规划机制，提高生成长文本的连贯性。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

#### 4.2 实验结果

实验结果表明：

- 基于扩散的 LCM 模型在下一个句子预测任务上明显优于其他模型。
- LCM 在摘要生成任务上具有竞争力，尤其在长文本摘要上表现良好。
- LCM 在摘要扩展任务上能够生成连贯且有意义的文本。
- LCM 在跨语言零样本泛化方面表现出色，在多种语言上优于 Llama-3.1-8B。
- 在 LCM 中引入显式规划机制，可以显著提高生成长文本的连贯性。
> 🖼️ *此处有配图，见[飞书原文](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)*

根据实验结果，LCM 的在应用场景，不同的模型字啊不同类型的任务各具优势：

### 5 结论与未来的研究方向

#### LCM 相比于 LLM 的优势

- 更符合人类认知：LCM 在概念级别而非 Token 级别进行操作，更接近人类的认知方式。
- 多语言和多模态：LCM 天生具有多语言和多模态的潜力，因为它建立在多语言、多模态的 SONAR 空间之上。
- 零样本泛化能力：LCM 可以在没有见过特定语言或任务的情况下进行泛化，减少了对特定训练数据的依赖。
- 可解释性：尽管语义空间的可解释性较弱，但相对与 Transformer 等黑盒模型，基于语义空间建模的 LCM 可以被认为更具有可解释性。

#### 局限性&缺点

虽然LCM展现了巨大的潜力，但也存在一些局限性：

- 适用场景有限：LCM 适合“概念”类属性较强的的文本任务，如文本翻译、故事生成，目前不适合精细化场景（对下一个 Token 要求高的的任务），如代码生成、数学推理的任务
- 对嵌入空间的依赖性：LCM 的性能受到所选句子嵌入空间（如 SONAR）的质量和特性的限制。SONAR 是一个通用的句子嵌入空间，它并不一定针对 LCM 的 next sentence prediction 任务做了专门优化。同时，由于 SONAR 本身是基于翻译任务进行训练的，其几何结构可能不完全符合 LCM 的需求。
- 对句子级概念的限制: 将句子作为概念，在粒度上可能并不总是合适的。长句子可能包含多个概念，而短句子可能无法表达完整的概念。
- 训练的复杂性：与 token 级模型相比，在嵌入空间中训练模型可能更具挑战性，需要大量高质量的多语言和多模态数据来训练模型，使其具备强大的泛化能力。
- Diffusion 模型的局限性: 虽然 diffusion 模型在生成连续数据上表现良好，但句子嵌入是离散的对象，这使得 diffusion 模型在文本生成上的优势不明显。
- Quantized LCM 的性能有待提升：尽管 Quantized LCM 是一种有前景的方法，但目前其性能不如基于 Diffusion 的 LCM 模型，可能的原因是当前 SONAR 空间并不是为量化任务所设计。
- 模型生成文本的质量: 虽然在 ROUGE 指标上具有竞争力，但 LCM 在诸如 CoLA, SH-4 和 SH-5 等指标上得分较低，这表明其生成文本在流畅性，来源可追溯以及语义覆盖方面尚有不足。
- 评估挑战：在嵌入空间中的生成需要新的评估方法，传统的基于词元的评估指标可能不再适用。

#### 未来研究方向

LCM的出现，为语言模型的研究开辟了一个全新的方向。未来，可以进一步探索以下方向：

- 更丰富的概念表征： 探索结合句法、语义、知识等信息的更丰富的概念表征方式
- 更精细的句子分割： 研究更精细的句子分割技术，以更好地处理复杂的文本结构
- 更高效的训练和推理算法： 开发更高效的训练和推理算法，降低计算成本，提高模型的实用性
- 更广泛的应用场景： 探索LCM在问答、机器翻译、对话系统等更多自然语言处理任务中的应用

## 参考资料

1. Large Concept Models (LCMs) by Meta: The Era of AI After LLMs?Large Concept Models (LCMs) by Meta: The Era of AI After LLMs?
2. 颠覆GPT！Meta推出大型概念模型LCM: 从“猜词”到“理解”
3. LLM的新尝试LCM（Large Concept Models）
4. LLM被推翻？Meta重磅开源LCM：大概念模型！引爆全部生成任务！
5. 关于大概念模型（LCM）的问题解读
6. 【论文解读】LCM：预测下一个「概念」而非token

---

原文（含全部配图）：[飞书链接](https://my.feishu.cn/wiki/QONwwuCKSiRjXHkStgrcrzusnFb)