## Definition 
[Cross-validation](https://machinelearningmastery.com/k-fold-cross-validation/) is a statistical method used to estimate the skill of machine learning models.

## k-Fold Cross-Validation

Cross-validation is a resampling procedure used to evaluate machine learning models on a limited data sample.

The procedure has a single parameter called **k** that refers to the number of groups that a given data sample is to be split into


It is a popular method because it is simple to understand and because it generally results in a less biased or less optimistic estimate of the model skill than other methods, such as a simple train/test split.

**The general procedure** is as follows:

1.  Shuffle the dataset randomly.
2.  Split the dataset into k groups
3.  For each unique group:
    1.  Take the group as a hold out or test data set
    2.  Take the remaining groups as a training data set
    3.  Fit a model on the training set and evaluate it on the test set
    4.  Retain the evaluation score and discard the model
4.  Summarize the skill of the model using the sample of model evaluation scores