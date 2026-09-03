# MCOFINX — Financial Information Retrieval

Hybrid information retrieval system for **opinion-aware financial document ranking**, developed on the **FiQA benchmark**.

The project investigates how lexical relevance, dense semantic representations, and financial sentiment signals can be combined to improve retrieval for natural-language financial queries.

## Overview

Financial information needs often go beyond factual keyword matching: users may look for explanations, evaluations, opinions, or perspectives about financial topics.

To address this, MCOFINX compares traditional lexical retrieval approaches with neural and hybrid retrieval methods.

The final system combines:

- **BM25** for first-stage lexical retrieval
- **Sentence-BERT** for dense semantic reranking
- **FinBERT** for financial sentiment analysis
- **Selective sentiment gating** for opinion-oriented queries

The experiments are conducted on the **FiQA** dataset using the PyTerrier framework.

## Retrieval Pipeline

### 1. Lexical Retrieval

BM25 is used as the main lexical baseline and as the first-stage retriever in the final hybrid system.

Additional lexical experiments include:

- BM25
- BM25 + RM3 query expansion
- BM25 + entity-based score boosting

### 2. Dense Semantic Retrieval

Documents and queries are represented in a shared embedding space using:

**Sentence-BERT — `sentence-transformers/all-MiniLM-L6-v2`**

A dense index is built with **PyTerrier DR FlexIndex**, allowing documents to be ranked according to semantic similarity rather than lexical overlap alone.

### 3. Selective Financial Sentiment

Financial sentiment is estimated using:

**FinBERT — `ProsusAI/finbert`**

Document-level sentiment scores are precomputed offline.

Sentiment is incorporated into the ranking only when the query contains opinion or evaluative cues, preventing sentiment information from introducing unnecessary noise for factual queries.

### 4. Hybrid Ranking

The final ranking combines normalized lexical and semantic scores, with an additional sentiment component for opinion-oriented queries.

Conceptually:

`Final Score = BM25 + Dense Similarity + Selective Sentiment`

This architecture separates computationally expensive offline operations from query-time retrieval and reranking.

## Experiments

The following systems were evaluated:

1. **BM25**
2. **BM25 + RM3**
3. **BM25 + Entity Boost**
4. **Dense Sentence-BERT Retrieval**
5. **Hybrid BM25 + Sentence-BERT + FinBERT**

Evaluation is performed using standard Information Retrieval metrics:

- MAP
- Precision@1, @5, @10
- Recall@5, @10
- nDCG@5, @10

## Results

The hybrid system substantially improves retrieval performance over the lexical BM25 baseline.

| Metric | BM25 | Hybrid System |
|---|---:|---:|
| MAP | 0.210 | **0.322** |
| Precision@1 | 0.236 | **0.370** |
| Precision@5 | 0.106 | **0.169** |
| Recall@5 | 0.247 | **0.378** |
| Recall@10 | 0.310 | **0.449** |
| nDCG@5 | 0.230 | **0.361** |
| nDCG@10 | 0.253 | **0.384** |

The experiments show that **dense semantic retrieval is the main driver of performance improvement**, while selectively incorporating sentiment provides an additional signal for opinion-oriented financial queries.

Lexical query expansion with RM3 provides no significant improvement over BM25, while simple entity matching produces only marginal gains.

## Dataset

The project uses the **FiQA test collection** available through `ir_datasets`:

`irds:beir/fiqa/test`

FiQA contains financial questions and documents drawn from real-world financial discourse, including community discussions and financial microblogs.

## Tech Stack

- Python
- PyTerrier
- PyTerrier-DR
- Sentence-Transformers
- Hugging Face Transformers
- FinBERT
- PyTorch
- Pandas
- NumPy
- Matplotlib
- Jupyter / Google Colab

## Repository Structure

```text
mcofinx-financial-ir/
│
├── README.md
├── mcofinx_financial_ir.ipynb
│
├── docs/
│   ├── mcofinx_report.pdf
│   └── mcofinx_presentation.pdf
│
├── requirements.txt
└── .gitignore
