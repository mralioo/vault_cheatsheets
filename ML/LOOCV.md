### Definition 
The [**Leave-One-Out Cross-Validation**](https://machinelearningmastery.com/loocv-for-evaluating-machine-learning-algorithms/), or **LOOCV**, procedure is used to estimate the performance of machine learning algorithms when they are used to make predictions on <span style="color:blue">data not used to train </span> the model.


 LOOCV, is a configuration of [[K-fold Cross-Validation]], where _k_ is set to the number of examples in the dataset.
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