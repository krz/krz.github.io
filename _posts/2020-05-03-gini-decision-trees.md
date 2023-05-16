---
layout: post
title: Decision Trees and Gini Impurity
categories: [r, machine learning]
---

Decision Trees are versatile Machine Learning algorithms that can perform both classification and regression tasks. In this post, we will learn how they use different cost functions to measure their performance depending on the type of problem they are solving.

### Classification
To understand the cost function for classification task, we first have to understand Gini impurity.
Gini impurity is a measure of how often a randomly chosen element from a set would be incorrectly labeled if it was randomly labeled according to the distribution of labels in the set. 
It is used to evaluate the quality of a split in a decision tree. A lower gini impurity means a more homogeneous or pure node, where most of the elements belong to the same class.
The formula for gini impurity is:

$$
G = 1-\sum_{i=1}^n p_1^2
$$

where \\(n\\) is the number of classes and \\(p_i\\) is the probability of an element belonging to class \((i\)).
For example, if we have a node with 10 red balls and 10 blue balls, the gini impurity of that node is:

$$
G = 1- (0.5^2 + 0.5^2) = 0.5.
$$

This is the highest possible gini impurity, meaning the node is very impure or mixed.

If we have a node with 15 red balls and 5 blue balls, the gini impurity of that node is:

$$
G = 1- (0.75^2 + 0.25^2) = 0.375.
$$

This is lower than the previous node, meaning the node is more pure or less mixed.

If we have a node with 20 red balls and 0 blue balls, the gini impurity of that node is:

$$
G = 1- (1^2 + 0^2) = 0.
$$

This is the lowest possible gini impurity, meaning the node is perfectly pure or homogeneous.

When splitting a node into two or more sub-nodes, we want to choose the split that minimizes the weighted average of the gini impurities of the sub-nodes. 
This means we are looking for the split that creates the most pure or homogeneous sub-nodes.

Let's illustrate this with a real example. We use the `stagec` data set with two classes, `Yes` and `No` in the `rpart` package for decision trees.
We simplicity we set a `maxdepth=2` of the tree.

```r
library(rpart)
library(rpart.plot)

progstat <- factor(stagec$pgstat, levels = 0:1, labels = c("No", "Yes"))
cfit <- rpart(progstat ~ age + eet + g2 + grade + gleason + ploidy,
              data = stagec, method = 'class', control = list(maxdepth=2))

rpart.plot(cfit)
```
![](/images/rpart.png)

In this example, the first split is at the `grade` variable. It its value is below 3, it splits to to the left side the tree and classifies to `No`.
42% of the data, or 61 observations split to this left side.
The Gini impurity for this node at depth 0 is: \\(1- (\frac{61}{146})^2 - (\frac{85}{146})^2 =0.469\\).
The second split is at depth 1 at the `g2` variable. If it's below 13 it splits to the left and classifies to `No`, otherwise `Yes`.
The Gini impurity for this node is: \\( 1- (\frac{40}{85}^2)-(\frac{45}{85})^2=0.498\\).

Decision trees use gini impurity as a cost function for classification tasks to measure how well a node can classify the samples based on the given features. 
The lower the gini impurity, the better the node can separate the samples into their correct classes.

When splitting a node into two or more sub-nodes, the decision tree algorithm tries to find the feature and the threshold that minimize the weighted average of the gini impurities of the sub-nodes. 
This means that the algorithm is looking for the split that creates the most pure or homogeneous sub-nodes with respect to the target classes.

The cost function for a split is calculated as:

$$
C = \sum_{k=1}^m \frac{n_k}{n}G_k,
$$

where \\(m\\) is the number of sub-nodes, \\(n_k\\) is the number of samples in sub-node \\(k\\), \\(n\\) is the total number of samples in the parent node,
and \\(G_k\\) is the gini impurity of sub-node \\(k\\).
The decision tree algorithm will compare different splits and choose the one that has the lowest cost function value. This way, it will build a tree that can classify the samples with high accuracy.

### Regression
For regression tasks, the cost function that is minimized to choose split points is the sum squared error across all training samples that fall within the node. The sum squared error is calculated as:

$$
RSS=\sum_{i=1}^n(y_i -\hat y_i)
$$






