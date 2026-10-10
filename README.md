<p align="center">
  <img width="420" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/logo.png" alt="Lingdan-V2 logo" />
</p>

<p align="center"><strong>Lingdan-V2: Large Language Models for Clinically Grounded Reasoning in Traditional Chinese Medicine</strong></p>

<p align="center">
  <a href="http://tcmai.chat/">
    <img src="https://img.shields.io/badge/OPEN_LINGDAN--V2_ONLINE-tcmai.chat-F28C28?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Open the Lingdan-V2 online demo" />
  </a>
</p>

<p align="center">
  <a href="https://modelscope.cn/collections/TCMAIBJTU/Lingdan-V2"><img src="https://img.shields.io/badge/Models-ModelScope-624AFF" alt="ModelScope models" /></a>
  <a href="https://github.com/TCMAI-BJTU/LingLan"><img src="https://img.shields.io/badge/Benchmark-LingLan-2A8F5B" alt="LingLan benchmark" /></a>
  <img src="https://img.shields.io/badge/Model_scales-4B_%7C_8B_%7C_14B-0B5394" alt="Model scales: 4B, 8B, and 14B" />
  <a href="https://github.com/TCMAI-BJTU/Lingdan-V2/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-Apache--2.0-D22128" alt="Apache License 2.0" /></a>
</p>

## Contents

