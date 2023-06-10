**An outlier is a point or set of data points that lie away from the rest of the data values of the dataset**. That is, it is a data point(s) that appear away from the overall distribution of data values in a dataset.

It can be considered as an **abnormal distribution which appears away from the class** or population.


## Why is it necessary to remove outliers from the data?

As discussed above, outliers are the data points that lie away from the usual distribution of the data and causes the below effects on the overall data distribution:

- **Affects the overall standard variation of the data.**
- **Manipulates the overall mean of the data.**
- **Converts the data to a skewed form.**
- **It causes bias in the accuracy estimation of the machine learning model.**
- **Affects the distribution and statistics of the dataset.**

# Detection of Outliers 
Outliers can be detected by : 
- **Z-score**
- **Scatter Plots**
- **Interquartile range(IQR)**
- **FFT**

### IQR approach

**IQR is the acronym for Interquartile Range**. **It measures the statistical dispersion of the data values** as a measure of overall distribution.

IQR is equivalent to the difference between the first quartile (Q1) and the third quartile (Q3) respectively.

![](../figures/Pasted%20image%2020230610100842.png)



# Resources : 
* https://www.askpython.com/python/examples/detection-removal-outliers-in-python
* https://docs.datalumina.io/jD1BSJCAPYKSwh