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
* \\(\varepsilon\\)

The goal of simple linear regression is to find the values of \\(\beta_0\\) and \\(\beta_1\\) that minimize the sum of squared errors (SSE), 
i.e., the sum of the squares of ϵ for all data points2. 
This can be done using various methods, such as ordinary least squares (OLS), gradient descent, etc.
Here, we will use the two deep learning libraries `pytorch` and `tensorflow`.

Pytorch has the `torch.nn.Linear()` also known as a feed-forward layer or fully connected layer, for this task.
This layer implements a matrix multiplication between an input \\(x\\) and a weights matrix \\(W\\):

$$
y = x\cdot W^T + b
$$

Here, \\(b\\)) would be the bias (denoted as \\(\varepsilon\\) above), and \\(y\\) is the outcome.
In neural networks, the weight matrix \\(A\\) is usually initialized randomly and gets adjusted as the networks learns to better represent patterns in the data.

