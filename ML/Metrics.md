#machinelearning #deeplearning 

# Classification 

## ROC AUC Curve

I once had a very imbalanced dataset, and I kept getting amazing predictive performance as measured by ROC AUC. That is when I discovered the limitations of that metric!

The ROC curve plots the True Positive Rate (TPR) as a function of the False Positive Rate (FPR). We have:

- TPR = True positives / positive = TP / P

- FPR = False positives / negative = FP / N

The curve is drawn by ranking the probability score of the model and computing those rates for each value of the score used as a threshold to assign the classes. A low threshold (e.g. p=0.1 => 1 if p > 0.1, 0 otherwise) will lead to a high number of TP but also a high number of FP. A high threshold (e.g. p=0.9 => 1 if p > 0.9, 0 otherwise) will lead to a low number of TP but also a low number of FP. In fact, at p=0, we have:

- TP = P => TPR = 1

- FP = N => FPR = 1

And for p=1, we have

- TP = 0 => TPR = 0

- FP = 0 => FPR = 0

Now what happens in the case of imbalanced classes? We have N >> P. As a consequence, the probability score distribution will tend to be skewed toward smaller values: most of the scores will be small. Most of the true positive samples will be on the high side of the probability score. Let's consider the following label (c) and resulting score (p) lists:

-> move the threshold from left to right

c = [0 , 0 , 0 , 0 , 0 , 1 , 0 ]

p = [0.05, 0.06, 0.07, 0.8, 0.15, 0.3, 0.5]

Most thresholds correspond to a transition (left to right) from a negative sample to another negative one (because there are very few positive samples). TPR jumps down only when we transition to a positive sample where FPR drops down only by a very small amount when we transition to a negative one. As a consequence, for most thresholds, we will have TPR ~ 1 where FPR progressively falls down to 0 (almost linearly), making FPR quite uninformative. Only at the highest values of the score will the TPR begin to fall. When it starts to fall, we have 1/ N << 1/ P, so when the increments are non-zero, we have D(FPR) << D(TPR), as FPR ~ 1/N and TPR ~ 1/P. Therefore, the ROC AUC looks artificially like the curve of a perfect classifier when the imbalance is high.

Let's now consider the Precision-Recall curve. We plot precision as a function of recall for the different thresholds:

- Precision = TP / predicted positives

- Recall = TPR = TP / P

Precision is a much more "dynamic" metric when we change the threshold in the case of imbalanced data, as opposed than FPR. Because the Precision-Recall curve focuses entirely on the minority class, it tends to be more invariant to changes in the class distribution. The Precision-Recall AUC ([https://scikit-learn.org/.../sklearn.metrics.average...](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html?fbclid=IwAR3wzjV2gAmzYqjAK_zp6Iz6vQFPHBgtPes8rd5XZyhtVjtd4Bnqsx1oD2c)) is therefore a much more actionable metric for highly imbalanced data as it does not suffer from the problem described above:

- [https://opendata.uni-halle.de/.../1/1_3Fayzrakhmanov.pdf](https://opendata.uni-halle.de/bitstream/1981185920/12388/1/1_3Fayzrakhmanov.pdf?fbclid=IwAR259x8t7aKZa1HlklKMQSByWRO_qQs4C9mjInPRJgkmiCgwwAeu1G9FOjg)
