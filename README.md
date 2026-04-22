[README.md](https://github.com/user-attachments/files/26989331/README.md)
# Classifying Disinformation Techniques with the DISARM Taxonomy

**Course:** MAI623 / DSC514 — Spring 2026  
**Team:** Demetris Pitsillides · Kyriakos Alampritis · Christos Kyriakou

---

## Overview

An end-to-end NLP pipeline for the automatic analysis of Foreign Information Manipulation and Interference (FIMI) incidents using the [DISARM framework](https://disarmfoundation.github.io/disarm-navigator/). The pipeline covers data collection, exploratory data analysis, classification of adversary actions and countermeasures, and a knowledge graph memory layer for LLMs.

---

## Repository Structure

```
├── data/
│   ├── fimi_dataset.csv                  # FIMI Elections Dataset (81 incidents)
│   ├── adversary_actions_dataset.csv     # Labeled statements for Task 3
│   └── countermeasures_dataset.csv       # Labeled statements for Task 3
│
├── Tasks_1_2_3.ipynb                     # Tasks 1, 2, 3 — collection, EDA, TF-IDF classification
├── parser_to_md.ipynb                    # PDF/HTML → Markdown parsing (Docling)
├── Roberta_Model.ipynb                   # DistilRoBERTa fine-tuning for Tasks 2 & 3
├── Cognee.ipynb                          # Task 4 — DISARM knowledge graph memory layer
└── README.md
```

> **Note:** Parsed article texts and the trained RoBERTa model checkpoint are stored on shared Google Drive and are not committed to this repository.

---

## Tasks

### Task 1 — Data Collection (`Tasks_1_2_3.ipynb`, `parser_to_md.ipynb`)
- Loads the [IGP FIMI Elections Dataset](https://igp.sipa.columbia.edu/news/igp-releases-new-dataset-online-foreign-information-operations-targeting-elections) (81 incidents, 266 source URLs)
- Retrieves HTML articles using `newspaper3k` and `trafilatura`; PDFs handled separately
- Parses all sources into unified markdown using [Docling](https://github.com/DS4SD/docling)
- Final coverage: **247/266 sources (~92.9%)** after automated retrieval and manual recovery

### Task 2 — Exploratory Data Analysis (`Tasks_1_2_3.ipynb`)
- Distribution of DISARM techniques, countries of origin, target countries, incidents by year
- N-gram analysis (unigrams, bigrams, trigrams) and TF–IDF keyword extraction
- Sentiment analysis across the parsed corpus

### Task 3 — Classification (`Tasks_1_2_3.ipynb`, `Roberta_Model.ipynb`)
Two classification subtasks on labeled statement datasets:

| Model | Adversary Actions | Countermeasures |
|---|---|---|
| TF-IDF + Logistic Regression | Accuracy: 0.98 · F1: 0.98 | Accuracy: 0.94 · F1: 0.94 |
| DistilRoBERTa | Accuracy: 0.99 · F1: 0.99 | Accuracy: 0.97 · F1: 0.97 |

### Task 4 — DISARM Knowledge Graph Memory Layer (`Cognee.ipynb`)
Builds a knowledge graph memory layer using [Cognee](https://docs.cognee.ai/) + OpenAI (`gpt-4o-mini`) that enables LLMs to retrieve DISARM techniques from unseen documents.

**Two-dataset architecture:**
1. **DISARM technique definitions** — 141 technique `.md` files from the [DISARM repo](https://github.com/DISARMFoundation/DISARMframeworks), each ingested as an individual dataset
2. **FIMI incidents** — each incident ingested as a structured chunk: metadata + article text + annotated techniques

**Evaluation on held-out test incidents (N=10 train, 5 test):**

| Metric | Value |
|---|---|
| Avg Precision | 0.334 |
| Avg Recall | 0.226 |
| Avg F1 | 0.124 |

**Gradual N-incident experiment:**

| N | Precision | Recall | F1 |
|---|---|---|---|
| 2 | 0.313 | 0.164 | 0.126 |
| 4 | 0.245 | 0.056 | 0.091 |
| 6 | 0.176 | 0.068 | 0.093 |
| 8 | 0.300 | 0.064 | 0.092 |
| 10 | 0.167 | 0.210 | 0.102 |

Recall improves as N increases, while precision is highest at small N. Results reflect a token-budget-constrained proof-of-concept (10/81 incidents, 141/392 techniques).

---

## Setup

All notebooks run on **Google Colab**. Open directly:

| Notebook | Open in Colab |
|---|---|
| Tasks 1–3 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/DemetrisPitsillides/MAI623_DSC514_-No-Link-Provided-_Spring_2026/blob/main/Task_1+2+3_Final.ipynb) |
| Parser | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/DemetrisPitsillides/MAI623_DSC514_-No-Link-Provided-_Spring_2026/blob/main/parser_to_md.ipynb) |
| RoBERTa | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/DemetrisPitsillides/MAI623_DSC514_-No-Link-Provided-_Spring_2026/blob/main/Roberta_Model.ipynb) |
| Cognee (Task 4) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/DemetrisPitsillides/MAI623_DSC514_-No-Link-Provided-_Spring_2026/blob/main/Cognee.ipynb) |

### Dependencies

```bash
# Tasks 1–3
pip install newspaper3k trafilatura beautifulsoup4 docling pandas openpyxl

# Task 3 — RoBERTa
pip install transformers datasets torch scikit-learn

# Task 4 — Cognee
pip install "cognee>=1.0.1" pandas openpyxl matplotlib
```

### Colab Secrets Required

| Secret | Used in |
|---|---|
| `OPENAI_API_KEY` | `Cognee.ipynb` (Task 4 — LLM + embeddings) |

---

## Data

- **FIMI Dataset:** [IGP Columbia](https://igp.sipa.columbia.edu/news/igp-releases-new-dataset-online-foreign-information-operations-targeting-elections) · [Codebook](https://igp.sipa.columbia.edu/sites/igp/files/2024-07/2024_07_01_FIMI-DISARM%20Elections%20Codebook.pdf)
- **DISARM Framework:** [Navigator](https://disarmfoundation.github.io/disarm-navigator/) · [GitHub](https://github.com/DISARMFoundation/DISARMframeworks)
- Parsed article texts are stored on shared Google Drive (mounted in Colab via `drive.mount('/content/drive')`)

---

> ⚠️ Never commit API keys to this repository. Use Colab Secrets (key icon in the left sidebar).