1. [Overview](#overview)
2. [Model Zoo](#model-zoo)
3. [Quickstart](#quickstart)
4. [Thinking Mode](#thinking-mode)
5. [vLLM Deployment](#vllm-deployment)
6. [Task Prompts](#task-prompts)
7. [Main Results](#main-results)
8. [Data Privacy and Responsible Use](#data-privacy-and-responsible-use)
9. [License](#license)
10. [Citation](#citation)
11. [Related Links](#related-links)

## Overview

Lingdan-V2 is a Qwen3-based family of 4B, 8B, and 14B models for traditional Chinese medicine (TCM), covering knowledge, entity extraction, clinical reasoning, and prescription generation.

| Stage | Training data | Intended use |
| --- | --- | --- |
| Base: continued pretraining (CPT) | 16.62B tokens | Text completion and further training |
| SFT: reasoning-oriented supervised fine-tuning | 7,729,982 samples / 2.08B tokens | TCM instruction following |
| R1: prescription-centered GRPO | 24,212 samples | Clinical reasoning and prescription generation |

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/overview.png" alt="Overview of Lingdan-V2 development and evaluation" />
</p>
<p align="center"><sub>Figure 1. Data foundation, staged training pipeline, and evaluation settings of Lingdan-V2.</sub></p>

## Model Zoo

| Model | Training stage | Hugging Face | ModelScope |
| --- | --- | --- | --- |
| Lingdan-4B-Base | CPT | [HF](https://huggingface.co/TCMLLM/Lingdan-4B-Base) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-Base) |
| Lingdan-4B-SFT | CPT → SFT | [HF](https://huggingface.co/TCMLLM/Lingdan-4B-SFT) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-SFT) |
| Lingdan-4B-R1 | CPT → SFT → GRPO | [HF](https://huggingface.co/TCMLLM/Lingdan-4B-R1) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-4B-R1) |
| Lingdan-8B-Base | CPT | [HF](https://huggingface.co/TCMLLM/Lingdan-8B-Base) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-Base) |
| Lingdan-8B-SFT | CPT → SFT | [HF](https://huggingface.co/TCMLLM/Lingdan-8B-SFT) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-SFT) |
| Lingdan-8B-R1 | CPT → SFT → GRPO | [HF](https://huggingface.co/TCMLLM/Lingdan-8B-R1) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-8B-R1) |
| Lingdan-14B-Base | CPT | [HF](https://huggingface.co/TCMLLM/Lingdan-14B-Base) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-Base) |
| Lingdan-14B-SFT | CPT → SFT | [HF](https://huggingface.co/TCMLLM/Lingdan-14B-SFT) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-SFT) |
| Lingdan-14B-R1 | CPT → SFT → GRPO | [HF](https://huggingface.co/TCMLLM/Lingdan-14B-R1) | [MS](https://modelscope.cn/models/TCMAIBJTU/Lingdan-14B-R1) |

## Quickstart

Example: Lingdan-14B-R1. Change the model ID for other SFT/R1 checkpoints; Base model cards provide text-completion examples. Use Transformers ≥4.51.0; see the [Qwen3 quickstart](https://qwen.readthedocs.io/en/stable/getting_started/quickstart.html).

```bash
pip install -U torch transformers accelerate
```

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model_name = "TCMLLM/Lingdan-14B-R1"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    torch_dtype="auto",
    device_map="auto",
).eval()

messages = [{"role": "user", "content": "请简要介绍中医辨证论治的基本概念。"}]
text = tokenizer.apply_chat_template(
    messages,
    tokenize=False,
    add_generation_prompt=True,
    enable_thinking=True,
)
inputs = tokenizer([text], return_tensors="pt").to(model.device)
outputs = model.generate(
    **inputs,
    max_new_tokens=8192,
    eos_token_id=[151645, 151643],  # <|im_end|> and <|endoftext|>
    do_sample=True,
    temperature=0.6,
    top_p=0.95,
    top_k=20,
)
new_tokens = outputs[0, inputs.input_ids.shape[1]:]
print(tokenizer.decode(new_tokens, skip_special_tokens=True))
```

For ModelScope, install `modelscope` and replace the import and model ID:

```python
from modelscope import AutoModelForCausalLM, AutoTokenizer
model_name = "TCMAIBJTU/Lingdan-14B-R1"
```

The explicit EOS IDs stop generation at either the chat-turn or text terminator.

## Thinking Mode

The quickstart enables thinking with `enable_thinking=True`. Set it to `False` for direct answers. The released chat templates support this switch.

| Mode | `enable_thinking` | Temperature | Top-p | Top-k |
| --- | --- | ---: | ---: | ---: |
| Thinking | `True` | 0.6 | 0.95 | 20 |
| Non-thinking | `False` | 0.7 | 0.8 | 20 |

These are Qwen3 inference starting settings; behavior after fine-tuning may vary. Prefer the explicit switch over the unverified `/think` and `/no_think` soft instructions. Separate direct-generation output as follows:

```python
raw_output = tokenizer.decode(new_tokens, skip_special_tokens=True)
reasoning_part, delimiter, final_answer = raw_output.partition("</think>")
if delimiter:
    thinking_content = reasoning_part.removeprefix("<think>").strip()
    final_answer = final_answer.strip()
else:
    thinking_content = ""
    final_answer = raw_output.strip()
print("Thinking:", thinking_content)
print("Answer:", final_answer)
```

If generation hits the token limit before the thinking block closes, increase the budget and retry; the answer is incomplete.

## vLLM Deployment

Install vLLM in a GPU environment with compatible CUDA/PyTorch; see the [Qwen3 vLLM guide](https://qwen.readthedocs.io/en/stable/deployment/vllm.html).

```bash
pip install -U "vllm>=0.9.0" openai
pip install -U modelscope
```

### Start the Server

```bash
# Hugging Face: use false and the TCMLLM model ID below.
# ModelScope: change false to true and TCMLLM/ to TCMAIBJTU/.
export VLLM_USE_MODELSCOPE=false
vllm serve TCMLLM/Lingdan-14B-R1 \
  --served-model-name Lingdan-14B-R1 \
  --host 127.0.0.1 \
  --port 8000 \
  --max-model-len 16384 \
  --reasoning-parser qwen3
```

Replace the repository ID with a local model directory to reuse downloaded files. For two GPUs, append `--tensor-parallel-size 2`. Adjust the 16,384-token context limit (input + output) to available GPU memory.

### Chat Client

```python
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:8000/v1", api_key="EMPTY")
response = client.chat.completions.create(
    model="Lingdan-14B-R1",
    messages=[{"role": "user", "content": "请简要介绍中医辨证论治的基本概念。"}],
    temperature=0.6,
    top_p=0.95,
    max_tokens=8192,
    extra_body={
        "top_k": 20,
        "chat_template_kwargs": {"enable_thinking": True},
        "stop_token_ids": [151645, 151643],
    },
)
message = response.choices[0].message
thinking_content = (
    getattr(message, "reasoning", None)
    or getattr(message, "reasoning_content", None)
    or ""
)
print("Thinking:", thinking_content)
print("Answer:", message.content)
print("Finish reason:", response.choices[0].finish_reason)
```

`chat_template_kwargs` selects thinking mode; `--reasoning-parser qwen3` separates its output. To disable thinking, use the non-thinking settings above. The client supports both `reasoning` and the older `reasoning_content` field ([vLLM reference](https://docs.vllm.ai/en/latest/features/reasoning_outputs/)). Parse task JSON from `message.content`; `finish_reason="length"` indicates truncation.

`EMPTY` is a placeholder for the local server started without API-key authentication.

## Task Prompts

For SFT/R1 models, fill the placeholders in these LingLan evaluation templates and pass the text as the user message.

### Clinical-record Entity Extraction

Source: [LingLan `ner_clinic.py`](https://github.com/TCMAI-BJTU/LingLan/blob/main/simple-evals/evals/tcm_evals/ner_clinic.py).

````text
请从以下病历文本中抽取指定类型的实体。

注意：
1. 请先仔细分析文本内容，思考每个实体类型的识别要点，然后再输出结果
2. 如果某个实体类型在文本中没有对应实体，请返回空列表[]
3. 如果同一个实体在文本中出现多次，请在列表中按顺序重复出现

文本：
{text}

需要抽取的实体类型：
{entity_types}

请按照以下JSON格式输出结果，每个实体类型对应一个列表，如果文本中某个实体出现多次，请在列表中重复出现：

```json
{
  "实体类型1": ["实体1", "实体2", ...],
  "实体类型2": ["实体1", "实体2", ...],
  ...
}
```
````

### Classical-text Entity Extraction

Source: [LingLan `ner_ancient.py`](https://github.com/TCMAI-BJTU/LingLan/blob/main/simple-evals/evals/tcm_evals/ner_ancient.py).

````text
请从以下古籍文本中抽取指定类型的实体。

注意：
1. 请先仔细分析古籍文本内容，思考古文语言特点和各实体类型的识别要点，然后再输出结果
2. 如果某个实体类型在文本中没有对应实体，请返回空列表[]
3. 如果同一个实体在文本中出现多次，请在列表中按顺序重复出现

文本：
{text}

需要抽取的实体类型：
{entity_types}

请按照以下JSON格式输出结果，每个实体类型对应一个列表，如果文本中某个实体出现多次，请在列表中重复出现：

```json
{
  "实体类型1": ["实体1", "实体2", ...],
  "实体类型2": ["实体1", "实体2", ...],
  ...
}
```
````

### Joint Syndrome, Treatment, and Prescription Generation

Source: [LingLan `pres_diag_eval.py`](https://github.com/TCMAI-BJTU/LingLan/blob/main/simple-evals/evals/tcm_evals/pres_diag_eval.py).

````text
请根据以下病历文本，进行完整的中医诊疗分析，包括辨证、治法和处方。

注意：
1. 请仔细分析患者的症状表现、舌脉象等信息
2. 辨证：给出准确的中医证型判断（如有多个，用逗号分隔）
3. 治法：根据辨证结果确定相应的治疗方法（如有多个，用逗号分隔）
4. 处方：开具具体的中药处方，格式为"药名: 剂量, 药名: 剂量"

病历文本：
{medical_record}

请按照以下JSON格式输出结果：

```json
{
  "辨证": "证型1, 证型2, ...",
  "治法": "治法1, 治法2, ...",
  "处方": "药名1: 剂量1, 药名2: 剂量2, 药名3: 剂量3, ..."
}
```
````

Use the dataset’s exact entity labels. JSON examples contain illustrative ellipses; actual output must be valid JSON. In joint generation, `辨证`, `治法`, and `处方` are strings; format prescription items as `药名: 数值g`. Extract task JSON from the final answer using the output handling above.

## Main Results

Results transcribed from the paper. Full: LingLan (25,620 items); Hard: LingLan-Hard (5,200 items). Overall Score averages 16 primary metrics, excluding dose MAE. All scores use a 0–100 scale except MAE (lower is better). The paper uses zero-shot Chinese prompts, temperature 0.6, and at most 8,192 generated tokens; reproduction also requires the same data and scoring pipeline. The private evaluation uses 4,348 de-identified spleen/stomach disorder cases.

### Results by Checkpoint

| Model | Overall Full | Overall Hard | Prescription F1 Full | Prescription F1 Hard | Private herb F1 | Private dose cosine | Private dose MAE ↓ |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Lingdan-4B-SFT | 63.1 | 37.6 | 30.1 | 17.5 | 33.7 | 34.1 | 5.787 |
| Lingdan-4B-R1 | 63.4 | 40.4 | 36.2 | 24.8 | 52.7 | 49.5 | 5.062 |
| Lingdan-8B-SFT | 63.0 | 36.6 | 29.8 | 16.1 | 34.2 | 34.2 | 5.812 |
| Lingdan-8B-R1 | 63.7 | 39.1 | 34.5 | 22.6 | 50.7 | 49.5 | 6.006 |
| Lingdan-14B-SFT | 65.3 | 41.3 | 30.9 | 17.4 | 35.1 | 34.8 | 5.840 |
| Lingdan-14B-R1 | 67.1 | 45.3 | 37.0 | 24.9 | 52.8 | 50.6 | 4.369 |

GRPO improves Overall Score and prescription F1 at all three scales. Dose MAE does not improve uniformly: it increases at 8B.

### Entity Extraction and Joint Generation (R1)

Each cell is **Full / Hard**, using the paper’s primary generation/extraction F1 metrics.

| Model | Clinical-record entities F1 | Classical-text entities F1 | Syndrome generation F1 | Treatment generation F1 | Prescription generation F1 |
| --- | ---: | ---: | ---: | ---: | ---: |
| Lingdan-4B-R1 | 70.5 / 58.4 | 58.5 / 42.5 | 9.5 / 1.6 | 13.9 / 2.4 | 36.2 / 24.8 |
| Lingdan-8B-R1 | 75.3 / 64.6 | 54.6 / 37.0 | 9.5 / 2.5 | 14.3 / 3.0 | 34.5 / 22.6 |
| Lingdan-14B-R1 | 69.6 / 55.5 | 62.6 / 45.6 | 12.1 / 2.4 | 15.5 / 2.1 | 37.0 / 24.9 |

### Comparisons

Lingdan-14B-R1 exceeds the strongest external model evaluated, DeepSeek-R1, by 2.5 Overall Score points on Full and 6.4 on Hard.

<p align="center">
  <img width="760" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/overall_score_comparison.png" alt="Overall Score comparison on LingLan and LingLan-Hard" />
</p>
<p align="center"><sub>Figure 2. Overall Score comparison between Lingdan-14B-R1 and external models of different scales on LingLan and LingLan-Hard.</sub></p>

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/training_stage_performance.png" alt="Performance changes from SFT to R1 across Lingdan-V2 model scales" />
</p>
<p align="center"><sub>Figure 3. Changes in Overall Score and prescription-generation F1 from SFT to R1 across the 4B, 8B, and 14B models.</sub></p>

<p align="center">
  <img width="100%" src="https://raw.githubusercontent.com/TCMAI-BJTU/Lingdan-V2/main/assets/private_prescription_recommendation.png" alt="Private prescription recommendation results for spleen and stomach disorders" />
</p>
<p align="center"><sub>Figure 4. Herbal composition F1, dosage cosine similarity, and dosage MAE in the private clinical prescription evaluation for spleen and stomach disorders.</sub></p>

## Data Privacy and Responsible Use

Lingdan-V2 is for research and does not replace qualified clinical judgment. Private clinical data are not released. Retrospective prescription agreement is not evidence of clinical safety or effectiveness; use de-identified inputs and professional review for any clinical application.

## License

Lingdan-V2 model weights and project-owned documentation and images are released under the [Apache License 2.0](https://github.com/TCMAI-BJTU/Lingdan-V2/blob/main/LICENSE). See [NOTICE](https://github.com/TCMAI-BJTU/Lingdan-V2/blob/main/NOTICE) for attribution notices. Third-party data and materials remain subject to their original terms.

## Citation

Cite Lingdan-V2 and the earlier Lingdan work:

```bibtex
@misc{hua2026lingdanv2,
  title  = {{Lingdan-V2}: Large language models for clinically grounded reasoning in traditional Chinese medicine},
  author = {Hua, Rui and Wei, Yu and Jin, Ziri and Sun, Zhuo and Chang, Kai and Xia, Jianan and Shu, Zixin and Li, Xiaodong and Jia, Dongmei and Dong, Fei and Zhang, Runshun and Yu, Jian and Xu, Hao and Yu, Haibin and Li, Jiansheng and Liu, Baoyan and Wang, Wenjia and Zhou, Xuezhong},
  year   = {2026},
  url    = {https://github.com/TCMAI-BJTU/Lingdan-V2}
}

@article{hua2024lingdan,
  title={Lingdan: enhancing encoding of traditional Chinese medicine knowledge for clinical reasoning tasks with large language models},
  author={Hua, Rui and Dong, Xin and Wei, Yu and Shu, Zixin and Yang, Pengcheng and Hu, Yunhui and Zhou, Shuiping and Sun, He and Yan, Kaijing and Yan, Xijun and others},
  journal={Journal of the American Medical Informatics Association},
  volume={31},
  number={9},
  pages={2019--2029},
  year={2024},
  publisher={Oxford University Press}
}
```

## Related Links

[GitHub](https://github.com/TCMAI-BJTU/Lingdan-V2) · [Hugging Face collection](https://huggingface.co/collections/TCMLLM/lingdan-v2) · [ModelScope collection](https://modelscope.cn/collections/TCMAIBJTU/Lingdan-V2) · [LingLan benchmark](https://github.com/TCMAI-BJTU/LingLan)
