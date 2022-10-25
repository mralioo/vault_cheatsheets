

Unscaled input variables can result in a slow or unstable learning process, whereas unscaled target variables on regression problems can result in exploding gradients causing the learning process to fail.


When a network is fit **on unscaled data** that has a range of values (e.g. quantities in the 10s to 100s) it is possible **for large inputs to slow down the learning and convergence** of your network and in some cases **prevent the network from effectively learning** your problem.

🏹 It is best practice is **to estimate the mean and standard deviation of the training dataset** and use these variables to **scale the train and test dataset**. This is to avoid any data leakage during the model evaluation process.

## Standardization
Standardizing a dataset involves **rescaling the distribution of values** so that the mean of observed values is 0 and the standard deviation is 1. It is sometimes referred to as “_whitening_.”

⚠️ Standardization can be useful, and even required in some machine learning algorithms when your data has input values with differing scales.

## Noramalization
Normalization is a **rescaling of the data from the original range** so that all values are within the range of 0 and 1.

### MinMaxScaler
A value is normalized as follows:

-   y = (x – min) / (max – min)

Where the minimum and maximum values pertain to the value x being normalized.

⚠️  if your time series **is trending up or down**, estimating these expected values may be difficult and normalization may **not be the best method to use** on your problem.


## Multilayer Perceptron With Scaled Input Variables

Whether input variables require **scaling depends on the specifics of your problem** and of each variable. Let’s look at some examples.
### Categorical Inputs
You may have a sequence of categorical inputs, **such as letters or statuses.**

Generally, categorical inputs are first integer encoded then one hot encoded. That is, a unique integer value is assigned to each distinct possible input, then a binary vector of ones and zeros is used to represent each integer value.

By definition, a one hot encoding will ensure that each input is a small real value, in this case 0.0 or 1.0.

### Real-Valued Inputs
You may have a sequence of quantities as inputs, such as prices or temperatures.

If the distribution of the **quantity is normal**, then it should be standardized, **otherwise the series should be normalized**. This applies if the range of quantity values is large (10s 100s, etc.) or small (0.01, 0.0001).

If the quantity **values are small (near 0-1)** and the distribution is limited (e.g. standard deviation near 1) then perhaps you can get away with no scaling of the series.


## Multilayer Perceptron With Scaled Output Variables
Reducing the scale of the target variable will, in turn, reduce the size of the gradient used to update the weights and result in a more stable model and training process.

⚠️ You must ensure that the **scale of your output variable** matches the **scale of the activation function (transfer function)** on the output layer of your network.

The following heuristics should cover most sequence prediction problems:
### Binary Classification Problem
If your problem is a **binary classification** problem, then the output will be **class values 0 and 1**. This is best modeled with a **sigmoid activation** function on the output layer. **Output values will be real values between 0 and 1** that can be snapped to crisp values.
### Multi-class Classification Problem
If your problem is a multi-class classification problem, then the **output will be a vector of binary class values between 0 and 1**, one output per class value. This is best modeled with a **softmax activation** function on the output layer. Again, **output values will be real values between 0 and 1** that can be snapped to crisp values.
### Regression Problem
If your problem is a regression problem, then **the output will be a real value**. This is best modeled with a **linear activation** function. If the distribution of the value is normal, then you can standardize the output variable. Otherwise, the output variable can be normalized.


resources : 

- [How to use Data Scaling Improve Deep Learning Model Stability and Performance](https://machinelearningmastery.com/how-to-improve-neural-network-stability-and-modeling-performance-with-data-scaling/)
- [How to Use StandardScaler and MinMaxScaler Transforms in Python](https://machinelearningmastery.com/standardscaler-and-minmaxscaler-transforms-in-python/)
- [How to Scale Data for Long Short-Term Memory Networks in Python](https://machinelearningmastery.com/how-to-scale-data-for-long-short-term-memory-networks-in-python/)
* [[Time-Series]]