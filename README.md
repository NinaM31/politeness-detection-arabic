<h4 align="left">MOSKA-NLP at AdabEval 2026</h4>
<h2 align="left">Feature-Enriched Ensembling for Arabic Politeness Detection</h2>
<p align="left">
  <img src="https://img.shields.io/badge/Task-AdabEval%202026-blue" alt="Task: AdabEval 2026">
  <img src="https://img.shields.io/badge/Workshop-OSACT7-blue" alt="Workshop: OSACT7">
  <img src="https://img.shields.io/badge/Rank-2nd%20Place-silver" alt="Rank: 2nd Place">
  <img src="https://img.shields.io/badge/Macro--F1-0.87-success" alt="Macro-F1: 0.87">
  <img src="https://img.shields.io/badge/Accuracy-93%25-success" alt="Accuracy: 93%">
</p>

## Abstract
In this paper, we present our system for subtask A of the AdabEval 2026 shared task, which focuses on classifying Arabic text into Polite, Neutral, and Impolite categories. Politeness detection is challenging because it cannot be inferred from lexical meaning alone. This is prominent in Arabic language, where politeness is often conveyed through formulaic expressions, stylistic cues, and dialectal variations. Our approach follows a three-stage strategy. First, we evaluate five Arabic sentence embedding models based on different pretrained encoders to identify a strong representation backbone. Second, we enrich sentence embeddings with explicit lexical, surface-level, and auxiliary signals derived from external models, including dialect, intent, and sarcasm classifiers. Third, we combine predictions from independently trained models, using weighted probability-level ensembling with class-specific decision thresholds to address class imbalance. Experimental results show that feature-enriched representations consistently outperform embedding-only baselines, with additional gains obtained from calibrated ensembling. The proposed system achieves a macro-F1 score of 0.87 and an accuracy of 93\% on the official AdabEval 2026 evaluation for subtask A.

## Repository Status

This repository documents the system architecture, feature design, and experimental results.  

---

## Method

<p align="center">
<img src="./figures/system_overview.png" width="700">
</p>

The system consists of a **primary classification arm** combined with **auxiliary models**, using weighted probability fusion and thresholding.
### 1. Sentence Embeddings

The system evaluates and uses Arabic pretrained models such as:

- [MARBERTv2](https://huggingface.co/UBC-NLP/MARBERTv2)
- [Marbert-all-nli-triplet-Matryoshka](https://huggingface.co/Omartificial-Intelligence-Space/Marbert-all-nli-triplet-Matryoshka)
- [CamelBERT](https://huggingface.co/CAMeL-Lab/bert-base-arabic-camelbert-da)

The final ensemble combines a primary feature-enriched MARBERTv2 arm with auxiliary fine-tuned models.

### 2. Feature Enrichment

The enriched feature set includes:

- Manual lexical indicators
  - polite phrases
  - honorifics
  - addressee markers
  - insult terms
- Automatically derived class-specific lexicons
- Surface-level cues
  - character elongation
  - punctuation patterns
  - emoji counts
  - diacritic-related signals
- Auxiliary model predictions
  - [Dialect](https://huggingface.co/IbrahimAmin/marbertv2-arabic-written-dialect-classifier)
  - [Intent](https://huggingface.co/bassemessam/Arabic-bank77-intent-classification)
  - [Sarcasm](https://huggingface.co/hardiksr/sarcasm-classifier-bert-base-arabic-camelbert-msa-data)  

These features are used as complementary signals rather than standalone predictors.

### 3. Classification and Ensembling

The primary classification arm combines:

- Logistic Regression
- SGD classifier with log-loss
- Complement Naive Bayes

The final system applies:

- weighted probability fusion
- class-specific thresholds
- Neutral as the default class under uncertainty

---

## Dataset
<p>
  <img align="right" width="329" alt="Label distribution" src="https://github.com/user-attachments/assets/2535f3a7-9140-4dc3-be76-dc5e4ecefc5a" />
</p>

The [ADAB dataset](https://github.com/iwan-rg/ADAB-Arabic-Politeness-Dataset) in AdabEval2026 contains 4,895 training instances, 693 validation instances,
and 1,406 test instances. The label distribution is highly imbalanced.

#### Dataset splits

| Split | Size |
|---|---:|
| Train | 4,895 |
| Validation | 693 |
| Test | 1,406 |

<br clear="right"/>

---

## Results

| Stage | Split | Macro-F1 | Accuracy |
|---|---|---:|---:|
| Frozen MARBERTv2 | Validation | 0.753 | 85% |
| Frozen Matryoshka | Validation | 0.797 | 87% |
| + Feature enrichment | Validation | 0.845 | 90% |
| Fine-tuned CamelBERT | Validation | 0.822 | 90% |
| Fine-tuned Matryoshka | Validation | 0.834 | 90% |
| Fine-tuned MARBERTv2 | Validation | 0.847 | 90% |
| + Feature enrichment | Validation | 0.853 | 91% |
| + Primary arm | Validation | 0.859 | 92% |
| Ensemble | Validation | 0.862 | 92% |
| **Final ensemble** | **Official test** | **0.87** | **93%** |

---

## Key Findings

- Feature enrichment produced the largest improvement over embedding-only baselines.
- Manual lexical indicators were more useful than automatically derived lexicons.
- Character elongation and sarcasm signals provided robust gains.
- Auxiliary models were most useful as complementary signals inside the ensemble.
- The main remaining difficulty is lexical and pragmatic ambiguity, especially when subtle Polite or Impolite cases are confused with Neutral.

---

## Installation

```bash
pip install torch torchvision torchaudio
pip install transformers==4.57.1
pip install scikit-learn==1.6.1
pip install optuna==2.10.1
pip install emoji arabic-reshaper python-bidi
```

Recommended environment:

- Python 3.10+
- PyTorch 2.0+
- Transformers 4.57+
- scikit-learn 1.6+
- Optuna 2.10+
---

## Acknowledgments

We thank the AdabEval 2026 shared task organizers for providing the dataset and support. We also acknowledge the developers of the open-source pretrained Arabic models used in this system.

