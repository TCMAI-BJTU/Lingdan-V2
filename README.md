<p align="center">
  <img width="420" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/logo.png" alt="Lingdan-V2 logo" />
</p>

<p align="center"><strong>Lingdan-V2: Large Language Models for Clinically Grounded Reasoning in Traditional Chinese Medicine</strong></p>

<p align="center">A multi-scale family of language models for TCM knowledge comprehension, clinical reasoning, and prescription recommendation</p>

<p align="center">
  <a href="https://modelscope.cn/organization/TCMAIBJTU"><img src="https://img.shields.io/badge/Models-ModelScope-624AFF" alt="ModelScope models" /></a>
  <a href="https://tcmai.chat/"><img src="https://img.shields.io/badge/Online_Demo-tcmai.chat-F28C28" alt="Lingdan-V2 online demo" /></a>
  <a href="https://github.com/TCMAI-BJTU/LingLan"><img src="https://img.shields.io/badge/Benchmark-LingLan-2A8F5B" alt="LingLan benchmark" /></a>
  <img src="https://img.shields.io/badge/Model_scales-4B_%7C_8B_%7C_14B-0B5394" alt="Model scales: 4B, 8B, and 14B" />
  <a href="https://github.com/TCMAI-BJTU/Lingdan-V2/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-Apache--2.0-D22128" alt="Apache License 2.0" /></a>
</p>

<p align="center">
  <a href="#overview">Overview</a> ·
  <a href="#model-zoo">Model Zoo</a> ·
  <a href="#study-design">Study Design</a> ·
  <a href="#main-results">Main Results</a> ·
  <a href="#data-privacy-and-responsible-use">Responsible Use</a> ·
  <a href="#license">License</a>
</p>

## Overview

Clinical decision-making in traditional Chinese medicine (TCM) requires the joint integration of domain knowledge, symptom presentations, syndrome differentiation, treatment principle selection, herbal prescription composition, and dosage information. Although general-purpose large language models demonstrate strong language understanding capabilities, they remain limited in their adaptation to specialized TCM knowledge, complex diagnostic-therapeutic reasoning, and structured prescription generation.

Lingdan-V2 is a family of TCM large language models developed from Qwen3 backbone models at three parameter scales: 4B, 8B, and 14B. Through TCM-domain continued pretraining (CPT), reasoning-oriented supervised fine-tuning (SFT), and prescription-centered Group Relative Policy Optimization (GRPO), the models progressively acquire domain knowledge, task execution capabilities, structured reasoning, and prescription decision-making abilities. Lingdan-14B-R1 is the primary model investigated in this study, while the 4B and 8B series provide complementary evidence regarding the effects of model scale and prescription-centered post-training.

| Model family | CPT corpus | SFT corpus | GRPO data | Evaluation scale |
| --- | ---: | ---: | ---: | ---: |
| 4B, 8B, and 14B × Base, SFT, and R1 | 16.62B tokens | 7.73M samples / 2.08B tokens | 24,212 Samples | LingLan: 25,624 items / Private clinical set: 4,348 cases |

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/overview.png" alt="Overview of Lingdan-V2 development and evaluation" />
</p>
<p align="center"><sub>Figure 1. Data foundation, staged training pipeline, and evaluation settings of Lingdan-V2.</sub></p>

## Key Contributions

- **A multi-scale family of TCM language models:** We develop Base, SFT, and R1 models at the 4B, 8B, and 14B parameter scales to support domain research, instruction-following tasks, and TCM clinical reasoning.
- **A staged pathway for capability development:** We integrate large-scale TCM knowledge acquisition, reasoning-oriented supervised learning, and prescription-centered reinforcement post-training within a unified framework.
- **A systematic evaluation of TCM clinical reasoning:** We evaluate TCM knowledge, information extraction, diagnostic-therapeutic reasoning, decision recognition, prescription composition, and dosage agreement on LingLan, LingLan-Hard, and a private prescription dataset for spleen and stomach disorders.

