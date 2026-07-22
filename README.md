# Token-Efficient Fine-Tuning of Small Language Models for Text-to-SQL

Fine-tuning Gemma-3-4B-Instruct using QLoRA, 4-bit quantization, and layer freezing to study the trade-off between SQL generation quality and token efficiency.

## Overview

This project investigates whether a Small Language Model (SLM) can generate accurate SQL queries while reducing computational cost.

The model is fine-tuned on the BIRD Text-to-SQL benchmark using QLoRA and evaluated under different prompt compression strategies to measure:

- SQL generation quality
- Prompt token usage
- Inference latency
- Energy consumption
- GPU cost

The objective is to understand the trade-off between accuracy and token efficiency for enterprise Text-to-SQL applications.

## Key Features

- Gemma-3-4B-Instruct fine-tuning
- 4-bit NF4 quantization (QLoRA)
- LoRA adapters
- Layer freezing for efficient training
- Lightweight schema-linking heuristic
- Multiple prompt optimization strategies
- Token, latency, energy and cost analysis
- Evaluation using multiple NLP metrics

## Project Architecture

```
BIRD Dataset
      │
      ▼
Data Preprocessing
(question + schema + hint + SQL)
      │
      ▼
Chat Template Conversion
(System/User/Assistant)
      │
      ▼
Gemma-3-4B-Instruct
      │
      ▼
4-bit Quantization
      │
      ▼
QLoRA Fine-tuning
      │
      ▼
Layer Freezing
      │
      ▼
Inference
      │
      ├── Full Schema Prompt
      ├── Linked Schema Prompt
      └── Minimal Prompt
      │
      ▼
Evaluation
(EM, Token F1, ROUGE-L, BERTScore,
Latency, Energy, Cost)
```

## Dataset

Dataset used: **BIRD Benchmark**

- ~9,400 Text-to-SQL examples
- 95 real-world databases
- Multiple enterprise domains

Each example contains:

- Natural language question
- Database schema
- Optional hint
- Ground truth SQL

## Prompt Strategies

| Strategy | Description |
|---|---|
| Full Schema | Entire database schema included |
| Linked Schema | Only schema tables relevant to the question |
| Minimal | Compact prompt with linked schema and no hint |

The study compares how prompt size affects model quality and efficiency.

## Fine-Tuning Configuration

| Parameter | Value |
|---|---|
| Base Model | Gemma-3-4B-Instruct |
| Quantization | 4-bit NF4 |
| Method | QLoRA |
| LoRA Rank | 16 |
| Alpha | 32 |
| Dropout | 0.05 |
| Learning Rate | 2e-4 |
| Epochs | 2 |
| Batch Size | 2 |
| Gradient Accumulation | 8 |

## Layer Freezing

The lower half of transformer layers are frozen during training.

**Motivation:**

- Lower memory usage
- Faster training
- Preserve general language understanding
- Update only higher-level task-specific representations

## Schema Linking

A lightweight keyword-overlap heuristic selects only relevant tables.

```
Question + Hint
        │
        ▼
Extract Keywords
        │
        ▼
Compare with Table Names
        │
        ▼
Keep Matching Tables
```

This reduces unnecessary schema tokens before inference.

## Evaluation Metrics

The model is evaluated using:

- Exact Match
- Token-level F1
- ROUGE-L
- BERTScore
- Prompt Tokens
- Output Tokens
- Latency
- Throughput
- Energy Consumption
- GPU Cost

## Results

### Efficiency

| Strategy | Prompt Tokens | Total Tokens | Latency (s) |
|---|---|---|---|
| Full Schema | 105.43 | 166.56 | 7.03 |
| Linked Schema | 105.43 | 166.56 | 6.81 |
| Minimal | 51.24 | 109.55 | 6.32 |

### Quality

| Strategy | Exact Match | Token F1 | ROUGE-L | BERTScore |
|---|---|---|---|---|
| Full Schema | 0.0067 | 0.6236 | 0.6270 | 0.9302 |
| Linked Schema | 0.0067 | 0.6236 | 0.6270 | 0.9302 |
| Minimal | 0.0000 | 0.5693 | 0.5474 | 0.9113 |

## Key Findings

- Minimal prompting reduced total tokens by approximately 34%.
- Lower prompt size reduced latency and estimated energy consumption.
- Removing hints negatively impacted SQL generation quality.
- Linked-schema prompting requires improved schema splitting to demonstrate its full benefit.

## Repository Structure

```
.
├── data/
│   ├── train.json
│   ├── test.json
│
├── preprocessing/
│   ├── preprocess.py
│   ├── schema_linking.py
│
├── training/
│   ├── train.py
│   ├── lora_config.py
│
├── evaluation/
│   ├── evaluate.py
│   ├── metrics.py
│
├── notebooks/
│
├── results/
│   ├── figures/
│   ├── tables/
│
├── requirements.txt
└── README.md
```

## Installation

```bash
git clone https://github.com/<username>/Token-Efficient-Text-to-SQL.git

cd Token-Efficient-Text-to-SQL

pip install -r requirements.txt
```

## Training

```bash
python training/train.py
```

## Evaluation

```bash
python evaluation/evaluate.py
```

## Future Improvements

- Embedding-based schema linking
- Execution Accuracy (EX) evaluation on BIRD
- Compare against larger LLMs (Llama, Qwen, GPT)
- CodeCarbon integration for carbon footprint analysis
- Semantic-aware layer freezing (SALF)
- Retrieval-augmented schema selection

## References

- BIRD Benchmark
- Gemma-3-4B-Instruct
- QLoRA
- LoRA
- Semantic-Aware Layer Freezing (SALF)

## Citation

```bibtex
@misc{token_efficient_text2sql,
  title={Token-Efficient Fine-Tuning of Small Language Models for Text-to-SQL Generation},
  author={Your Name},
  year={2026},
  note={Research Project}
}
```
