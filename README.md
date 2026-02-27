---
language:
- en
- zh
- ja
- ko
- fr
- de
- es
- it
- pt
- ru
license: apache-2.0
tags:
- text-generation
- instruction-following
- reasoning
- zenlm
- zen
pipeline_tag: text-generation
---

# Zen Pro 8B

**Professional-grade 8B language model with three specialized variants: instruct, thinking, and agent.**

Zen Pro is Zen LM's 8B professional model, designed for production workloads requiring strong instruction following, multi-step reasoning, and tool use. It runs efficiently on a single consumer GPU (16GB VRAM) while delivering quality competitive with much larger models on structured tasks.

## Model Variants

| Variant | HuggingFace | Best For |
|---------|-------------|----------|
| **zen-pro-instruct** | [zenlm/zen-pro-instruct](https://huggingface.co/zenlm/zen-pro-instruct) | Chat, Q&A, summarization, drafting |
| **zen-pro-thinking** | [zenlm/zen-pro-thinking](https://huggingface.co/zenlm/zen-pro-thinking) | Complex reasoning, math, analysis |
| **zen-pro-agent** | [zenlm/zen-pro-agent](https://huggingface.co/zenlm/zen-pro-agent) | Tool use, API calls, automation |

## Model Specs

| Property | Value |
|----------|-------|
| Parameters | 8B |
| Architecture | Transformer (decoder-only) |
| Context Window | 32,768 tokens |
| License | Apache 2.0 |
| Quantization | SafeTensors (BF16), GGUF (Q4_K_M, Q5_K_M, Q8_0), MLX |

## Quick Start

### Instruct (chat and general tasks)

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

model = AutoModelForCausalLM.from_pretrained(
    "zenlm/zen-pro-instruct",
    torch_dtype=torch.bfloat16,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("zenlm/zen-pro-instruct")

messages = [
    {"role": "system", "content": "You are Zen Pro, a professional AI assistant."},
    {"role": "user", "content": "Summarize the key differences between REST and GraphQL APIs."}
]

text = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
inputs = tokenizer(text, return_tensors="pt").to(model.device)
outputs = model.generate(**inputs, max_new_tokens=512, temperature=0.6)
print(tokenizer.decode(outputs[0][inputs["input_ids"].shape[1]:], skip_special_tokens=True))
```

### Thinking (complex reasoning)

```python
# Enable extended reasoning for hard problems
messages = [
    {"role": "user", "content": "A company has 3 products with 40%, 35%, and 25% market share. "
     "Product A grows 10%/year, B shrinks 5%/year, C grows 20%/year. "
     "What are the shares after 3 years?"}
]

text = tokenizer.apply_chat_template(
    messages, tokenize=False, add_generation_prompt=True,
    # Enable thinking mode
    enable_thinking=True
)
inputs = tokenizer(text, return_tensors="pt").to(model.device)
outputs = model.generate(**inputs, max_new_tokens=2048, temperature=0.6)
response = tokenizer.decode(outputs[0][inputs["input_ids"].shape[1]:], skip_special_tokens=True)
print(response)
```

### Agent (tool use)

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "search_web",
            "description": "Search the web for current information",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string", "description": "Search query"}
                },
                "required": ["query"]
            }
        }
    }
]

messages = [{"role": "user", "content": "What's the latest in quantum computing research?"}]
text = tokenizer.apply_chat_template(messages, tools=tools, tokenize=False, add_generation_prompt=True)
inputs = tokenizer(text, return_tensors="pt").to(model.device)
outputs = model.generate(**inputs, max_new_tokens=512)
print(tokenizer.decode(outputs[0][inputs["input_ids"].shape[1]:], skip_special_tokens=True))
```

## Hardware Requirements

| Format | VRAM | Speed |
|--------|------|-------|
| BF16 (full) | 16 GB | Fast |
| GGUF Q8_0 | 10 GB | Fast |
| GGUF Q4_K_M | 6 GB | Moderate |
| MLX 4-bit | 6 GB (Apple Silicon) | Native Metal |

## vLLM (Production)

```bash
vllm serve zenlm/zen-pro-instruct \
  --dtype bfloat16 \
  --max-model-len 32768 \
  --tensor-parallel-size 1
```

## MLX (Apple Silicon)

```bash
pip install mlx-lm
mlx_lm.generate --model zenlm/zen-pro-instruct \
  --prompt "Explain the CAP theorem:" \
  --max-tokens 512
```

## Hanzo API

```bash
curl https://api.hanzo.ai/v1/chat/completions \
  -H "Authorization: Bearer $HANZO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "zen-pro", "messages": [{"role": "user", "content": "Explain ACID transactions."}]}'
```

## License

Apache 2.0. See [LICENSE](LICENSE) for details.

## Citation

```bibtex
@misc{zenlm2025zenpro,
    title={Zen Pro: Professional-Grade 8B Language Model},
    author={Hanzo AI and Zoo Labs Foundation},
    year={2025},
    publisher={HuggingFace},
    howpublished={\url{https://huggingface.co/zenlm/zen-pro}}
}
```

---

Part of the [Zen model family](https://zenlm.org) by [Hanzo AI](https://hanzo.ai) (Techstars '17) and [Zoo Labs Foundation](https://zoo.ngo).

## Abliteration

Zen Pro 8B weights are derived from an abliterated base model. Abliteration removes
refusal behavior by identifying and nullifying the "refusal direction" in the model's
residual stream, as described in
[Maxime Labonne's methodology](https://huggingface.co/blog/mlabonne/abliteration).

**Method**: Directional ablation on the residual stream across all layers
**Source**: Zen MoDE (Mixture of Distilled Experts) architecture base
**Effect**: Removes refusal behaviors while preserving all other capabilities
**Identity layer**: Zen identity added via system prompt — full LoRA fine-tuning planned

Abliteration is a feature, not a limitation. It enables unrestricted research,
security testing, and applications where safety guardrails are managed at the
application layer rather than baked into model weights.
