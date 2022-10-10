### Definition 
The [**Leave-One-Out Cross-Validation**](https://machinelearningmastery.com/loocv-for-evaluating-machine-learning-algorithms/), or **LOOCV**, procedure is used to estimate the performance of machine learning algorithms when they are used to make predictions on <span style="color:blue">data not used to train </span> the model.


 LOOCV, is a configuration of [[K-fold Cross-Validation]], where _k_ is set to the number of examples in the dataset.
	- has the maximum computational cost
	- requries one model to be created and evaluated for each example in the training dataset
	-  **Don’t Use LOOCV**: Large datasets or costly models to fit.
	-  **Use LOOCV**: Small datasets or when estimated model performance is critical.


