<p align="center">
  <img width="420" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/logo.png" alt="Lingdan-V2 logo" />
</p>

<h1 align="center">Lingdan-V2</h1>

<p align="center"><strong>Large language models for clinically grounded reasoning in traditional Chinese medicine</strong></p>

<p align="center">面向中医知识理解、临床推理与处方推荐的多规模大语言模型系列</p>

<p align="center">
  <a href="https://modelscope.cn/organization/TCMAIBJTU"><img src="https://img.shields.io/badge/Models-ModelScope-624AFF" alt="ModelScope models" /></a>
  <a href="https://github.com/TCMAI-BJTU/LingLan"><img src="https://img.shields.io/badge/Benchmark-LingLan-2A8F5B" alt="LingLan benchmark" /></a>
  <img src="https://img.shields.io/badge/Model_scales-4B_%7C_8B_%7C_14B-0B5394" alt="Model scales: 4B, 8B, and 14B" />
</p>

<p align="center">
  <a href="#项目简介">项目简介</a> ·
  <a href="#模型下载">模型下载</a> ·
  <a href="#研究设计">研究设计</a> ·
  <a href="#主要结果">主要结果</a> ·
  <a href="#数据隐私与使用边界">使用边界</a>
</p>

## 项目简介

传统中医临床决策需要同时整合领域知识、症状表现、证候辨识、治法选择、方药组成与剂量信息。通用大语言模型虽然具备较强的语言理解能力，但在中医专业知识适配、复杂诊疗推理和结构化处方生成方面仍面临挑战。

Lingdan-V2 是基于 Qwen3 系列基座模型构建的中医大语言模型家族，覆盖 4B、8B 和 14B 三种参数规模。模型通过中医领域持续预训练（CPT）、推理导向的监督微调（SFT）和处方导向的群组相对策略优化（GRPO）逐步获得领域知识、任务执行、结构化推理与处方决策能力。其中，Lingdan-14B-R1 是本文重点研究的模型，4B 和 8B 系列用于分析模型规模及处方导向后训练的影响。

| 模型家族 | CPT 语料 | SFT 语料 | GRPO 数据 | 评测规模 |
| --- | ---: | ---: | ---: | ---: |
| 4B、8B、14B × Base、SFT、R1 | 16.62B tokens | 7.73M samples / 2.08B tokens | 24,212 source prompts | LingLan 25,624 items / 私有临床 4,348 cases |

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/overview.png" alt="Overview of Lingdan-V2 development and evaluation" />
</p>
<p align="center"><sub>图 1. Lingdan-V2 的数据基础、分阶段训练流程和评测设置。</sub></p>

## 核心贡献

- **多规模中医模型家族**：构建 4B、8B 和 14B 三种参数规模的 Base、SFT 与 R1 模型，支持领域研究、指令任务和中医临床推理等不同需求。
- **分阶段能力形成路径**：将大规模中医领域知识注入、推理导向监督学习和处方导向强化后训练整合到统一流程中。
- **面向中医临床推理的系统评测**：在 LingLan、LingLan-Hard 以及私有脾胃病处方评测集上，分别考察中医知识、信息抽取、诊疗推理、决策识别、处方组成和剂量一致性。

## 模型下载

全部模型通过 ModelScope 发布。模型名称统一采用 `Lingdan-{规模}-{阶段}` 格式。

| 模型规模 | Base | SFT | R1 |
| --- | --- | --- | --- |
| 4B | [Lingdan-4B-Base](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-Base) | [Lingdan-4B-SFT](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-SFT) | [Lingdan-4B-R1](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-R1) |
| 8B | [Lingdan-8B-Base](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-Base) | [Lingdan-8B-SFT](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-SFT) | [Lingdan-8B-R1](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-R1) |
| 14B | [Lingdan-14B-Base](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-Base) | [Lingdan-14B-SFT](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-SFT) | [Lingdan-14B-R1](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-R1) |

- **Base**：经过中医领域持续预训练，适合进一步微调和领域研究。
- **SFT**：在 Base 模型上进行监督微调，适合中医知识问答、信息抽取和一般诊疗指令任务。
- **R1**：在 SFT 模型上进行处方导向 GRPO 对齐，重点增强结构化临床推理和处方推荐能力。

## 研究设计

### 数据基础与分阶段训练

