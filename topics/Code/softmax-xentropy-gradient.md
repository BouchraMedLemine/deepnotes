---
layout: default
title: New Topic 
nav_order: 2 
# permalink: /topics/utils/target_execute
parent: Utilities
---

## Insert Topic Here [PyTorch][PYTORCH]
<span style="background-color:LightGreen">
Created : 20/01/2022 | on Linux: 5.4.0-91-generic <br />
Updated: 20/01/2022 | on Linux: 5.4.0-91-generic <br />
Status: Draft
</span>

<span style="background-color:LightYellow"> [**previous topic 1: Starting Development with PyTorch**](../pytorch_walkthrough#Starting-Development-with-PyTorch)  </span> <br  />
<span style="background-color:LightYellow"> [**previous topic 2: Tensors and Data Handling with PyTorch**](../tensors_and_model_input#Tensors-and-Data-Handling-with-PyTorch)  </span>
<span style="background-color:LightYellow"> [**previous topic 3: Building a network in eager mode**](../eager_network_building_blocks#Building-a-network-with-PyTorch)  </span>

## Warning!! This post is under construction! 

### Softmax function 

### Probabilistic and Information Theory perspectives.

### Proof for Softmax Gradient 

### Implementations with python


The code below is taken from my answers to the assignments in [CS231](http://cs231n.stanford.edu/)

```python
from builtins import range
import numpy as np

def softmax_loss_naive(W, X, y, reg):
    """
    Softmax loss function, naive implementation (with loops)

    Inputs have dimension D, there are C classes, and we operate on minibatches
    of N examples.

    Inputs:
    - W: A numpy array of shape (D, C) containing weights.
    - X: A numpy array of shape (N, D) containing a minibatch of data.
    - y: A numpy array of shape (N,) containing training labels; y[i] = c means
      that X[i] has label c, where 0 <= c < C.
    - reg: (float) regularization strength

    Returns a tuple of:
    - loss as single float
    - gradient with respect to weights W; an array of same shape as W
    """
    # Initialize the loss and gradient to zero.
    loss = 0.0
    dW = np.zeros_like(W)

    batch_size = X.shape[0]
    n_features = X.shape[1]
    n_classes  = W.shape[1]

    for i in range(batch_size):

      un_normalised_probs = X[i] @ W
      loss += -un_normalised_probs[y[i]] + np.log(np.sum(np.exp(un_normalised_probs))) 

      # Probabilities with the "log-sum-exp trick"
      un_normalised_probs -= np.max(un_normalised_probs)
      y_hat = np.exp(un_normalised_probs)/np.sum(np.exp(un_normalised_probs))
      
      for k in range(n_classes):
      	# Here we implement the gradient we derived above 
        dW[:, k] += X[i] * (y_hat[k] - (k == y[i]))
   
    loss /= batch_size 
    dW /= batch_size

    loss += reg * np.sum(W*W)   
    dW +=  reg * 2 * W

    return loss, dW

```

We apply the gradient for each column in the `dW` matrix 

```python
for k in range(n_classes):
	# Here we implement the gradient we derived above 
	dW[:, k] += X[i] * (y_hat[k] - (k == y[i]))
```

Here is a faster vectorised version of the code above.  

```python
un_normalised_probs = X @ W
losses = np.log(np.sum(np.exp(un_normalised_probs), axis=1 , keepdims=True)) - np.take_along_axis(un_normalised_probs, np.reshape(y, (-1, 1)), axis=1)
loss   += np.sum(losses)/batch_size + reg *  np.sum(W*W) 

un_normalised_probs -=  np.amax(un_normalised_probs, axis=1, keepdims=True)
y_hats = np.exp(un_normalised_probs) / np.sum(np.exp(un_normalised_probs), axis=1, keepdims=True)
y_hats[np.arange(y.shape[0]), y] -= 1
dW = (X.T @ y_hats)/batch_size + reg * 2 * W
```

<br />

<span style="background-color:LightYellow"> Check the [**next topic**](../pytorch_walkthrough#Starting-Development-with-PyTorch)  </span>

Source: [PyTorch Tutorial][PyTorch-Tutorial]

---
*Click [here][ERRORS-SUGGESTIONS] to report Errors, make Suggestions or Comments!*

[JETSON-URL]: https://developer.nvidia.com/embedded/jetson-agx-xavier-developer-kit
[PYTORCH]: https://pytorch.org
[NVIDIA-PYTORCH-GUIDE]: https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-9-0-now-available/72048
[PyTorch-Tutorial]: https://pytorch.org/tutorials/beginner/basics/quickstart_tutorial.html
[FashonMnist-dataset]: https://github.com/zalandoresearch/fashion-mnist
[ERRORS-SUGGESTIONS]: https://github.com/ganindu7/deepnotes/issues

<!-- Latex in markdown -->
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
<!-- $$ \nabla_\boldsymbol{x} J(\boldsymbol{x}) $$ -->

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.5.0/styles/default.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.5.0/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>

<!-- This is not pretty!! -->
<!-- 
<script src="//cdnjs.cloudflare.com/ajax/libs/highlightjs-line-numbers.js/2.8.0/highlightjs-line-numbers.min.js"></script>
<script>hljs.initLineNumbersOnLoad();</script>
 -->