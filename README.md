# SydneyUniPEDro

# Automated Citation Screening for PEDro Using RAGflow

This repository contains the automated screening pipeline used to classify bibliographic records for inclusion in the [Physiotherapy Evidence Database (PEDro)](https://www.pedro.org.au/). It accompanies the paper as an appendix to support reproducibility.

## Overview

Each record (title + abstract) is sent to a RAGflow chat assistant that uses retrieval-augmented generation (RAG) to classify it as eligible (`1`) or ineligible (`0`) for inclusion in PEDro. The chat assistant retrieves similar records from a knowledge base of 4,101 human-labelled records to inform each decision.

## Repository Contents

| File | Description |
|---|---|
| `ragflow_batch_label.py` | Main script for batch automated screening |
| `PEDro_configuration.json` | RAGflow chat assistant configuration used in this study |

## Requirements

- Python 3.8+
- A self-hosted [RAGflow](https://ragflow.io) instance
- An OpenAI API key configured in RAGflow

## Input Excel Format

The script expects an `.xlsx` file with the following columns:

| Column | Content |
|---|---|
| B | Title |
| C | Abstract |
| D | Label — written by the script (`0` or `1`, `ERROR` if failed) |
| E | Timestamp — written by the script |

Row 1 is treated as a header and skipped.

## Configuration

Edit the following variables at the top of `ragflow_batch_label.py`:

```python
RAGFLOW_BASE_URL = "http://your-ragflow-host"
API_KEY          = "your-api-key"
CHAT_ID          = "your-chat-id"
EXCEL_PATH       = "path/to/your/file.xlsx"
```

## Usage

```bash
python ragflow_batch_label.py
```

The script processes each row sequentially, creating a new RAGflow session per record to ensure no cross-record context contamination. Progress is saved after every row, so the script can be safely interrupted and resumed — already-labelled rows are automatically skipped.

If a request times out or fails, it retries up to 3 times before marking the row as `ERROR` and moving on.

## RAGflow Configuration

Key settings used in this study (see `PEDro_configuration.json` for full details):

| Parameter | Value | Description |
|---|---|---|
| LLM | `gpt-4o` | Model used for classification |
| Embedding | `text-embedding-3-large` | Embedding model for knowledge base retrieval |
| Parser | `one` | Each reference stored as one chunk |
| `top_n` | `3` | Number of retrieved reference chunks per query |
| `similarity_threshold` | `0.8` | Minimum similarity score for retrieved chunks |
| `vector_similarity_weight` | `0.3` | Weight of vector similarity vs keyword matching |
| Knowledge base size | 4,000 records | Human-labelled PEDro records used as training references |
