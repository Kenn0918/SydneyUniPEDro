# SydneyUniPEDro

# Automated Citation Screening for PEDro Using RAGflow

This repository contains the automated screening pipeline used to classify bibliographic records for inclusion in the [Physiotherapy Evidence Database (PEDro)](https://www.pedro.org.au/). It accompanies the paper as an appendix to support reproducibility.

## Overview

Each record (title + abstract) is sent to a RAGflow chat assistant that uses retrieval-augmented generation (RAG) to classify it as eligible (`1`) or ineligible (`0`) for inclusion in PEDro. The chat assistant retrieves similar records from a knowledge base of 4,101 human-labelled records to inform each decision.

In addition to the RAG-based approach, this repository also includes baseline machine learning and transformer-based models for comparison.

## Repository Contents

| File | Description |
|---|---|
| `ragflow_batch_label.py` | Main script for batch automated screening using RAG |
| `PEDro_configuration.json` | RAGflow chat assistant configuration used in this study |
| `Baseline Models — SVM & Logistic Regression` | Traditional machine learning baseline models |
| `BERT-Based Text Classification — BioBERT / Bio_ClinicalBERT / ClinicalBERT / SciBERT` | Transformer-based NLP models for text classification |

## Acknowledgement

The baseline machine learning models (SVM and Logistic Regression) and BERT-based models (BioBERT, Bio_ClinicalBERT, ClinicalBERT, and SciBERT) were developed by **Prof. Dr. Thomas Schrader**. These components are included here to support comparative evaluation within this study.

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
