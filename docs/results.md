## Sparse attention sometimes reduces memory usage

To understand how sparse-attention affected the memory usage of the model, we increased the input sequence length from 131,072 bp to 786,432 bp (Figure 1). To clarify how much of the memory usage is due to the different attention methods (and other components of the transformer layers), we also measured the memory usage when just one transformer layer is used (Figure 1). The difference between the memory usage when using one transformer layer versus the original eleven provides an estimate of the memory required for each additional transformer layer (Figure 2).

### Figure 1: Memory usage increases with DNA sequence length (11 vs 1 transformer layers)
<iframe width="900" height="375" frameborder="0" scrolling="no" src="//plotly.com/~Currie32/30.embed?showlink=false"></iframe>

The results when using eleven transformer layers show a clear trend that the memory usage for self-attention scales exponentially compared to the linear increase for sparse-attention. With sparse-attention, the model can handle sequence lengths of 786,432bp, while the model runs out of memory (>24gb) when using self-attention and a sequence length of 589,824. The average memory usage per transformer layer provides further evidence for the difference in requirements between the two methods (Figure 2). Each transformer layer when using sparse-attention requires less memory with a sequence length of 786,432 than when using self-attention with a sequence length of 393,216.

### Figure 2: Average memory usage depending on sequence length
<iframe width="900" height="400" frameborder="0" scrolling="no" src="//plotly.com/~Currie32/35.embed?showlink=false"></iframe>

Despite the benefits of using sparse-attention with longer sequences, they have a cost when using shorter sequence lengths. Until the sequence length reaches approximately 300,000bp, self-attention uses less memory than sparse-attention (Figure 1).

## Slower training

At many of our analysed sequence lengths, training a model using sparse-attention is slower than using self-attention (Figure 3). This was true whether one or eleven transformer layers were used in the model. This result is consistent with other sparse-attention models, including Performer (Choromanski et al. 2021) and Reformer (Kitaev, Kaiser, and Levskaya 2020). This suggests that the method these models use to achieve linear memory dependency has a computational cost until the input is of a sufficient size.

### Figure 3: Slower training with sparse-attention
<iframe width="900" height="300" frameborder="0" scrolling="no" src="//plotly.com/~Currie32/38.embed?showlink=false"></iframe>

The Long-Range Arena benchmark (Tay et al. 2020), which compares memory-efficient Transformers against the vanilla Transformer in a range of tasks, provides further evidence of the computational speed of BigBird’s sparse-attention mechanism. BigBird trained slower than the vanilla Transformer until the input sequence length was greater than 3,000. Compared to the other memory efficient Transformers, it was the second slowest (out of eight) to train (Tay et al. 2020).

## Accuracy results could not be reproduced

To confirm the performance Enformer, we ran the model evaluation code (see the evaluate_model function), but altered the code to obtain the Pearson correlation for the four types of genome-wide tracks: CAGE measuring transcriptional activity, histone modifications, TF binding, and DNA accessibility. Each testing example has its corresponding track listed in Supplementary Table 2.

Our results did not match those from Figure 1c (Avsec, Agarwal, et al. 2021) and it is unclear why this is the case. The authors of the paper were contacted about this matter, but they could not provide an explanation for the difference. Nonetheless, our calculated results for the Enformer model will be used as the primary comparison against the S-Enformer results.

## Achieving accuracy parity requires training the full model

We attempted to achieve accuracy parity with Enformer by training the model, but only updating the weights in the sparse-attention layers. This method is similar to fine-tuning as all of the weights were frozen except for those in the new layers. The benefit of this method is that it could save a significant amount of time and computational resources compared to retraining the model from scratch. Enformer was trained on 64 TPU v3 cores with a batch size of 64 (1 per core) for 150,000 steps over approximately 3 days (Avsec, Agarwal, et al. 2021). Reducing this training process to more modest levels could help to make updating state-of-the-art models more accessible. However, after training the model for XXX steps, we were not able to achieve the same Pearson correlation values as Enformer (Figure 2a).

### Figure 3: Performance parity could not be achieved
<iframe width="950" height="350" frameborder="0" scrolling="no" src="//plotly.com/~Currie32/33.embed"></iframe>

Since much of S-Enformer was pretrained when we began retraining it, it quickly achieves non-trivial performance. Progress quickly slows until the gains for further training appear to plateau at the XXX step (Figure 2b). It is possible that using different training parameters and further training steps could result in performance parity, but due to time limitations and parameter optimization not being the primary focus of this work, these possibilities were not explored.

## Sparse attention still uses the entire receptive field

The receptive field of the model was measured to confirm that no reduction was made by replacing the self-attention layers with sparse-attention. The results in Figure 3 illustrate that the model’s receptive field still encompasses the entire input sequence. This means that a change in nucleotide at any base will result in a change in the predicted gene expression across an entire track.

There is a noticeable difference in how a mutation affects the predicted expression based on attention type. When a mutation is at the start or end of the input sequence, sparse-attention predicts a greater and more uniform change in gene expression than self-attention. The change in predicted expression by self-attention is skewed towards the mutation’s end of the sequence. When the mutation is towards the centre of the input sequence, both models have a visible peak where the change in predicted gene expression is most significant. A peak’s location in a genomic track is correlated with the mutation’s location in the input sequence. Sparse-attention differs from self-attention as its peak is greater and there is a more significant drop-off in the predicted change in gene expression as the distance increases from the peak.
