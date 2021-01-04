---
layout: post
title: Why Does Batch Normalization works?
date: 2020-12-26
use_math: true
---
## Introduction
The batch normalization by default is now used in many deep learning models. Batch normalization improves the training of the networks. It is claimed that it does so by handling the internal covariate shift $(ICS)$. ICS is refereed to the phenomena of changes in the distribution of a particular layer's inputs because of the parameters updates in the preceding layer. Batch normalization handles ICS by normalizing each batch with mean and variance, and rescale and shift the normalized input through learnable parameters. 
\section{Does Batch Normalization Really Counters ICS?}
In [1] a detailed analysis has been carried out to show that batch normalization does not counter ICS. In fact it leads to increased ICS. But still it improves the model's training. To demonstrate this they carried out two experiments. In one experiment, non-zero mean and non-unit variance random noise was added after BatchNorm layers so that it leads to sever covariate shift. Authors called this setup as Noisy BatchNorm. Indeed it leads to less stable distribution. However, Noisy BatchNorm almost same as BatchNorm. Also, they both performed better than standard networks (without BatchNorm). 

For second experiment, they extended the notion of ICS. Let $G_{t,i}$ be the gradient of the $i^{th}$ layer that is used to update the parameters simultaneously with all the layers. This is a regular update method. Let $G'_{t,i}$ be the gradient obtained after all the previous layers $(1,2..,i-1)$ have been updated to the new values. ICS is then given as $||G_{t,i}-G'_{t,i}||$. The gradient is used because it is used for training the networks $(first\ order\ method)$ . By using this definition, authors want to highlight the change in optimization landscape of parameters of a layer caused by the changes in the input. If BatchNorm really counters the ICS, it should increase the correlation between $G$ and $G'$. However, it was observed that BatchNorm leads to increase in the ICS as compared to standard network $(no\ BatchNorm)$. At the same time, BatchNorm performed better.

Both of these experiments suggests that BatchNorm doesn't leads to improved training by reducing the ICS.

## Why It Works Then?
The authors identified that the impact of BatchNorm is due to Lipschitzness. They argue that largely  due to BatchNorm  loss function has better effective $\beta$-smoothness or the gradients now are more $\beta$-Lipschitz or the gradients are now more predictive. 

## $\beta$-Lipschitz
A real-valued function $(f)$ is called $\beta$-Lipschitz if:
\begin{equation}
\frac{|f(x_1)-f(x_2|}{|x_1-x_2|}\leq \beta
\end{equation}
This means that rate of change of a $\beta$-Lipschitz function is bounded by $\beta$. Hence, there can not be abrupt changes. As the gradient is now more $\beta$-Lipschitz, it will have stable and predictive changes. This allows network to take step more confidently in a particular direction without facing vanishing or exploding gradients. This in effect improves the training of the networks. This is how BatchNorm improves the training and not by countering the ICS. 

## Is BatchNorm the only way?
Authors also experimented with $l_p$ normalization and found that it also leads to the improved training. They also observed that $l_1$ leads to even better performance than BatchNorm for deep linear networks. 

## Where Else Is Lipschitzness used? 
Lipschitzness has found very interesting applications. Two examples are [Wasserstein GAN](https://arxiv.org/abs/1701.07875) and [Spectral Normalization](https://arxiv.org/abs/1802.05957). 

## References
[1] [How Does Batch Normalization Help Optimization?](https://arxiv.org/abs/1805.11604)
