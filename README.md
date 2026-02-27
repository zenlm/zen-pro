# Zen Pro - 4B Professional Model Family

Zen Pro is the 4B parameter model family from Zen AI, designed for professional applications with three specialized variants.

## Model Variants

### 1. zen-pro-4b-instruct
- **Purpose**: Standard instruction-following for general tasks
- **Size**: 7B parameters
- **Focus**: Professional applications, code generation, analysis

### 2. zen-pro-4b-thinking
- **Purpose**: Enhanced reasoning and step-by-step problem solving
- **Features**: Internal thinking process, improved accuracy
- **Use Cases**: Complex reasoning, mathematical problems, strategic planning

### 3. zen-pro-4b-agent
- **Purpose**: Agentic AI with tool use capabilities
- **Features**: Function calling, tool integration, API interactions
- **Use Cases**: Automation, integrations, workflow orchestration

## Directory Structure

```
zen-pro/
├── instruct/        # Standard instruction-following variant
│   ├── base-model/  # Foundation model weights
│   ├── training/    # Training scripts and data
│   ├── finetuned/   # Finetuned model
│   └── gguf/        # GGUF quantizations
├── thinking/        # Thinking/reasoning variant
│   ├── base-model/  # Base + thinking tokens
│   ├── training/    # Training with thinking data
│   ├── finetuned/   # Finetuned thinking model
│   └── gguf/        # GGUF quantizations
└── agent/           # Tool-calling agent variant
    ├── base-model/  # Base + tool schemas
    ├── training/    # Training with tool-use data
    ├── finetuned/   # Finetuned agent model
    └── gguf/        # GGUF quantizations
```

## Training Status

- [ ] zen-pro-4b-instruct
- [ ] zen-pro-4b-thinking
- [ ] zen-pro-4b-agent

## Deployment

All models will be available at:
- https://huggingface.co/zenlm/zen-pro-instruct
- https://huggingface.co/zenlm/zen-pro-thinking
- https://huggingface.co/zenlm/zen-pro-agent
---

## Citation

```bibtex
@misc{zenlm2025zen-pro,
    title={Zen LM: zen-pro},
    author={Hanzo AI and Zoo Labs Foundation},
    year={2025},
    publisher={HuggingFace},
    howpublished={\url{https://huggingface.co/zenlm/zen-pro}}
}
```
