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

1. The first one is a simple model that regresses \\(Y\\) on \\(X\\): \\(Y \sim X\\)
2. Then, we’ll add \\(A\\) to this model: \\(Y \sim X + A\\)
3. Next, we’ll fit a model without \\(A\\), but \\(B\\): \\(Y \sim X + B\\)
4. Finally, we’ll build a model with all four variables: \\(Y \sim X + A + B\\)

Based on your intuition, which of the four models do you believe will accurately represent the causal independence between \\(X\)) and \\(Y\))?

Let's generate some data:

```python
import numpy as np
import statsmodels.api as sm
np.random.seed(42)

NSAMPLES = 3000

a = np.random.randn(NSAMPLES)
x = 2 * a + np.random.randn(NSAMPLES)
y = 2 * a + np.random.randn(NSAMPLES)
b = 1.7 * x + 0.8 * y
```

### Model 1: \\(Y \sim X\\)

 ```python
X1 = sm.add_constant(x)
model_1 = sm.OLS(y, X1).fit()
```

This is the result:

```python
print(model_1.summary(xname=['const', 'x']))
```

```
OLS Regression Results                            
==============================================================================
Dep. Variable:                      y   R-squared:                       0.630
Model:                            OLS   Adj. R-squared:                  0.630
Method:                 Least Squares   F-statistic:                     5114.
Date:                Mon, 04 Mar 2024   Prob (F-statistic):               0.00
Time:                        12:59:41   Log-Likelihood:                -5173.1
No. Observations:                3000   AIC:                         1.035e+04
Df Residuals:                    2998   BIC:                         1.036e+04
Df Model:                           1                                         
Covariance Type:            nonrobust                                         
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
const          0.0465      0.025      1.874      0.061      -0.002       0.095
x              0.8030      0.011     71.510      0.000       0.781       0.825
==============================================================================
Omnibus:                        3.944   Durbin-Watson:                   2.047
Prob(Omnibus):                  0.139   Jarque-Bera (JB):                4.150
Skew:                           0.043   Prob(JB):                        0.126
Kurtosis:                       3.161   Cond. No.                         2.21
==============================================================================
```

So this model finds a spurious effect of \\(X\\) on \\(Y\\), indicated by the low p-value (< 0.001).

### Model 2: \\(Y \sim X + A\\)

What about model 2:

´´´python
X2 = sm.add_constant(np.stack([x, a]).T)
model_2 = sm.OLS(y, X2).fit()
print(model_2.summary(xname=['const', 'x', 'a']))
```

```
OLS Regression Results                            
==============================================================================
Dep. Variable:                      y   R-squared:                       0.789
Model:                            OLS   Adj. R-squared:                  0.789
Method:                 Least Squares   F-statistic:                     5600.
Date:                Mon, 04 Mar 2024   Prob (F-statistic):               0.00
Time:                        13:11:43   Log-Likelihood:                -4333.0
No. Observations:                3000   AIC:                             8672.
Df Residuals:                    2997   BIC:                             8690.
Df Model:                           2                                         
Covariance Type:            nonrobust                                         
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
const          0.0036      0.019      0.190      0.850      -0.033       0.040
x              0.0192      0.019      1.034      0.301      -0.017       0.056
a              1.9714      0.042     47.435      0.000       1.890       2.053
==============================================================================
Omnibus:                        0.439   Durbin-Watson:                   2.023
Prob(Omnibus):                  0.803   Jarque-Bera (JB):                0.394
Skew:                           0.024   Prob(JB):                        0.821
Kurtosis:                       3.028   Cond. No.                         5.70
==============================================================================
```

This model seems to recognize the causal independence of \\(X\\) and \\(Y\\) correctly (large p-value for \\(X\\), suggesting the lack of significance).





