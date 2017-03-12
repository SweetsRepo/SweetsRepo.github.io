---
layout: post
title: Generative Adversarial Networks: First Results
date: 2017-03-08T17:16:00.000Z
categories: update
---

#Generative Adversarial Networks

Generative Adversarial Networks have been a personal research topic of mine
throughout the semester and today I have gotten a usuable output for the first
time. This has not only given me satisfaction as one often feels when their 
code works, but also insight into my future developments. But before getting
too far ahead of myself, let me walk you through some of the steps I've taken.

##High-Level Goal:

To be able to produce a generated sample which mimics an input dataset with a 
high level of fidelity. Furthermore this should be accomplished using a 
neural network architecture which may be scaled to n dimensional input 
feature sets.

##Methodology:

In order to accomplish the above goals a generative adversarial network was 
constructed to approximate input dataset's PDF and use it to generate new 
samples. If you are interested in learning more about generative adversarial
networks I highly recommend reading some of my older posts or doing a quick
Google Search.

The network architecture can be broken up into 2 parts; the Generator and the
Discriminator. 

The generator is responsible for creating new data, and uses a 
simple 3-layer neural network with a softplus activation layer to help expand
input values into real-valued data. Some pertinent code snippets are shown below:

```python
def generator(self, seed):
    """
    Generates synthetic data through a series of transforms
    :param seed: Input seed for data
    :return: Synthetic Data
    """
    W1 = weight_variable([self.feature_size, self.h_dim], name='g_w1')
    b1 = bias_variable([self.h_dim], name='g_b1')
    W2 = weight_variable([self.h_dim, self.feature_size], name='g_w3')
    b2 = bias_variable([self.feature_size], name='g_b3')
    #Compute the Forward Pass
    h1 = tf.matmul(seed, W1) + b1
    a1 = tf.nn.softplus(h1)
    y = tf.matmul(a1, W2) + b2
    return y

```

The discriminator is responsible for differentiating between data which belongs
to the original dataset and the generated dataset. The architecture used was a 
4 layer neural network with rectified linear activations on all but the output
layer. The output layer uses the sigmoid function to bound output values 
between 0 and 1. Once again, pertinent code is shown below:

```python
def discriminator(self, data, reuse=False):
    """
    Discriminates between real and synthetic data
    :param data: Input data to discriminate
    :param reuse: (Optional) Used when Trained Variables must be reused
    :return: Discriminated values
    """
    if reuse:
       tf.get_variable_scope().reuse_variables()
    W1 = weight_variable([self.feature_size, self.h_dim], name='d_w1')
    b1 = bias_variable([self.h_dim], name='d_b1')
    W2 = weight_variable([self.h_dim, self.h_dim], name='d_w2')
    b2 = bias_variable([self.h_dim], name='d_b2')
    W3 = weight_variable([self.h_dim, self.feature_size], name='d_w3')
    b3 = bias_variable([self.feature_size], name='d_b3')
    #Compute the Forward Pass
    h1 = tf.matmul(data, W1) + b1
    a1 = tf.nn.relu(h1)
    h2 = tf.matmul(a1, W2) + b2
    a2 = tf.nn.relu(h2)
    y = tf.matmul(a2, W3) + b3
    return tf.sigmoid(y)

```

With the architectural definitions complete a training procedure was defined.
First, the Discriminator needed to be pretrained on a subset of the data in 
order to provide useful gradients when undergoing adversarial training. This
was accomplished using the code below:

```python
for i in range(10000):
    mini_d = (np.random.random(con['batch_size']))
    #Should be an estimate of the mean and std of the dataset
    mini_d_ = norm.pdf(mini_d, loc=0, scale=1)
    _, summary_str = self.sess.run([pre_opt, self.pre_sum], feed_dict={
       self.pre: np.reshape(mini_d, (con['batch_size'], 1)),
       self.pre_: np.reshape(mini_d_, (con['batch_size'], 1))})
    if(i % 100 == 0):
       pre_loss_eval = self.loss_pre.eval(feed_dict={self.pre: np.reshape(mini_d, (con['batch_size'], 1)),
       self.pre_: np.reshape(mini_d_, (con['batch_size'], 1))})
       print("Step %d\n Discriminator: %g" % (i, pre_loss_eval))

```

Note that this dataset can be approximated by a normal distribution, hence the
usage of it here.

After pretraining the Generator and Discriminator undergo Adversarial Training.
The real interest in this lies in the loss function used for each topology. 
The loss functions can be thought of as a maximin problem, as the generator
attempts to maximize the fidelity of it's output values and the discriminator
attempts to minimize misclassifications.

```python
self.loss_d = tf.reduce_mean(-tf.log(self.D) - tf.log(1 - self.D_))
self.loss_g = tf.reduce_mean(-tf.log(self.D_))

```

##Results
Training the network takes around 2 minutes when using 10000 pretraining samples
and 100000 adversarial samples with a batch size of 50. Once trained the network
was used to predict values along the input distribution as shown below:

<img src="/images/fulls/03.png" class="fit image">

Note that this is accomplished by feeding a set of linearly space values along
the range of interest (RoI).The true distribution is shown by the green line, 
while the generated distribution is shown by the blue line. The discrepancy 
occurs because of the low feedback on relatively rare values along the edges of
the distribution. In future work this should be correctable by implementing
mini-batch discrimination.

##Future Work
With this toy model complete the next goal I have set is to scale the design
for multi-feature datasets. The end goal is to be able to take in arbitrary
datasets with arbitrary feature sets and create useful data. This should help 
provide insight into fields where data collection is difficult/rare.

