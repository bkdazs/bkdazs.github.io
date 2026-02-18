---
title: "Introduction to Large Language Models (LLMs)"
date: 2025-11-29
draft: false
description: "A deep dive into how Large Language Models work, from transformers to GPT architecture."
categories:
  - AI-ML
tags:
  - llm
  - transformers
  - gpt
  - deep-learning
  - nlp
series:
  - "AI Fundamentals"
cover:
  image: ""
  alt: "LLM Architecture"
  caption: "Understanding the architecture behind modern AI"
  relative: false
ShowToc: true
TocOpen: true
---

## Introduction

Large Language Models (LLMs) have revolutionized how we interact with AI. From ChatGPT to Claude, these models demonstrate remarkable capabilities in understanding and generating human-like text.

## The Transformer Architecture

The foundation of modern LLMs is the **Transformer** architecture, introduced in the 2017 paper "Attention Is All You Need."

### Key Components

1. **Self-Attention Mechanism**: Allows the model to weigh the importance of different words
2. **Positional Encoding**: Provides sequence order information
3. **Feed-Forward Networks**: Process the attended representations

```python
import torch
import torch.nn as nn

class SelfAttention(nn.Module):
    def __init__(self, embed_size: int, heads: int):
        super().__init__()
        self.embed_size = embed_size
        self.heads = heads
        self.head_dim = embed_size // heads
        
        self.queries = nn.Linear(embed_size, embed_size)
        self.keys = nn.Linear(embed_size, embed_size)
        self.values = nn.Linear(embed_size, embed_size)
        self.fc_out = nn.Linear(embed_size, embed_size)
        
    def forward(self, query, key, value, mask=None):
        N = query.shape[0]
        q_len, k_len, v_len = query.shape[1], key.shape[1], value.shape[1]
        
        # Linear projections
        Q = self.queries(query)
        K = self.keys(key)
        V = self.values(value)
        
        # Reshape for multi-head attention
        Q = Q.reshape(N, q_len, self.heads, self.head_dim)
        K = K.reshape(N, k_len, self.heads, self.head_dim)
        V = V.reshape(N, v_len, self.heads, self.head_dim)
        
        # Scaled dot-product attention
        energy = torch.einsum("nqhd,nkhd->nhqk", [Q, K])
        
        if mask is not None:
            energy = energy.masked_fill(mask == 0, float("-inf"))
        
        attention = torch.softmax(energy / (self.head_dim ** 0.5), dim=3)
        
        out = torch.einsum("nhqk,nvhd->nqhd", [attention, V])
        out = out.reshape(N, q_len, self.embed_size)
        
        return self.fc_out(out)
```

## How LLMs Learn

### Pre-training

LLMs are trained on massive text corpora using **self-supervised learning**:

- **Masked Language Modeling (BERT-style)**: Predict masked tokens
- **Causal Language Modeling (GPT-style)**: Predict the next token

### Fine-tuning

After pre-training, models can be fine-tuned for specific tasks:

- Instruction following
- Code generation
- Question answering

## Popular LLM Families

| Model Family | Organization | Parameters | Key Feature |
|-------------|--------------|------------|-------------|
| GPT-4 | OpenAI | ~1T+ | Multimodal |
| Claude | Anthropic | ~100B+ | Constitutional AI |
| Llama | Meta | 7B-70B | Open weights |
| Gemini | Google | ~1T+ | Native multimodal |

## Conclusion

LLMs represent a paradigm shift in AI. Understanding their architecture helps us leverage them effectively and anticipate their limitations.

## Further Reading

- "Attention Is All You Need" (Vaswani et al., 2017)
- "Language Models are Few-Shot Learners" (GPT-3 paper)
- Andrej Karpathy's "Let's build GPT" video series
