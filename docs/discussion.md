## Sparse-attention should lead to more accurate predictions

Deep learning models have made significant gains in predicting gene expression from a sequence of DNA. A key limitation of these models is that they still lack the ability to use long enough sequences to include all regulatory elements in their predictions. By replacing the self-attention mechanism from the Enformer model with BigBird’s sparse-attention, we have taken one step closer to overcoming this obstacle. Although we lacked the time and computational resources to train the model from scratch and demonstrate that using sparse-attention and a longer input sequence will achieve more accurate results, we are confident that this will be the case. This confidence is based on the state-of-the-art results achieved by BigBird[^1]<sup>,</sup>[^2] from its novel architecture and earlier models scoring greater accuracy through the use of longer input sequences[^3]<sup>,</sup>[^4]<sup>,</sup>[^5].

## Limitations to model training

A possible explanation for not achieving accuracy parity with Enformer is the constraint placed on the model during training. When a model is being trained from scratch, all of its weights update in order to reduce the loss. This allows the model to make the best use of its architecture. When training S-Enformer, the model could not update in a cohesive way as all of the weights were frozen except for those in the sparse-attention layers. The sparse-attention layers’ weights were forced to update in a constrained environment and possibly not make optimal use of their design. Given that the self-attention layers have a different architecture to the sparse-attention layers, it might not be possible for the model to achieve its optimal performance when only the sparse-attention layer weights can update.

## Parameter optimisation could improve results

Optimising S-Enformer’s training parameters and architecture were not a primary focus of this work. Therefore, there are potential gains to be made on our results.

The learning rate was held constant at 0.0001 during training. Alternative rates, or starting with a higher rate and decaying it, could result in faster training and improved performance.

The default model architecture values were used to train S-Enformer. With respect to the parameters for the sparse-attention layers, values that could be changed include the block size, number of random blocks, number of sliding blocks, and number of global blocks. Each of these values will affect the amount of attention used by the model. Given the complexities of this task, it is possible that the model is not attending to enough of the data and increasing some of these values could be beneficial.

## More transformer layers may be required

In a self-attention layer, all nodes are connected with each other. This allows information to easily be shared across the neural network. In BigBird’s sparse-attention, not all nodes are connected within a layer. For two unconnected nodes in a layer to share information, they must pass information via their connections with nodes in other layers. Given the large input sequence length, more layers than the currently used eleven could be required to achieve accuracy parity with Enformer. As a default, Bigbird uses sixteen transformer layers, which suggests that more layers might be required for optimal performance[^1]. Depending on the number of additional transformer layers required to match Enformer’s performance, the benefits of sparse-attention could deteriorate.

## Increase prediction resolution

Enformer was designed to predict human and mouse genomic tracks at 128 bp resolution. Depending on the task, it could be beneficial to have a more precise resolution, such as to a single base. BPNet is one such model that was designed for nucleotide-resolution prediction[^6]. Differing from S-Enformer, BPNet was trained with single-cell data, instead of bulk sequencing data. Its input sequence length is 2,068 bp, which is longer than if Enformer was scaled down to the same resolution (1,538 = 196,608 / 128). However, Enformer should be able to use a longer input sequence than 1,538 bp since shorter sequences use less memory. Nonetheless, using S-Enformer’s architecture should allow for a much longer input sequence. This is because BPNet[^6] uses dilated convolutional layers, which were also used by Basenji[^4], the prior work to Enformer. Enformer has already demonstrated that using transformers instead of dilated convolutional layers allows for a longer input sequence length[^3]. The addition of sparse-attention should further these gains.

A nucleotide-resolution model could be applied to GWAS to help identify the causal variant(s) for a disease or trait. This is due to the unidirectional flow of information in a model, meaning that a mutation in the input DNA sequence is the single reason for a change in the predicted gene expression. Contrary to GWAS, where there is only an association between a mutation and disease or trait. Therefore, the high-resolution predictions of a model can complement the wide search space of GWAS to better identify nucleotides of interest.

## Alternative sparse-attention methods

