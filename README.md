# 📚 BookVibe

> Discover your next read by how it *feels*, not just its genre.
> An agentic big data pipeline for trope-based book discovery.

![PySpark](https://img.shields.io/badge/PySpark-3.x-E25A1C?logo=apache-spark)
![LangGraph](https://img.shields.io/badge/LangGraph-Agentic-blue)
![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python)

---

## 📖 About

BookVibe is a hybrid recommendation engine built on ~1M Amazon Kindle
reviews. Rather than relying on coarse genre tags and star ratings,
the pipeline uses LLM-driven agents to extract narrative **tropes**
(e.g. "Enemies to Lovers", "Dystopian World-building") from raw review
text, enabling recommendations based on how a book actually *feels*
to read.

Built as part of NUS BT4221 (Big Data Techniques and Technologies).

---

## ✨ Key Features

### 🤖 Agentic LangGraph Pipeline
- **Validator Node** – LLM gatekeeper that filters out non-narrative
  reviews (e.g. delivery complaints, packaging issues)
- **Extractor Node** – Semantic extractor that maps reviews to
  structured tropes and genres via Pydantic schemas
- **Autonomous Orchestrator** – Self-tuning LangGraph DAG that
  iteratively adjusts popularity and recency weights to optimise
  Recall@K and NDCG@K without manual grid search

### ⚡ Distributed PySpark Pipeline
- Processes 700MB+ of raw Kindle review data
- K-Core bipartite filtering (Item K>50, User K≥10) — 17× improvement
  in matrix density
- Chronological 80/20 train-test split via Spark Window Functions
  to prevent data leakage
- Spark-native cosine similarity via SQL Catalyst Optimiser
  (no Python UDF overhead)

### 🔢 Multi-Modal Feature Engineering
- **Genre Encoding** – Sparse binary vectors (596-D) via
  CountVectorizer
- **TF-IDF Tropes** – 512-D keyword-based narrative vectors via
  HashingTF + IDF
- **Word2Vec Tropes** – 512-D semantic embeddings capturing latent
  thematic relationships
- **Feature Fusion** – 1,108-D unified narrative signature per book

### 🎯 Hybrid Recommendation Engine
| Model | Recall@50 | NDCG@50 |
|---|---|---|
| Item-based CF + Popularity Boost | **0.2710** | **0.1316** |
| Item-based CF (KNN) | 0.2688 | 0.1302 |
| User-based CF (KNN) | 0.2371 | 0.1113 |
| ALS (Matrix Factorisation) | 0.0823 | 0.0297 |
| Content (TF-IDF + Popularity) | 0.0917 | 0.0379 |
| Full Hybrid (CF + Content) | 0.1609 | 0.0752 |

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Big Data Processing | PySpark, Spark MLlib |
| Agentic Framework | LangGraph, LangChain |
| LLM | OpenAI GPT-4o |
| Schema Enforcement | Pydantic |
| Feature Engineering | TF-IDF, Word2Vec, CountVectorizer |
| Collaborative Filtering | ALS, Item-KNN, User-KNN |
| Language | Python 3.x |

---
## 🏗️ Pipeline Architecture

**Phase 1 — Data Ingestion & Pruning**
- Structural pruning + K-Core filter (Item >50, User ≥10)
- Output: 102K rows, 0.70% matrix density

↓

**Phase 2 — Agentic Feature Engineering (LangGraph)**
- Validator Node filters non-narrative reviews
- Extractor Node pulls tropes + genres via GPT-4o
- Pydantic schema enforcement

↓

**Phase 3 — Vectorisation (Spark ML)**
- Genre CountVectorizer → 596-D
- Trope TF-IDF → 512-D
- Trope Word2Vec → 512-D
- Fused feature vector → 1,108-D

↓

**Phase 4 — Modelling & Auto-Tuning**
- Chronological 80/20 train-test split
- Hybrid Recommender (Content + CF + Popularity Boost)
- LangGraph orchestrator auto-tunes weights
- Evaluation: Recall@K, NDCG@K
---

## 📊 Dataset

**UCSD Amazon Kindle Store Reviews (5-core)**
- 982,619 reviews across 9 columns
- Spanning 1996–2014
- 701.88MB raw size
- Post-filtering: 102,784 interactions, 5,174 users, 2,836 books

---


## 🚀 Setup

Requires Python 3.10+ and Java (for PySpark).

```bash
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

Set your OpenAI API key:

```bash
export OPENAI_API_KEY=your_key_here
```

Download the [UCSD Amazon Kindle 5-core dataset](https://www.kaggle.com/datasets/bharadwaj6/kindle-reviews)
and place it at:

```
data/raw/kindle_reviews.csv
```

---

## ▶️ Running

Run the full pipeline:

```bash
python scripts/run_all.py
```

Or run stages individually:

```bash
python scripts/run_preprocessing.py   # clean + filter
python scripts/generate_features.py   # LLM enrichment + vectorisation
python scripts/train_models.py        # train + evaluate
```

To skip a stage and reuse existing artifacts:

```bash
python scripts/run_all.py --skip-preprocessing
python scripts/run_all.py --skip-features
python scripts/run_all.py --skip-training
```
```

## 👥 Team (NUS BT4221)

| Name |
|---|
| Fan Ryan |
| Shang Yi Qian |
| Tay Kai |
| Ong Yi Jie |
| Dannon Lee Kang Wei |
| Wang Ziheng |