## Model Zoo

All models are released on ModelScope. Model names follow the unified format `Lingdan-{Scale}-{Stage}`.

| Model scale | Base | SFT | R1 |
| --- | --- | --- | --- |
| 4B | [Lingdan-4B-Base](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-Base) | [Lingdan-4B-SFT](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-SFT) | [Lingdan-4B-R1](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-R1) |
| 8B | [Lingdan-8B-Base](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-Base) | [Lingdan-8B-SFT](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-SFT) | [Lingdan-8B-R1](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-R1) |
| 14B | [Lingdan-14B-Base](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-Base) | [Lingdan-14B-SFT](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-SFT) | [Lingdan-14B-R1](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-R1) |

- **Base:** Continued pretrained models for further fine-tuning and domain-specific research.
- **SFT:** Models obtained by supervised fine-tuning of the Base checkpoints for TCM question answering, information extraction, and general diagnostic and therapeutic instruction-following tasks.
- **R1:** Models aligned from the SFT checkpoints through prescription-centered GRPO, with an emphasis on structured clinical reasoning and prescription recommendation.

## Study Design

### Data Foundation and Staged Training

1. **Continued pretraining (CPT):** Lingdan-Base models are trained on 16.62B tokens of TCM and clinically relevant text, including clinical data, classical and modern TCM literature, structured knowledge, public medical data, and general-domain corpora.
2. **Supervised fine-tuning (SFT):** Lingdan-SFT models are trained on 7,729,982 samples comprising 2.08B tokens. The tasks include TCM question answering, information extraction, diagnostic-therapeutic reasoning, clinical cases, and prescription generation.
3. **Prescription-centered GRPO:** Lingdan-R1 models are trained on 24,212 Samples, with prescription composition agreement serving as the primary optimization objective.

### Evaluation Settings

- **LingLan:** 25,624 expert-verified samples spanning five TCM capability domains and 13 subtasks.
- **LingLan-Hard:** 5,200 challenging samples, with 400 samples selected from each subtask.
- **Private clinical prescription evaluation:** 4,348 de-identified cases involving spleen and stomach disorders, used to evaluate prescription composition and dosage agreement. The original clinical data are not publicly released.

## Main Results

### Comprehensive Benchmark Performance

Lingdan-14B-R1 achieves an Overall Score of **67.1** on the full LingLan benchmark and **45.3** on LingLan-Hard. Among the external models evaluated in this study, DeepSeek-R1 obtains corresponding scores of 64.6 and 38.9. Lingdan-14B-R1 also achieves prescription-generation F1 scores of 37.0 on the full benchmark and 24.9 on the challenging subset.

The Overall Score is computed as the unweighted mean of 16 primary metrics. It includes dosage cosine similarity and excludes dosage mean absolute error (MAE), precision, recall, and alternative aggregate metrics for diagnostic-therapeutic reasoning.

<p align="center">
  <img width="760" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/overall_score_comparison.png" alt="Overall Score comparison on LingLan and LingLan-Hard" />
</p>
<p align="center"><sub>Figure 2. Overall Score comparison between Lingdan-14B-R1 and external models of different scales on LingLan and LingLan-Hard.</sub></p>

### Performance Changes from SFT to R1

Within the primary 14B model trajectory, prescription-centered GRPO improves the Overall Score from 65.3 to 67.1 on the full LingLan benchmark and from 41.3 to 45.3 on LingLan-Hard. Prescription-generation F1 increases from 30.9 to 37.0 on the full benchmark and from 17.4 to 24.9 on the challenging subset.

