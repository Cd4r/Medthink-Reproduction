# MedThink: A Rationale-Guided Framework for Explaining Medical VQA (Reproduction)

**Reproduced by:** Hieu Ngo  
**Original Authors:** Xiaotang Gai, Chenyi Zhou, Jiaxiang Liu, Yang Feng, Jian Wu, Zuozhu Liu  

---

## 📌 Project Overview
This repository contains an implementation and reproduction of the MedThink framework. Medical Visual Question Answering (Med-VQA) models typically do not include the reasoning behind their answers, leading to challenges with transparency and interpretability. MedThink solves this by finetuning lightweight pretrained generative models to incorporate medical decision-making rationales.

### Architecture Details
*   **Goal:** Develop a mapping function $f(\cdot)$ represented as $\{A,R\}=f(I,T)$ where $I$ is the medical image, $T$ is the natural language question, $A$ is the predicted answer, and $R$ is the medical decision-making rationale.
*   **Base Framework:** Finetuning a pretrained T5 model.
*   **Core Components:** Features a Textual Encoder, Visual Encoder, Cross Attention Network, Gated Fusion Network, and Textual Decoder. 
*   **Fusion Mechanism:** The Gated Fusion Mechanism dynamically combines textual representations and attention-guided visual features to compute a fusion coefficient through a sigmoid-activated linear combination.

### The Three Generation Strategies
The authors propose three distinct generative modes for Medical Decision-Making Rationales (MDMRs):
1.  **Explanation Strategy:** The answer A is generated first, immediately followed by the rationale R.
2.  **Reasoning Strategy:** The rationale R is generated before the answer A.
3.  **Two-Stage Reasoning:** Phase 1 uses an independent model to generate the intermediate rationale R from the question and image. Phase 2 uses a separate model that takes rationale R alongside the question and image to derive the final answer A.

---

## ⚙️ Reproduction Setup & Environment

### Hardware & Software
*   **Framework:** PyTorch + Hugging Face Transformers
*   **Platform:** Vast.ai cloud VM
*   **GPU:** 1x NVIDIA RTX PRO 5000 Blackwell
*   **Base Model:** UnifiedQA-T5-base with DETR image features

### Dataset Handling
*   **Datasets Reproduced:** R-RAD and R-SLAKE. *(Note: R-PathVQA was skipped due to time constraints)*
*   **Preprocessing:** Skipped broken/missing image-feature samples and logged the skipped counts. 

### Hyperparameters & Training Details
*   **Batch Size:** 32
*   **Base Learning Rate:** 5e-4
*   **Epochs:** 150 (R-RAD), 300 (R-SLAKE)
*   **Two-Stage Phase 2 Details:** Learning rate of 5e-5 for 20 epochs
*   **Seed:** 42
*   **Total Training Time:** ~36 hours for both R-RAD and R-SLAKE combined

---

## 📊 Results Comparison

### Closed-End Accuracy
| Dataset | Strategy | Paper Result | Reproduced Result| Delta |
| :--- | :--- | :--- | :--- | :--- |
| **R-RAD** | w/o Rationale | 79.0% | 77.91% | -1.09 |
| **R-RAD** | Reasoning | 73.9% | 72.87% | -1.03 |
| **R-RAD** | Two-Stage Reasoning | 80.5% | 75.58% | -4.92 |
| **R-RAD** | Explanation | 83.5% | 81.01% | -2.49 |
| **R-SLAKE** | w/o Rationale | 82.5% | 81.70% | -0.80 |
| **R-SLAKE** | Reasoning | 80.8% | 79.95% | -0.85 |
| **R-SLAKE** | Two-Stage Reasoning | 79.1% | 78.45% | -0.65 |
| **R-SLAKE** | Explanation | 86.3% | 85.71% | -0.59 |

### Open-End Metric (BLEU)
> **Note on BLEU Scores:** The difference in open-end metrics may be attributed to using SacreBLEU in this reproduction, whereas the original authors might have utilized BLEU metrics differently.

| Dataset | Strategy | Paper B-1 | Repro B-1 | Paper B-2 | Repro B-2 | Paper B-3 | Repro B-3 | Paper B-4 | Repro B-4 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **R-RAD** | Reasoning | 37.8 | 50.30 | 22.7 | 20.00 | 14.0 | 8.51 | 8.9 | 4.28 |
| **R-RAD** | Two-Stage | 37.7 | 49.27 | 22.5 | 19.61 | 13.9 | 8.28 | 8.8 | 4.21 |
| **R-RAD** | Explanation | 38.3 | 49.54 | 22.9 | 20.01 | 14.0 | 8.82 | 8.8 | 4.53 |
| **R-SLAKE** | Reasoning | 39.5 | 51.86 | 24.3 | 21.79 | 15.5 | 9.95 | 10.0 | 5.31 |
| **R-SLAKE** | Two-Stage | 39.5 | 51.60 | 24.5 | 21.70 | 15.8 | 9.85 | 10.3 | 5.28 |
| **R-SLAKE** | Explanation | 39.2 | 52.01 | 24.1 | 22.01 | 15.4 | 10.15 | 9.9 | 5.56 |
