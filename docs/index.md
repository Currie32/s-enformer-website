## Abstract

It is well established that regulatory sequences in DNA can alter gene expression. It is also known that variants in these regulatory sequences can lead to changes in the level of gene expression. On top of this, variant effects are cell type specific despite DNA being nearly identical in all cells. Understanding this relationship is critical as adverse changes in gene expression can lead to diseases and syndromes such as cancer, autoimmune, neurological disorders, and many others. However, there is not yet a model to accurately map variations in regulatory sequences to changes in gene expression across all human cell and tissue types. Complexities in modelling arise from regulatory elements being located a significant distance from the gene being expressed, non-linear interactions between genomic regions, and regulatory variations in different cell and tissue types. Ab initio deep learning models have made significant advancements on these two fronts in recent years, yet current methods are still limited in their ability to include distal genomic regions due to their model architecture. The current state-of-the-art method, Enformer, uses transformers with a self-attention mechanism that causes a quadratic increase in memory as the sequence length expands. To help maximise the amount of DNA that can be included in the model, we propose replacing self-attention with sparse-attention from the BigBird model. This attention mechanism results in the memory usage increasing linearly with sequence length. This allows for significantly longer DNA sequences to be used in modelling, at the same memory capacity, which should result in more accurate gene expression predictions.

## Lay Summary

Accurate predictions of gene expression can lead to better health outcomes. These predictions can help diagnose a disease and understand its severity, which can assist medical professionals in selecting the most suitable treatment. The challenge of predicting gene expression lies in  the number and location of regulatory elements in DNA. These regulatory elements can be located more than 100,000 bases away from the gene being expressed and multiple elements can simultaneously affect the expression of a gene, leading to complex calculations. Further complications arise from gene regulation differing across cell and tissue types. Nonetheless, with advances in deep learning models and increasingly large datasets, the accuracy of predictions have significantly improved. The trouble is that current methods, such as the state-of-the-art model Enformer, is limited in how much DNA it can use to predict gene expression. Enformer’s memory usage increases significantly with the length of the input DNA sequence due to its model’s architecture. We propose a new model that replaces Enformer’s computationally intensive layers (the attention layers) with those from the BigBird model, which only scale linearly with the length of the input DNA sequence. Using these attention layers allows a longer sequence of DNA to be used by the model, which should lead to more accurate predictions of gene expression.


## Glossary
- <a href=https://jalammar.github.io/illustrated-transformer/ target="_blank">**Attention:**</a> A mechanism used by Transformers that provides more important inputs with greater weight when making predictions.
- **Enformer:** Current state-of-the-art model for predicting gene expression from a sequence of DNA.
- **Genomic track:** A sequence of DNA described by a set of properties.
- **Multi-task learning:** Training a machine learning model on multiple related task at once.
- **Model architecture:** The layers in a deep learning model that transform the input into the expected output.
- <a href=https://theaisummer.com/receptive-field/#:~:text=What%20is%20the%20receptive%20field,the%20input%20region%20(patch) target="_blank">**Receptive field:**</a> The subset of a model’s input that impacts the prediction.
- <a href=https://jalammar.github.io/illustrated-transformer/ target="_blank">**Transformer:**</a> A deep learning model consisting of an encoder and/or decoder, which uses attention to process an input sequence in parallel.

## Abbreviations
- **ATAC-Seq:** Assay of transposase accessible chromatin sequencing
- **bp:** Base pairs
- <a href=https://www.nature.com/articles/nature13182 target="_blank">**CAGE:**</a> Cap analysis of gene expression
- **ChIP:** Chromatin immunoprecipitation and sequencing
- **CNN:** Convolutional neural network
- **DNase-Seq:** Deoxyribonuclease sequencing
- **GELU:** Gaussian error linear unit
- **GWAS:** Genome wide association studies
- **TF:** Transcription factor
- **TPU:** Tensor processing unit

## Word Count

This report contains 4,764 words.
