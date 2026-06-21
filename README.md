# Multilingual Health Question Answering — Low-Resource African Languages


Final project for **Machine Learning Techniques I**, submitted to the [Zindi Multilingual Health Question Answering Challenge](https://zindi.africa). This repository contains the full training, fine-tuning, and inference pipeline used to generate health-related answers in low-resource African languages (Amharic, Akan, Luganda, Kiswahili) and English.

**Author:** El Hadji Seck

---

## 📋 Project Summary

The task is to generate accurate, fluent, language-appropriate answers to health questions across multiple African languages. This repo documents seven experiments spanning a TF-IDF retrieval baseline, zero-shot generation, and fine-tuned mT5 models (small and base), evaluated with ROUGE-1 F1, ROUGE-L F1, and LLM-as-a-Judge metrics.

Full methodology, results, and analysis are in the accompanying report: `ElHadjiSeck_FinalProject.pdf` (or `.docx`).

---

## 🏆 Results Summary

| Exp | Description | Model | Data | ROUGE-1 | ROUGE-L | Zindi Score |
|---|---|---|---|---|---|---|
| 1 | TF-IDF retrieval baseline | — | 100% | 0.4773 | 0.3969 | **0.4901** |
| 2 | Zero-shot generation | NLLB-600M | 100% | — | — | Failed (missing IDs) |
| 3 | Fine-tune, no prompt | mt5-small | 20% | 0.1807 | 0.1451 | 0.2308 |
| 4 | + language-aware prompt | mt5-small | 20% | 0.2352 | 0.1733 | 0.2338 |
| 5 | + more epochs (5) | mt5-small | 10% | 0.2204 | 0.1659 | 0.2245 |
| 6 | Model scale-up | mt5-base | 5% | 0.1538 | 0.1264 | 0.1991 |
| 7 | + beam search tuning | mt5-base | 5% | 0.1953 | 0.1517 | 0.2500 |

**Best submission: Experiment 1 (TF-IDF retrieval), Zindi score 0.4901.**

See the report's Discussion section for full analysis of why the retrieval baseline outperformed fine-tuned neural approaches under the compute constraints available.

---

## 📁 Repository Structure

```
multilingual_qa/
├── multilingual_health_qa_starter_notebook.ipynb   # Main notebook — runs end-to-end on Colab
├── submissions/
│   ├── submission_exp01_tfidf.csv
│   ├── submission_exp03_mt5small_noprompt.csv
│   ├── submission_exp04_mt5small_prompt.csv
│   ├── submission_exp05_mt5small_5epochs.csv
│   ├── submission_exp06_mt5base.csv
│   └── submission_exp07_mt5base_beamsearch.csv
├── screenshots/
│   └── leaderboard_progression/                    # Zindi leaderboard screenshots
├── report/
│   └── ElHadjiSeck_FinalProject.pdf
└── README.md
```

---

## 🚀 How to Reproduce

### 1. Open in Google Colab
Click the **Open in Colab** badge above, or open `multilingual_health_qa_starter_notebook.ipynb` directly in Colab.

### 2. Set runtime
`Runtime → Change runtime type → T4 GPU`

### 3. Upload data
Upload `Train.csv`, `Test.csv`, `Val.csv`, and `SampleSubmission.csv` (from the Zindi competition Data tab) to the Colab session, or mount Google Drive and adjust the file paths in the data-loading cell.

### 4. Run cells top to bottom
The notebook is organized into clearly labeled sections:
- **Setup & data loading**
- **Preprocessing & cleaning**
- **Experiment 1 — TF-IDF retrieval baseline**
- **Experiment 2 — Zero-shot generation (NLLB-600M)**
- **Experiments 3–7 — Fine-tuning (mT5-small / mT5-base)**, configured via the variables at the top of each fine-tuning section (`MODEL_NAME`, `SAMPLE_FRACTION`, `FINETUNE_EPOCHS`, beam search params)

To reproduce a specific experiment, set the config variables at the top of the fine-tuning section to match the table below, then run from that cell downward.

| Experiment | `MODEL_NAME` | `SAMPLE_FRACTION` | `FINETUNE_EPOCHS` | `num_beams` | `length_penalty` |
|---|---|---|---|---|---|
| 3 | `google/mt5-small` | 0.20 | 3 | 4 | 1.0 |
| 4 | `google/mt5-small` | 0.20 | 3 | 4 | 1.0 |
| 5 | `google/mt5-small` | 0.10 | 5 | 4 | 1.0 |
| 6 | `google/mt5-base` | 0.05 | 3 | 4 | 1.0 |
| 7 | `google/mt5-base` | 0.05 | 3 | 6 | 1.2 |

### 5. Generate submission
The final cell of each experiment section saves a `submission_*.csv` file in the format required by Zindi (`ID, TargetRLF1, TargetR1F1, TargetLLM`, all three columns containing the same predicted answer text).

---

## ⚙️ Environment & Dependencies

Runs on Google Colab's free tier (T4 GPU, ~14.5 GB VRAM). Key libraries:

```
transformers
datasets
torch
scikit-learn
pandas
rouge-score
```

All dependencies are installed via `pip install` cells at the top of the notebook — no separate `requirements.txt` setup is needed for Colab.

**Note on compute constraints:** mT5-base training is slow on a T4 GPU and may not complete an epoch within a typical Colab session at data fractions above ~5–10%. See the report's Discussion (Section 5.3 and 5.7) for details. An A100 GPU (Colab Pro) is recommended for reproducing Experiment 6 at higher data fractions.

---

## 📊 Evaluation Metrics

- **ROUGE-1 F1** — unigram overlap between generated and reference answers
- **ROUGE-L F1** — longest common subsequence overlap, sensitive to word order
- **LLM-as-a-Judge** — semantic accuracy, completeness, and language appropriateness, scored by an LLM and normalized to [0, 1]

Submission files must contain all three target columns (`TargetRLF1`, `TargetR1F1`, `TargetLLM`) populated with the same predicted answer text; Zindi computes the differentiated metrics server-side.

---

## 🧠 AI Tool Usage Disclosure

In accordance with the course's academic integrity policy: AI tools (Claude) were used to support debugging of CUDA out-of-memory errors, drafting of training configuration code, and structuring/drafting of the written report. All experimental design decisions, execution, result interpretation, and analysis are the author's own. The author can explain and defend all code, results, and design decisions in this repository.

---

