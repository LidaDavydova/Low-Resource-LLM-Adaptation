# Cross-Lingual Adaptation of RoBERTa-Base to Swahili

This project explores efficient strategies for adapting an English-pretrained **RoBERTa-base** model to **Swahili**, specifically targeting extreme low-resource scenarios (10k sentences). We evaluate the impact of tokenizer efficiency, parameter-efficient fine-tuning (LoRA), and knowledge retention.

## Research Poster
Detailed visual summary of the experimental setup, tokenizer efficiency gaps, and zero-shot results.

![Project Poster](./assets/poster.pdf)

## Methodology Overview

The project follows a multi-stage adaptation pipeline:
1.  **Source Pretraining:** Initial MLM training on **100k English sentences** (Wikitext-103).
2.  **Vocabulary Re-tokenization:** Replacement of the English BPE with a custom **Swahili Byte-Level BPE** ($V=12\text{k}$), reducing fertility by **73.4%**.
3.  **Embedding Remapping:** Initialization of Swahili embeddings via subword mean-pooling: $E_{new} = \text{mean}(E_{old})$.
4.  **LoRA Adaptation:** Parameter-efficient fine-tuning ($r=16$) on the attention layers while freezing the transformer backbone.

## Evaluation Framework

The model is assessed across three dimensions:
* **Perplexity (PPL):** Swahili modeling performance vs. English retention (Wikitext-103).
* **Morphological Reconstruction:** Accuracy in predicting masked tense/subject markers within agglutinative Swahili verbs.
* **Zero-Shot Transfer:** Sentiment classification on the **AfriSenti** dataset using anchor-based **Log-Sum-Exp (LSE) scoring**.

## Results Summary

| Strategy | Swahili PPL | English PPL | Zero-Shot F1 | Trainable Params |
| :--- | :--- | :--- | :--- | :--- |
| **Method 1: CPT** | 13.4* | **6.16** | 0.16 | 100% |
| **Method 2: Full FT** | 371.0 | 1200.0+ | 0.17 | 100% |
| **Method 3: LoRA** | **321.0** | **407.0** | **0.26** | **0.65%** |

*\*Note: CPT/Base PPL is artificially low due to high subword fragmentation.*

## Replication & Models

### **Trained Models**
All trained model checkpoints, including the base English model and adapted Swahili versions, are available here:
[**Google Drive: Swahili-RoBERTa-Models**](https://drive.google.com/drive/folders/1AFibush0CIs-A04hm91ALx4yY-SGthrj?usp=sharing)

### **Execution Pipeline**
To replicate the results, run the following notebooks/scripts in order:
1.  `train_en`: Initial pretraining on English Wikipedia.
2.  `swahili_cont_pretrain.ipynb`: Continued pretraining (CPT) baseline.
3.  `swahili_retokenize.ipynb`: Implementation of the custom Swahili tokenizer and embedding remapping.
4.  `swahili_adapter.ipynb`: Parameter-efficient adaptation using LoRA.
5.  `eval notebooks`: Comprehensive evaluation across PPL, Morphology, and Zero-Shot tasks.

## Key Findings
* **Tokenizer Dominance:** Custom BPE decreased fertility from **5.38** to **1.43** tokens/word, resolving the information bottleneck.
* **Retention:** LoRA prevents **catastrophic forgetting**, maintaining cross-lingual priors that facilitate superior zero-shot performance.
* **Optimal Pipeline:** $\text{Retokenization} \rightarrow \text{Embedding Remapping} \rightarrow \text{LoRA Adaptation}$ is the recommended path for low-resource transfer.
