---
layout: post
title: Linear regression in Pytorch and Tensorflow
categories: [python, machine learning]
---

Regression is a statistical technique that helps you to estimate the relationship between a dependent variable and one or more independent variables.
It is basically the simplest machine learning algorithm you can imagine.
For example, you can use regression to find out how the price of a house depends on its size, location, number of rooms, etc.

There are different types of regression, such as linear regression, logistic regression, nonlinear regression, etc. 
Each type has its own assumptions and methods of fitting the data. 
In this article, I will focus on simple linear regression, which is the simplest and most common type of regression.

Simple linear regression assumes that there is a linear relationship between the dependent variable and the independent variable, i.e., 
the data points can be approximated by a straight line1. The equation of this line is:

$$
y = \beta_o + \beta_1 x + \varepsilon
$$

where:

* \\(y\\) is the dependent variable
* \\(x\\) is the independent variable
* \\(\beta_0\\) is the intercept term, i.e. the value of \\(y\\) when \\(x\\) is zero
* \\(beta_1\\) is the slope term, i.e., the change in \\(y\\) when \\(x\\) changes by one unit
* \\(\varepsilon\\) is the error term, i.e. the difference between the actual and predicted values of \\(y\\)

The goal of simple linear regression is to find the values of \\(\beta_0\\) and \\(\beta_1\\) that minimize the sum of squared errors (SSE), 
i.e., the sum of the squares of ϵ for all data points. 
This can be done using various methods, such as ordinary least squares (OLS), gradient descent, etc.
Here, we will use the two deep learning libraries `pytorch` and `tensorflow`.

Pytorch has the `torch.nn.Linear()` module, also known as a feed-forward layer or fully connected layer, for this task.
This layer implements a matrix multiplication between an input \\(x\\) and a weights matrix \\(W\\):

$$
y = x\cdot W^T + b
$$

Here, \\(b\\) would be the bias (same as \\(\varepsilon\\) above), and \\(y\\) is the outcome.
In neural networks, the weight matrix \\(A\\) is usually initialized randomly and gets adjusted as the networks learns to better represent patterns in the data.

First, let's do some matrix multiplication. This is how it works:


In torch, let's define two tensors (matrices) `A` and `B`:
```python
import torch

A = torch.tensor([[1, 2, 1],
                  [0, 1, 0],
                  [2, 3, 4]])

B = torch.tensor([[2, 5],
                  [6, 7],
                  [1, 8]])
```

Matrix multiplication (aka the dot product) can be done in torch in several ways:

```python
X @ W
# tensor([[15, 27],
#         [ 6,  7],
#         [26, 63]])

torch.matmul(X, W)
# tensor([[15, 27],
#         [ 6,  7],
#         [26, 63]])

torch.mm(X, W)
# tensor([[15, 27],
#         [ 6,  7],
#         [26, 63]])
```
