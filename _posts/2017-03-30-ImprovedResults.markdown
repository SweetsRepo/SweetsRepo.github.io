---
layout: post
title: Generative Adversarial Networks - Improved Results
date: 2017-03-30T17:16:00.000Z
categories: update
---

# Minibatch Discrimination Goal

To improve the fidelity of generated samples from a Deep Neural Network through
statistical inference. Namely, the definition of a kernel function which calculates
and sums the row-wise gradient for a minibatch of samples. This results in low
entropy points providing feedback within the minibatch, removing previously 
seen vertical asymptotes along the edges of the dataset distribution.

## Methodology:

Minibatch discrimination allows low entropy data points to provide useful gradient
feedback to the model during training. This is accomplished as follows:

Specify a number of kernels and their dimensions to target. Multiply the choosen
hidden layer by a Weight Matrix of dimensions n_kernels X k_dim. Then calculate
the L1 distance between adjacent rows and apply a negative exponential to the results.
Sum the values and append them to the end of the original dataset. Now when the 
classifier learns the parameters for the selected layer, there is data from the 
entire minibatch to take into consideration, as well as the value given by the 
sample. One item to note is that large minibatches may cause exploding gradients
when using minibatch sampling, therefore batch size may need to be tuned to 
provide better results.


## Results:

When performing minibatch discrimination, 5 epochs across a dataset of 1000000
points were completed. By using such a large number of datapoints the ground
truth curve was smoothed, as compared to earlier results. A batch size of 50
was used, with 5 kernel functions applied to each batchset. 

Below are the results of a normal distribution with and without minbatch discrimination.

<img src="/images/fulls/04.png" class="fit image" style = "float: right;">
<img src="/images/fulls/03.png" class="fit image" style = "float: left;">

## Conclusion:

Sadly I don't have much more time to write now, but in the future I intend to
begin applying the lessons learned from this experiment to real datasets.

