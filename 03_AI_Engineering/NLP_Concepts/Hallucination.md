---
title: The hallucination
aliases:
  - 幻觉
date: 2026-01-02
tags:
  - LLM
status:
  - 🌱
---

# 🎯 Problem / Context 

> *while running **inference** on the **4-bit quantized Mistral-7B-Instruct** with prompt"If the Company notifies the Vendor that it has failed to deliver the Goods, it shall verify the status.Question: Who is the second 'it' referring to?", I observed that the model produced **nonsensical responses** five trials, which I identified as a clear case of **hallucination***

# ✅ Solution / Concept 

*   **Core Concept:** hallucination
*   **Explanation:**
    *   模型没有学习到概率分布而胡乱生成的内容。
    *   尝试在输入层注入语言学规则约束加以解决。

# 💻 Code Snippet 

```python
from transformers import AutoTokenizer, AutoModelForCausalLM, BitsAndBytesConfig

model_id = "Mistral-7B-Instruct-v0.2"

bnb_config = BitsAndBytesConfig(

    load_in_4bit=True,

    bnb_4bit_compute_dtype=torch.float16,

    bnb_4bit_use_double_quant=True,

    bnb_4bit_quant_type="nf4",  # 关键：NF4

)

tokenizer = AutoTokenizer.from_pretrained(

    model_id,

    use_fast=True

)

model = AutoModelForCausalLM.from_pretrained(

    model_id,

    quantization_config=bnb_config,

    device_map="auto",

    torch_dtype=torch.float16,

)

def llm_fallback(prompt, max_new_tokens=128):

    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)

    with torch.no_grad():

        outputs = model.generate(

            **inputs,

            max_new_tokens=max_new_tokens,

            do_sample=True,  

            temperature=0.7,

        )

    return tokenizer.decode(outputs[0], skip_special_tokens=True)
    
text= "If the Company notifies the Vendor that it has failed to deliver the Goods, it shall verify the status.Question: Who is the second 'it' referring to?"

test = llm_fallback(text)

test

```