1. **持续预训练（CPT）**：使用 16.62B tokens 的中医与临床相关语料，涵盖临床数据、中医古籍与现代书籍、结构化知识、公开医学数据和通用语料，形成 Lingdan-Base 模型。
2. **监督微调（SFT）**：使用 7,729,982 个样本和 2.08B tokens，覆盖中医知识问答、信息抽取、诊疗推理、临床案例和处方生成等任务，形成 Lingdan-SFT 模型。
3. **处方导向 GRPO**：以 24,212 个临床处方生成提示构成的源数据池为基础，以处方组成一致性为主要优化目标，形成 Lingdan-R1 模型。

### 评测设置

- **LingLan**：包含 25,624 个专家核验样本，覆盖 5 个中医能力领域和 13 个子任务。
- **LingLan-Hard**：包含 5,200 个高难度样本，每个子任务选取 400 个样本。
- **私有临床处方评测**：包含 4,348 个脱敏脾胃病病例，用于评估处方组成与剂量一致性。原始临床数据不公开。

## 主要结果

### 综合基准表现

Lingdan-14B-R1 在 LingLan 全量集上的 Overall Score 为 **67.1**，在 LingLan-Hard 上为 **45.3**。在本文评测的外部模型中，DeepSeek-R1 的对应分数为 64.6 和 38.9。Lingdan-14B-R1 的处方生成 F1 在全量集和困难子集上分别达到 37.0 和 24.9。

Overall Score 是 16 个主要指标的等权平均值，其中包含剂量余弦相似度，不包含剂量 MAE、precision、recall 和替代性诊疗推理汇总指标。

<p align="center">
  <img width="760" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/overall_score_comparison.png" alt="Overall Score comparison on LingLan and LingLan-Hard" />
</p>
<p align="center"><sub>图 2. Lingdan-14B-R1 与不同规模外部模型在 LingLan 和 LingLan-Hard 上的 Overall Score 对比。</sub></p>

### 从 SFT 到 R1 的性能变化

在主要的 14B 模型链路中，处方导向 GRPO 将 Overall Score 从 65.3 提升至 67.1，并在 LingLan-Hard 上从 41.3 提升至 45.3。处方生成 F1 在全量集上从 30.9 提升至 37.0，在困难子集上从 17.4 提升至 24.9。

处方生成能力在 4B、8B 和 14B 三种规模上均表现出一致改善。相比之下，综合基准分数的变化具有模型规模和任务依赖性，因此本文将处方相关能力视为 GRPO 最直接、最稳定的改进方向。

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/training_stage_performance.png" alt="Performance changes from SFT to R1 across Lingdan-V2 model scales" />
</p>
<p align="center"><sub>图 3. 4B、8B 和 14B 模型从 SFT 到 R1 的 Overall Score 与处方生成 F1 变化。</sub></p>

### 私有临床处方评测

在 4,348 个脱敏脾胃病病例上，Lingdan-14B-R1 的 herb-level F1 为 **52.8**，剂量余弦相似度为 **50.6**，剂量 MAE 为 **4.369**。这些结果表明，在本文定义的回顾性文本评测协议下，Lingdan-14B-R1 与参考处方在药物组成及剂量结构上具有更高的一致性。

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/private_prescription_recommendation.png" alt="Private spleen-stomach prescription recommendation results" />
</p>
<p align="center"><sub>图 4. 私有脾胃病临床处方评测中的药物组成 F1、剂量余弦相似度和剂量 MAE。</sub></p>

## 数据隐私与使用边界

- Lingdan-V2 的训练与评测涉及公开数据和受限临床数据。私有临床数据不公开原始病例、患者标识符、病历片段或其他可能导致患者重识别的信息。
- 私有临床评测属于回顾性、文本化的处方一致性评测，不能等同于前瞻性临床验证或真实诊疗有效性评价。
- Lingdan-V2 仅用于科研和技术研究，不构成医疗建议，也不能替代专业医生的诊断、辨证、治疗或处方审核。
- 处方导向 GRPO 对处方相关任务的改善最为稳定，但并不代表所有中医知识和临床任务均获得同等幅度的提升。

## 引用

论文正式公开后，本节将补充完整作者信息、预印本或 DOI 地址以及 BibTeX。

> **Lingdan-V2: Large language models for clinically grounded reasoning in traditional Chinese medicine**

## 相关链接

- [Lingdan-V2 GitHub](https://github.com/TCMAI-BJTU/Lingdan-V2)
- [Lingdan-V2 models on ModelScope](https://modelscope.cn/organization/TCMAIBJTU)
- [LingLan benchmark](https://github.com/TCMAI-BJTU/LingLan)
