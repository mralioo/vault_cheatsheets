

[Meta Continual Learning_7_8_24 | Kaggle](https://www.kaggle.com/code/mrali92/meta-continual-learning-7-8-24/edit)

https://docs.google.com/presentation/d/1en5XNkcyQnrgYXkJRnOI06BnUQmT6L43WPkYRVjKThc/edit?usp=sharing



## CL Strategies

Let us now focus on some strategies for reducing catastrofic forgetting, one of the principal problems when learning continuously. in this section we will take a look at three different strategies:

1.   Naive
2.   Rehearsal
3.   Elastic Weight Consolidation (EWC)
4.   Variance Reduced - Meta Continual Learning

Finally we will plot our results for comparison. For a more comprehensive overview on recent CL strategies for deep learning take a look at the recent paper "[Continuous Learning in Single-Incremental-Task Scenarios](https://arxiv.org/abs/1806.08568)".

Let's start by defining our 5 tasks with the function we have already introduced before:



### Elastic Weights Consolidation (EWC) Strategy

Elastic Weights Consolidation (EWC) is a common CL strategy firstly proposed in the paper: "[Overcoming catastrophic forgetting in neural networks](https://arxiv.org/abs/1612.00796)" for deep neural networks.

It is based on the computation of the importance of each weight (fisher information) and a squared regularization loss, penalizing changes in the most important wheights for the previous tasks.

It has the great advantage of **not using any** of the previous tasks data!


#### ‘Meta’-learning
