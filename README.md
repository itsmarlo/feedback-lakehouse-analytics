# feedback-lakehouse-analytics
# feedback-lakehouse-analytics

End-to-end lakehouse analytics project that turns raw customer reviews into business-ready insights using a Bronze, Silver, Gold architecture.

This project is designed to mirror real-world analytics engineering patterns used in Databricks environments, while running fully on open-source tools.

---

## Project Overview

**Use case**
Amazon-style product reviews

**Goal**
Build a production-oriented lakehouse pipeline that enriches raw feedback with NLP signals and exposes clear business insights.

**What this demonstrates**

* Lakehouse and medallion architecture
* Incremental and idempotent pipelines
* Delta Lake semantics (ACID, versioning, time travel)
* ML as data enrichment, not experimentation
* Business-driven analytics and storytelling

---

## Architecture

```
Raw Reviews
    ↓
Bronze (raw ingestion, schema-on-read)
    ↓
Silver (cleaned, validated, NLP-enriched)
    ↓
Gold (business metrics and aggregates)
    ↓
Streamlit Dashboard
```

The design separates ingestion, transformation, enrichment, and analytics to keep the system scalable, testable, and easy to evolve.

---

## Folder Structure

```
feedback-lakehouse-analytics/
├── data/
│   ├── bronze/
│   │   └── reviews_raw.delta
│   ├── silver/
│   │   └── reviews_enriched.delta
│   └── gold/
│       ├── sentiment_daily.delta
│       ├── top_negative_keywords.delta
│       └── topic_trends.delta
│
├── pipelines/
│   ├── ingest_bronze.py
│   ├── transform_silver.py
│   └── aggregate_gold.py
│
├── nlp/
│   ├── sentiment.py
│   ├── topics.py
│   └── keywords.py
│
├── app/
│   └── streamlit_app.py
│
├── notebooks/
│   └── exploration.ipynb
│
└── README.md
```

---

## Data Layers

### Bronze Layer

Purpose:

* Store raw reviews exactly as ingested
* Enable replay and backfills

Characteristics:

* Append-only
* Minimal transformations
* Includes metadata columns such as:

  * ingestion_timestamp
  * batch_id
  * source

---

### Silver Layer

Purpose:

* Prepare data for analytics and ML

Transformations:

* Text cleaning and normalization
* Basic validation and filtering
* NLP enrichment

  * Sentiment score and label
  * Topic assignment
  * Extracted keywords

Design choice:
ML outputs live in Silver to keep Gold strictly business-facing.

---

### Gold Layer

Purpose:

* Serve business questions directly

Examples:

* Sentiment trends over time by product
* Top negative drivers and recurring issues
* Topic evolution across categories

Gold tables are:

* Aggregated
* Stable
* Ready for BI and dashboards

---

## NLP Approach

This project treats NLP as a data enrichment step, not as a research problem.

Principles:

* Prefer interpretable models
* Favor stability over SOTA accuracy
* Keep outputs explainable to business users

Implemented techniques:

* Sentiment analysis (batch inference)
* Topic modeling (interpretable clustering)
* Keyword extraction for pain points and features

Trade-offs and limitations are explicitly documented.

---

## Pipelines

All pipelines are designed to be:

* Incremental
* Idempotent
* Re-runnable without side effects

### ingest_bronze.py

* Reads raw review data
* Adds ingestion metadata
* Writes to Bronze Delta table

### transform_silver.py

* Cleans and validates data
* Applies NLP enrichment
* Writes to Silver Delta table

### aggregate_gold.py

* Computes business metrics
* Creates analytics-ready Gold tables

---

## Dashboard

A lightweight Streamlit app exposes insights for stakeholders.

Features:

* Product and category filters
* Sentiment trends over time
* Top negative themes and keywords
* Clear mapping from metrics to business questions

Example questions answered:

* Why are ratings dropping for a product?
* Which issues appear most often in negative reviews?
* How are customer concerns evolving over time?

---

## Tech Stack

* Python
* PySpark (local)
* Delta Lake (open source)
* NLP libraries (Python)
* Streamlit

The stack is intentionally Databricks-compatible, even though it runs locally.

---

## Scaling and Production Considerations

If deployed on Databricks, this project would benefit from:

* Auto Loader for ingestion
* Delta Live Tables for pipeline orchestration
* Unity Catalog for governance
* Model serving for NLP inference

Potential scale challenges discussed:

* NLP inference cost
* Model drift
* Schema evolution
* Late-arriving data

---



## License

TBD
