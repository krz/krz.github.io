---
layout: post
title: AutoML full workflow with tidymodels
categories: [r, machine learning]
---

AutoML is the process of automating the tasks of applying machine learning to real-world problems. I often use this approach as it requires very little investment in tuning models and the results are usually quite competitive.
The following is a notebook for a recent [kaggle competition](https://www.kaggle.com/code/christph/automl-full-workflow).


```R
suppressMessages(library(tidyverse))
suppressMessages(library(tidymodels))
suppressMessages(library(h2o))
suppressMessages(library(agua))
tidymodels_prefer()
```

## Overview
In this notebook I will use automated machine learning using the `h2o` package wrapped in a `tidymodels` workflow for preprocessing.

__AutoML__ is the process of automating the tasks of applying machine learning to real-world problems.
H2O AutoML trains a random grid of algorithms such as GBMs, DNNs, GLMs, etc. using a carefully chosen hyper-parameter space. It also trains two stacked ensembles using all the models from the grid.

### Import data
First, we read in the data and drop the `Id` variable.


```R
dat <- read_csv("/kaggle/input/icr-identify-age-related-conditions/train.csv") %>%
    mutate(
        Class = factor(Class),
        EJ = factor(EJ)
    ) %>%
    select(-Id)
```

### Initialize H2O
Next, we initialize `h2o` with 25G of RAM. By default we use all available cores.


```R
h2o.init(max_mem_size = "25G")
```

### Define the model
we try 30 different models (set higher to maybe get better results).
Note that we also set `balance_classes` to TRUE because the `Class` variable is highly unbalanced.


```R
auto_mod <- auto_ml() %>%
  set_engine("h2o",
    max_models = 30,
    stopping_metric = "logloss",
    sort_metric = "logloss",
    balance_classes = T,
    seed = 42
  ) %>%
  set_mode("classification") %>%
  translate()
```

### Feature Engineering
Now we define the recipe. We impute missing values and delete variables with zero variance.
There are other steps that can be included to improves results, for example:
* step_normalize(all_numeric_predictors) to normalize variables
* step_pca(all_numeric_variables, keep_original_cols=T) to include principal components
* step_poly(all_numeric_variables) to generate poly splines of variables

See https://recipes.tidymodels.org/reference/index.html for a list of all steps.

In this example we only impute missing variables with a bagged tree and delete highly correlated variables, variables that are linear combinations of each other and variables with zero variance


```R
auto_recipe <-
  recipe(Class ~ ., data = dat) %>%
  step_impute_bag(all_predictors()) %>%
  step_corr(all_numeric_predictors()) %>%
  step_lincomb(all_numeric_predictors()) %>%
  step_zv(all_predictors()) 
auto_recipe
```

    [36m──[39m [1mRecipe[22m [36m──────────────────────────────────────────────────────────────────────[39m
    
    
    
    ── Inputs 
    
    Number of variables by role
    
    outcome:    1
    predictor: 56
    
    
    
    ── Operations 
    
    [36m•[39m Bagged tree imputation for: [34mall_predictors()[39m
    
    [36m•[39m Correlation filter on: [34mall_numeric_predictors()[39m
    
    [36m•[39m Linear combination filter on: [34mall_numeric_predictors()[39m
    
    [36m•[39m Zero variance filter on: [34mall_predictors()[39m



### Workflow
This is the full workflow with pre-processing and training. `h2o`
internally evaluates the models in 10-fold cross-validation.


```R
auto_workflow <-
  workflow() %>%
  add_model(auto_mod) %>%
  add_recipe(auto_recipe)

auto_res <-
  auto_workflow %>%
  fit(data = dat)
```

### Results
The best model is a stacked ensemble of all 30 models with a logloss of __0.16__. Nice.


```R
print(auto_res)
```

══ Workflow [trained] ══════════════════════════════════════════════════════════
Preprocessor: Recipe
Model: auto_ml()

── Preprocessor ────────────────────────────────────────────────────────────────
4 Recipe Steps

• step_impute_bag()
• step_corr()
• step_lincomb()
• step_zv()

── Model ───────────────────────────────────────────────────────────────────────

Leader Algorithm: stackedensemble

Leader ID: StackedEnsemble_BestOfFamily_1_AutoML_1_20230705_70330

═════════════════════════ H2O AutoML Summary: 32 models ════════════════════════ 

Leader Algorithm: stackedensemble

 

Leader ID: StackedEnsemble_BestOfFamily_1_AutoML_1_20230705_70330

 
══════════════════════════════════ Leaderboard ═════════════════════════════════ 
                                                model_id   logloss       auc
1 StackedEnsemble_BestOfFamily_1_AutoML_1_20230705_70330 0.1585723 0.9683930
2    StackedEnsemble_AllModels_1_AutoML_1_20230705_70330 0.1629910 0.9691752
3         XGBoost_grid_1_AutoML_1_20230705_70330_model_4 0.1869239 0.9615713
4         XGBoost_grid_1_AutoML_1_20230705_70330_model_5 0.1963171 0.9546314
5                      XGBoost_2_AutoML_1_20230705_70330 0.1978969 0.9564051
6                      XGBoost_3_AutoML_1_20230705_70330 0.1987732 0.9570963
      aucpr mean_per_class_error      rmse        mse
1 0.8946899           0.08179619 0.2117525 0.04483912
2 0.8920815           0.10564469 0.2174633 0.04729029
3 0.8663235           0.10999236 0.2258904 0.05102648
4 0.8586601           0.11210252 0.2407249 0.05794847
5 0.8427151           0.10817325 0.2389873 0.05711494
6 0.8366848           0.10677254 0.2428506 0.05897642




### Predict
Finally we predict on the test data and create a submission file. The preprocessing workflow will be automatically applied to the test data.


```R
test <- read_csv("/kaggle/input/icr-identify-age-related-conditions/test.csv") %>%
    mutate(
        EJ = factor(EJ)
    )
```


```R
preds <- predict(auto_res, test, type="prob")
preds
```


```R
submission <- tibble(Id = test$Id,
                 class_0 = preds$.pred_p0,
                 class_1 = preds$.pred_p1)
submission
```


```R
write_csv(submission, file="submission.csv")
```
