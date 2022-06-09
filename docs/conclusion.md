## Conclusion

This work has demonstrated the possible benefits of using sparse-attention to predict gene expression levels from a sequence of DNA. Specifically, longer DNA sequences can be used as input to the model, which should achieve more accurate gene expression predictions, while using relatively less memory than if the model used self-attention. Although conclusive results could not be achieved due to time and computational constraints, it is promising that advancements can be made on the current state-of-the-art.

## Future Work

There are a number of promising directions in which to take this work. The first is to confirm if accuracy parity can be achieved when all of the weights in the model are frozen during training except for those in the attention layers. This would be a valuable finding as it would save a significant amount of time and resources compared to training a model from scratch. However, it is uncertain if this is possible given our training results. Assuming this first step fails, the next step would be to confirm that BigBird’s sparse-attention can achieve performance parity with Enformer by training the model from scratch. Given that BigBird’s sparse-attention mechanism is meant to match the performance of self-attention, it is expected that parity will be achieved[^1].

After achieving accuracy parity, the aim is to improve upon these results. Using the maximum possible sequence length as input to the model and training it from scratch should help to improve its gene expression predictions. It would be worthwhile at this point to compare the model with one using a different sparse-attention mechanism. The Linear Transformer[^2] would be an excellent choice as it was the top scoring model in the text classification task from the Long Range Arena benchmark[^3]. Since this model used less memory than BigBird in the benchmark study, it is possible that it can use a longer DNA sequence as input and achieve even better results.

Based on the BigBird and Linear Transformer performance comparison, the better scoring model could be used to improve upon nucleotide-resolution predictions. For example, if the model, after being retrained on the single-cell data, can outperform BPNet[^4] at predicting binding profiles of TFs with only slight changes to its architecture, to account for the differently sized input and output, it would demonstrate its generalisability. The model’s architecture could then be slightly altered to allow it to perform multi-task learning. Given the promise of state-of-the-art results on the 128 bp and nucleotide-resolution tasks, it could score even better results by learning both of these tasks at once with their combined training data.

## Acknowledgements

We thank Alan Murphy and Dr. Nathan Skene for their assistance and guidance throughout this project.

## Code Availability

All the code to reproduce this work is available at:

<a href=https://github.com/neurogenomics/enformer_bigbird target="_blank">https://github.com/neurogenomics/enformer_bigbird</a>

## Data Availability

All the training, validation, and testing data is available at:

<a href=https://console.cloud.google.com/storage/browser/basenji_barnyard/data target="_blank">https://console.cloud.google.com/storage/browser/basenji_barnyard/data</a>

## Ethics Declaration

There are no ethical conflicts for this work. Although human data is used, it is publicly available and non-identifiable.

[^1]: Zaheer, Manzil, Guru Guruganesh, Avinava Dubey, Joshua Ainslie, Chris Alberti, Santiago Ontanon, Philip Pham, et al. 2021. ‘Big Bird: Transformers for Longer Sequences’. ArXiv:2007.14062 [Cs, Stat], January. <a href=http://arxiv.org/abs/2007.14062 target="_blank">http://arxiv.org/abs/2007.14062</a>.
[^2]: Katharopoulos, Angelos, Apoorv Vyas, Nikolaos Pappas, and François Fleuret. 2020. ‘Transformers Are RNNs: Fast Autoregressive Transformers with Linear Attention’. ArXiv:2006.16236 [Cs, Stat], August. <a href=http://arxiv.org/abs/2006.16236 target="_blank">http://arxiv.org/abs/2006.16236</a>.
[^3]: Tay, Yi, Mostafa Dehghani, Samira Abnar, Yikang Shen, Dara Bahri, Philip Pham, Jinfeng Rao, Liu Yang, Sebastian Ruder, and Donald Metzler. 2020. ‘Long Range Arena: A Benchmark for Efficient Transformers’. ArXiv:2011.04006 [Cs], November. <a href=http://arxiv.org/abs/2011.04006 target="_blank">http://arxiv.org/abs/2011.04006</a>.
[^4]: Avsec, Žiga, Melanie Weilert, Avanti Shrikumar, Sabrina Krueger, Amr Alexandari, Khyati Dalal, Robin Fropf, et al. 2021. ‘Base-Resolution Models of Transcription-Factor Binding Reveal Soft Motif Syntax’. Nature Genetics 53 (3): 354–66. <a href=https://doi.org/10.1038/s41588-021-00782-6 target="_blank">https://doi.org/10.1038/s41588-021-00782-6</a>.
