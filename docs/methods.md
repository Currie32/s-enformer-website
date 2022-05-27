## Model architecture

The S-Enformer architecture is a combination of the Enformer (Avsec, Agarwal, et al. 2021) and BigBird (Zaheer et al. 2021) models. At a high level, S-Enformer is composed of the six parts (Figure 4): (1: Stem) a convolution block with attention pooling, (2: Convolutional tower) 6 convolutional blocks with batch normalisation and attention pooling, (3: Transformers) 11 transformer blocks with layer normalisation, sparse attention, and dropout, (4: Cropping) a cropping layer, (5: Pointwise convolution) a pointwise convolution with dropout and a Gaussian error linear unit (GELU) activation function, and (6: Heads) a network head for each organism consisting of a dense layer and softplus activation function. Tensorflow v2.4.1 (TensorFlow Developers 2022) and Sonnet v2.0.0 (Sonnet Developers 2020) were the primary libraries for developing the model.

The input to S-Enformer is a 3-D matrix with dimensions for batch size, sequence length, and nucleotides. The batch size was set to 5, but it can vary based on the training requirements, the sequence length is always 196,608 base pairs (bp), and there are 4 nucleotides (corresponding to A = [1,0,0,0], C = [0,1,0,0], G = [0,0,1,0], T = [0,0,0,1], N = [0,0,0,0]). When an input sequence is fed into S-Enformer, it is first reduced in length to 1,536 by the attention pooling in the stem and convolutional tower. Then, distal interactions within the sequence are learned by the transformer blocks. The cropping layer removes 320 positions at both ends of the sequence as the Enformer model could not share information to these edges from the opposite half of the sequence. It is possible for this layer to be removed so that more nodes connect with the pointwise convolutional layer and potentially increase the accuracy of the model, but changing the model architecture this significantly was outside the scope of this work. In the last section of the model, it predicts the genomic tracks for the relevant organism. There are 5,313 tracks for humans and 1,643 for mice, each of length 896, which correspond to a DNA sequence length of 114,688-bp aggregated into 128-bp bins.

### Sparse attention

The multi-head attention (MHA) layers are what allow the transformer blocks to learn the relationship between DNA sequence and expression levels. At the same time, it is the sparse-attention mechanism that allows the model to learn this relationship with a linear (as opposed to quadratic) dependency on memory. There are three components to sparse attention: (1) random attention, (2) sliding attention, and (3) global attention (Figure 5).

Random attention is when a subset of tokens attend to other tokens. This allows information to be shared across the network, but without the memory requirements of full attention. 192 tokens (of the 1,536) will be randomly attended to in each layer.

Sliding attention makes use of the locality of reference. In a DNA sequence (and NLP in general), a significant amount of information can be learned about a token from its neighbouring tokens (Zaheer et al. 2021). In S-Enformer, a token will attend to 64 tokens to the left and right of itself to capture the local information.

Global attention is when a token is attended to by all other tokens in a sequence. For S-Enformer, global attention is used by the first and last 64 tokens of a sequence. In comparison, all tokens use global attention in Enformer, therefore it can be another term for self-attention. The benefit of global attention is that it can help information to move much faster in a network than relying on just sliding or random attention, as global tokens are connected with all other tokens.

An issue with this attention is that sparse multiplication is inefficient when training a model on a GPU (Gale et al. 2020; Yao et al. 2019). To overcome this, query tokens and key tokens are grouped together into attention blocks (instead of being more uniformly distributed). This allows matrix multiplication to be used, rather than a gather operation, which is much faster. Since sparse attention is being used, the time complexity is linear, O(nbd) (n = number of tokens, b = block size, d = number of tokens to attend to), instead of quadratic, O(n2d), for self-attention (Zaheer et al. 2021). This results in far fewer than half of nodes being attended to in each layer using sparse-attention, compared to all nodes attending to all others in each layer using self-attention.

## Modelling data

S-Enformer was trained, validated, and tested using the same data as Enformer (Avsec, Agarwal, et al. 2021). There are 34,021 training, 2,213 validation, and 1,937 test sequences for the human genome, and 29,295 training, 2,209 validation, and 2,017 test sequences for the mouse genome. For the human genome, each example contains 2,131 transcription factor (TF) chromatin immunoprecipitation and sequencing (ChIP–seq), 1,860 histone modification ChIP–seq, 684 DNase-seq or ATAC-seq, and 638 CAGE tracks (totalling to 5,313). For the mouse genome, each example contains 308 TF ChIP–seq, 750 histone modification ChIP–seq, 228 DNase-seq or ATAC-seq, and 357 CAGE tracks (totalling to 1,643).

## Model training

The model was trained for 20,000 steps using a batch size of 5 on a single RTX 6000 GPU (approx time). The Adam optimizer with a learning rate of 0.0001 was used to train the model, which is less than the final learning rate of 0.0005 for Enformer. The higher rate used by Enformer resulted in lower Pearson correlation values for S-Enformer on the training and validation data. The same architecture parameters were used as Enformer, specifically 1,536 channels, 8 attention heads, and 11 transformer blocks.

A pre-trained Enformer model, shared by its authors, was used when comparing the performance against S-Enformer.

## Accuracy evaluation

Only the human testing dataset was used to compare the performance between the two models. The mouse testing data was omitted as the human data was sufficient to make a fair comparison. The Pearson correlation coefficient was reported for each of the four genomic tracks: (1) CAGE, (2) histone modifications, (3) TF binding, and (4) DNA accessibility using DNase and ATAC-seq. Supplementary Table 2 from the Enformer paper was used to label the track type within each of the 5,313 testing examples. The mean Pearson correlation coefficient was reported for each track type to compare the performance between Enformer and S-Enformer.

## Memory usage and training speed

A model’s memory usage and training speed was measured across 10 training steps using a batch size of 1 on an RTX 6000 GPU. The peak memory usage was reported using the tf.config.experimental.get_memory_info function. After each training step, the memory stats were reset so that an average could be taken across the first 10 steps. Measuring the training time began just before the first training step and concluded once the tenth training step had finished.

## Receptive field

To measure the receptive field for a model we: (1) took a random sequence of DNA (length = 196,608), (2) predicted the expression across all 5,313 genomic tracks, (3) randomly changed a single base at a predefined location (such as the first nucleotide), (4) again predicted the expression across all 5,313 genomic tracks, (5) calculated the difference in expression between the two sets of predictions, (6) repeated steps 1-5 ten times, and (7) averaged the change in expression for the 896 position across the 5,313 genomic tracks. These seven steps were repeated nine times, each using a different mutation location. The nine locations were equally spaced across the sequence of DNA.
