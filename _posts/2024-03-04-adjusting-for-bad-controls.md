---
layout: post
title: Adjusting for "bad controls"
categories: [r, python]
---

In every statistical analysis, the selection of covariates in regression models is akin to navigating a labyrinth. 
One common heuristic advises controlling for as many variables as possible, under the presumption that it yields more conservative tests of hypotheses. 
However, this approach is not without pitfalls. 
Adding wrong or extraneous variables can distort results, introducing spurious effects and even reversing the signs of actual causal relationships.
In the following, I will provide an example of how this can happen.

Let's consider this example:
![](/images/scm.png)

In this example, we want to estimate the effect of \((X\)) on \((Y\)).
However, examining the model structure reveals a clear __causal independence__ between variables \((X\)) and \((Y\)). There’s no arrow between them, nor is there a directed path that would connect them indirectly. We will now construct four models and investigate the impact of controlling for different variables on the emergence of spurious relationships between \((X\)) and \((Y\)).

1. The first one is a simpel model that regresses \((Y\)) on \((X\)): \((Y \sim X\))
2. Then, we’ll add \((A\)) to this model: \((Y \sim X + A\))
3. Next, we’ll fit a model without \((A\)), but with \((B\)): \((Y \sim X + B\))
4. Finally, we’ll build a model with all four variables: \((Y \sim X + A + B\))
