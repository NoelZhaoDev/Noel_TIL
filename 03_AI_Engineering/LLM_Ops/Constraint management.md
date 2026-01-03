---
title: Constraint management
aliases:
  - 约束管理
date: 2026-01-03
tags:
  - LLM
status:
  - 🌱
---

# 🎯 Problem / Context 

> *To mitigate **hallucinations** and enforce strict **instruction following**, I explored various methods for **constraining** LLM outputs. While initial attempts using **structured data** formats—such as **Markdown**, **XML**, and **JSON**—failed to yield consistent results, I eventually achieved the desired effect by leveraging a **chat template**. By defining **rules** at the **system level**, I instructed the model to monitor specific **tags** within the **user message** and adhere to the associated **instructions**. Crucially, the model was restricted from **translating** or **echoing** the content of these tags in its final output.*


# ✅ Solution / Concept 

*   **Core Concept:** chat template
*   **Explanation:**
    *   LLM训练时学习到的一种结构。通过system层和user层的分离。让模型的注意力保持在system层。有更强的约束力。
    *   目前为止约束是有效的。更复杂的场景表现还未知。

# 💻 Code Snippet 

```python
messages = [

    {

        "role": "system",

        "content": "You are a translation engine. Translate the user input into Japanese. Pay close attention to the <linguist_guardrail> tags. These contain strict logic constraints you MUST follow. Do NOT translate the linguist_guardrail tag themselves or output them."

    },

    {

        "role": "user",

        "content": """

          The agreement shall be effective as of January 1st, 2026.

          The insurance covers old men and women who have retired.

        <linguist_guardrail>

          Adjective 'old' scope is ambiguous.

          Translate with the interpretation that 'old' modifies BOTH 'men' and 'women'.Use distinct Japanese phrasing to clarify this (e.g., "高齢の男性および高齢の女性").

        </linguist_guardrail>          

          If the Company notifies the Vendor that it has failed to deliver the goods, it shall pay a penalty.

        <linguist_guardrail>

          'failed to deliver' implies the subject is the Supplier (Vendor).

          'pay a penalty' for failure implies the subject is the Defaulting Party (Vendor).

          Explicitly translate the first 'it' as 'Vendor' (ベンダー).

          Explicitly translate the second 'it' as 'Vendor' (同社/ベンダー).

          Do NOT use ambiguous pronouns like 'それ' or '彼'.

        </linguist_guardrail>          

          This contract is governed by the laws of Japan.

        """

    }

]
```
