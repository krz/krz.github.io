---
layout: post
title: Backprogragation from the ground up
categories: [python, machine learning, neural networks]
---

Backpropagation is a popular algorithm used in training neural networks, which allows the network to learn from the input data and improve its performance over time. It is essentially a way to update the weights and biases of the network by propagating errors backwards from the output layer to the input layer.

Neural networks can be thought of as very complicated mathematical functions that take in inputs, perform a series of computations, and output predictions or classifications. They can be extremely complex and difficult to understand, especially for larger networks with many layers and thousands or millions of parameters. However, at a high level, they can be thought of as complicated mathematical functions that learn to map inputs to outputs through a process of trial and error.

For simplicity, let's imagine a very simple function:

$$f(x)=2x^2-3x+4$$

This function can be defined in python like this

```python
def f(x):
    return 2*x**2 - 3*x + 4
```

Plot it:

```python
import numpy as np
import matplotlib.pyplot as plt
xs = np.arange(-3, 4, 0.2)
ys = f(xs)
plt.plot(xs, ys)
```

Let's now talk about derivatives. What does the derivative of a function tell you? If you head over to the [Wikipedia](https://en.wikipedia.org/wiki/Derivative) page of Derivatives it says that *the derivative of a function measures the sensitivity to change of the function value (output value) with respect to a change in its argument (input value).* This means the derivative describes what happens if you add a very small number \( h \) to \(x\), how the function responds and by how much. Does the function go up or down, and how sensitive is it to such changes? This is equivalent to the slope of the tangent line at a given point \( x \). $x$ \$x\$

This means if we know the derivatives, we know how to change the inputs (better: the weights and biases) in a neural network to match a desired output. This is how a neural network is trained.

For simplicity, we illustrate this with the very basic function from above. Please note that in this case we don't have weights and biases, just inputs. In a regular neural network we usually don't change the inputs, just the weights in biases.

We can easily calculate \(y\) a any given point \(x\):

```python
f(2)
# 6
f(6)
# 58
f(-2)
# 18
```

Now let's define a small number \(h\) and observe how the ouput changes:

```python
h = 0.000001
f(2 + h)
# 6.000005000002001
f(6 + h)
# 58.000021000002
f(-2 + h)
# 17.999989000002003
```

We observe, that the first two values, 2 and 6, the output increases, while for the last one with \(x=2\), it decreases. According to the Wikipedia definition, the derivative (or the slope) of \(f\) is the limit of

$$
\lim_{h\rightarrow 0} \frac{f(x+h)-f(x)}{h}
$$
