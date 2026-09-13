---
layout: page
title: DeepPass2
description: Multi-layer secret detection combining regex, a fine-tuned transformer, and LLM validation
importance: 7
category: open-source
---

A context-aware secret scanner that combines regex-based rules, a fine-tuned token-classification transformer, and LLM-based validation to detect structured credentials and free-form passwords hidden in documents. Password extraction is framed as sequence labeling, trained on a large synthetic corpus generated for the task, with a staged inference pipeline that chunks documents, extracts candidate spans, and validates them with an LLM to cut down false positives.

[View on GitHub →](https://github.com/SpecterOps/DeepPass2)

**Stack:** Python, Hugging Face Transformers, Flask, AWS Bedrock