There are multiple other sparse-attention methods that are worth exploring[^2]. Two notable ones are the Linear Transformer[^7] and Performer[^8]. Both of these models train faster and have a lower peak memory usage than BigBird[^2]. Since Enformer was trained for three days using 64 TPUs, reducing these training resources is critical for model retraining with sparse-attention to be more accessible. When looking at the average accuracy of predictions, Bigbird is the best performing model, but the more relevant metric is likely the text classification results[^2], as this is the most similar task to predicting gene expression. In the text classification task, both the Linear Transformer and Performer outperform Bigbird, which suggests that they could achieve better results in predicting gene expression[^2].

[^1]: Zaheer, Manzil, Guru Guruganesh, Avinava Dubey, Joshua Ainslie, Chris Alberti, Santiago Ontanon, Philip Pham, et al. 2021. ‘Big Bird: Transformers for Longer Sequences’. ArXiv:2007.14062 [Cs, Stat], January. <a href=http://arxiv.org/abs/2007.14062 target="_blank">http://arxiv.org/abs/2007.14062</a>.
[^2]: Tay, Yi, Mostafa Dehghani, Samira Abnar, Yikang Shen, Dara Bahri, Philip Pham, Jinfeng Rao, Liu Yang, Sebastian Ruder, and Donald Metzler. 2020. ‘Long Range Arena: A Benchmark for Efficient Transformers’. ArXiv:2011.04006 [Cs], November. <a href=http://arxiv.org/abs/2011.04006 target="_blank">http://arxiv.org/abs/2011.04006</a>.
[^3]: Avsec, Žiga, Vikram Agarwal, Daniel Visentin, Joseph R. Ledsam, Agnieszka Grabska-Barwinska, Kyle R. Taylor, Yannis Assael, John Jumper, Pushmeet Kohli, and David R. Kelley. 2021. ‘Effective Gene Expression Prediction from Sequence by Integrating Long-Range Interactions’. Nature Methods 18 (10): 1196–1203. <a href=https://doi.org/10.1038/s41592-021-01252-x target="_blank">https://doi.org/10.1038/s41592-021-01252-x</a>.
[^4]: Kelley, David R., Yakir A. Reshef, Maxwell Bileschi, David Belanger, Cory Y. McLean, and Jasper Snoek. 2018. ‘Sequential Regulatory Activity Prediction across Chromosomes with Convolutional Neural Networks’. Genome Research 28 (5): 739–50. <a href=https://doi.org/10.1101/gr.227819.117 target="_blank">https://doi.org/10.1101/gr.227819.117</a>.
[^5]: Zhou, Jian, Chandra L. Theesfeld, Kevin Yao, Kathleen M. Chen, Aaron K. Wong, and Olga G. Troyanskaya. 2018. ‘Deep Learning Sequence-Based Ab Initio Prediction of Variant Effects on Expression and Disease Risk’. Nature Genetics 50 (8): 1171–79. <a href=https://doi.org/10.1038/s41588-018-0160-6 target="_blank">https://doi.org/10.1038/s41588-018-0160-6</a>.
[^6]: Avsec, Žiga, Melanie Weilert, Avanti Shrikumar, Sabrina Krueger, Amr Alexandari, Khyati Dalal, Robin Fropf, et al. 2021. ‘Base-Resolution Models of Transcription-Factor Binding Reveal Soft Motif Syntax’. Nature Genetics 53 (3): 354–66. <a href=https://doi.org/10.1038/s41588-021-00782-6 target="_blank">https://doi.org/10.1038/s41588-021-00782-6</a>.
[^7]: Katharopoulos, Angelos, Apoorv Vyas, Nikolaos Pappas, and François Fleuret. 2020. ‘Transformers Are RNNs: Fast Autoregressive Transformers with Linear Attention’. ArXiv:2006.16236 [Cs, Stat], August. <a href=http://arxiv.org/abs/2006.16236 target="_blank">http://arxiv.org/abs/2006.16236</a>.
[^8]: Choromanski, Krzysztof, Valerii Likhosherstov, David Dohan, Xingyou Song, Andreea Gane, Tamas Sarlos, Peter Hawkins, et al. 2021. ‘Rethinking Attention with Performers’. ArXiv:2009.14794 [Cs, Stat], March. <a href=http://arxiv.org/abs/2009.14794 target="_blank">http://arxiv.org/abs/2009.14794</a>.