Overall Score and prescription-generation F1 improve from SFT to R1 across the 4B, 8B, and 14B model scales on both LingLan and LingLan-Hard. At 8B, Overall Score increases from 63.0 to 63.7 on the full benchmark and from 36.6 to 39.1 on LingLan-Hard, while prescription-generation F1 increases from 29.8 to 34.5 and from 16.1 to 22.6, respectively. The larger gains in prescription generation indicate that the most pronounced cross-scale improvement remains concentrated in the task directly represented by the GRPO reward, while individual knowledge, extraction, reasoning, and dosage metrics do not improve uniformly.

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/training_stage_performance.png" alt="Performance changes from SFT to R1 across Lingdan-V2 model scales" />
</p>
<p align="center"><sub>Figure 3. Changes in Overall Score and prescription-generation F1 from SFT to R1 across the 4B, 8B, and 14B models.</sub></p>

### Private Clinical Prescription Evaluation

On 4,348 de-identified cases involving spleen and stomach disorders, Lingdan-14B-R1 achieves a herb-level F1 score of **52.8**, a dosage cosine similarity of **50.6**, and a dosage MAE of **4.369**. Under the retrospective, text-based evaluation protocol defined in this study, these results indicate closer agreement with the reference prescriptions in both herbal composition and dosage structure.

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/private_prescription_recommendation.png" alt="Private prescription recommendation results for spleen and stomach disorders" />
</p>
<p align="center"><sub>Figure 4. Herbal composition F1, dosage cosine similarity, and dosage MAE in the private clinical prescription evaluation for spleen and stomach disorders.</sub></p>

## Data Privacy and Responsible Use

- The training and evaluation of Lingdan-V2 involve both public data and restricted clinical data. The private clinical data do not disclose original cases, patient identifiers, medical record excerpts, or other information that could enable patient re-identification.
- The private clinical evaluation is a retrospective, text-based assessment of prescription agreement. It does not constitute prospective clinical validation or evidence of effectiveness in real-world clinical practice.
- Lingdan-V2 is intended solely for scientific and technical research. It does not provide medical advice and must not replace diagnosis, syndrome differentiation, treatment, or prescription review by qualified healthcare professionals.
- Prescription-centered GRPO yields its most consistent improvements on prescription-related tasks. This finding does not imply uniform improvements across all TCM knowledge and clinical tasks.

## License

The Lingdan-V2 model weights, together with the documentation and images in this repository for which the project authors hold copyright, are released under the [Apache License 2.0](https://github.com/TCMAI-BJTU/Lingdan-V2/blob/main/LICENSE). Lingdan-V2 is developed from Qwen3 open-weight models, which are also released under the Apache License 2.0.

Subject to the terms of the Apache License 2.0, personal, research, and commercial use is permitted, as are copying, modification, and redistribution. Redistributions of the original or derivative versions must retain the license text and all applicable copyright, patent, trademark, and attribution notices; disclose any modifications; and preserve the project's [NOTICE](https://github.com/TCMAI-BJTU/Lingdan-V2/blob/main/NOTICE) file.

The Apache License 2.0 does not relicense third-party training data, external benchmark materials, or private clinical data. These resources remain subject to their original licenses, data-use agreements, and privacy requirements. The medical safety statements above describe the intended use and associated risks of the models; they do not impose additional restrictions under the Apache License 2.0. Users are responsible for deployment decisions, regulatory compliance, and any clinical use.

This section is provided only as a summary. The `LICENSE` and `NOTICE` files in this repository govern the applicable rights and obligations.

## Citation

If you use Lingdan-V2 in your research, please cite this project:

```bibtex
@misc{hua2026lingdanv2,
  title  = {{Lingdan-V2}: Large Language Models for Clinically Grounded Reasoning in Traditional Chinese Medicine},
  author = {Hua, Rui and Zhou, Xuezhong},
  year   = {2026},
  url    = {https://github.com/TCMAI-BJTU/Lingdan-V2}
}
```

## Related Links

- [Lingdan-V2 GitHub](https://github.com/TCMAI-BJTU/Lingdan-V2)
- [Lingdan-V2 models on ModelScope](https://modelscope.cn/organization/TCMAIBJTU)
- [LingLan benchmark](https://github.com/TCMAI-BJTU/LingLan)
