![[Pasted image 20221025015847.png]]

## Definition 
[Cross-validation](https://machinelearningmastery.com/k-fold-cross-validation/) is a statistical method used to estimate the skill of machine learning models.

[Cross-Validation Generators](https://vitalflux.com/k-fold-cross-validation-python-example/)

[Cross-Validation with Code in Python](https://medium.com/analytics-vidhya/cross-validation-with-code-in-python-55b342840089)

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

🔥A value of k=10 is very common in the field of applied machine learning, and is recommend if you are struggling to choose a value for your dataset.

### Example 

The _KFold()_ scikit-learn class can be used. It takes as arguments the number of splits, whether or not to shuffle the sample, and the seed for the [pseudorandom number generator](https://machinelearningmastery.com/how-to-generate-random-numbers-in-python/) used prior to the shuffle.

```python
# scikit-learn k-fold cross-validation

from numpy import array

from sklearn.model_selection import KFold

# data sample

data = array([0.1, 0.2, 0.3, 0.4, 0.5, 0.6])

# prepare cross validation

kfold = KFold(3, True, 1)

# enumerate splits

for train, test in kfold.split(data):

	print('train: %s, test: %s' % (data[train], data[test]))
```
```python
train: [0.1 0.4 0.5 0.6], test: [0.2 0.3]

train: [0.2 0.3 0.4 0.6], test: [0.1 0.5]

train: [0.1 0.2 0.3 0.5], test: [0.4 0.6]
```


## StratifiedKFold

[**StratifiedKFold**](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedKFold.html#sklearn.model_selection.StratifiedKFold) cross-validation technique is a powerful tool for optimizing machine learning models. It is a way of dividing data into train and test sets while accounting for the **class imbalance** in the data.

## GroupKFold

[**GroupKFold**](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GroupKFold.html#sklearn.model_selection.GroupKFold): is a cross-validation technique that is commonly used in machine learning. It is similar to KFold, but instead of splitting the data into random folds, it splits the data into folds based on groups.

## LeaveOneOut

### Definition 
The [**Leave-One-Out Cross-Validation**](https://machinelearningmastery.com/loocv-for-evaluating-machine-learning-algorithms/), or **LOOCV**, procedure is used to estimate the performance of machine learning algorithms when they are used to make predictions on <span style="color:blue">data not used to train </span> the model.


 LOOCV, is a configuration of [[Cross-Validation]], where _k_ is set to the number of examples in the dataset.
	- has the maximum computational cost
	- requries one model to be created and evaluated for each example in the training dataset
	-  **Don’t Use LOOCV**: Large datasets or costly models to fit.
	-  **Use LOOCV**: Small datasets or when estimated model performance is critical.


### Example 

```python

# loocv to manually evaluate the performance of a random forest classifier

from sklearn.datasets import make_blobs

from sklearn.model_selection import LeaveOneOut

from sklearn.ensemble import RandomForestClassifier

from sklearn.metrics import accuracy_score

# create dataset

X, y = make_blobs(n_samples=100, random_state=1)

# create loocv procedure

cv = LeaveOneOut()

# enumerate splits

y_true, y_pred = list(), list()

for train_ix, test_ix in cv.split(X):

	# split data
	
	X_train, X_test = X[train_ix, :], X[test_ix, :]
	
	y_train, y_test = y[train_ix], y[test_ix]
	
	# fit model
	
	model = RandomForestClassifier(random_state=1)
	
	model.fit(X_train, y_train)
	
	# evaluate model
	
	yhat = model.predict(X_test)
	
	# store
	
	y_true.append(y_test[0])
	
	y_pred.append(yhat[0])

# calculate accuracy

acc = accuracy_score(y_true, y_pred)

print('Accuracy: %.3f' % acc)
```
