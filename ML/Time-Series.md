#machinelearning 

## Time-domain features

[numpy Statistics](https://numpy.org/doc/stable/reference/routines.statistics.html)

Here are some of statistcal transforamtion that can b applied on time-serie  data  in order to investigate the feature space and to look deeper in the data dependency and correlation. 

As a first step before trying complicated methods, its usually helpful to apply simple algorithms depending on the problem type (e.g SVM, Random forest ...) and not to forget dimensionality reduction using [[Unsupervised Learning/PCA]] and [[Unsupervised Learning/t-SNE]] techniques.

```ad-note

A golden rule in machine learning, is not using machine learning. 
```

- mean 
- variance 
- standard deviation 
- median 
- maximum, minimum 
- root mean square 
- correlation between two axes 
- zero crossing rate 


### Skewness and Kurtosis

 [Measures of central tendency](https://www.turing.com/kb/calculating-skewness-and-kurtosis-in-python)

Understanding how central tendency measures spread when the normal distribution is distorted is important

![[../figures/Time-Series.jpg]]



**Skewness is a statistical measure of asymmetric distribution of data while kurtosis helps determine if the distribution is heavy-tailed compared to a normal distribution.**


The common type of data and probability distribution is the normal distribution, and it can become distorted under significant causes, which is calculated using **skewness and kurtosis**

- **skewness** :  is a measure of symmetry, or more precisely, the lack of symmetry. A distribution, or data set, is symmetric if it looks the same to the left and right of the center.
![[../figures/Time-Series-1.jpg]]

		- positive skew will indicate that the tail is on the right side. It will extend toward the most positive values (Skewness > 0)
		- a negative skew will indicate a tail on the left side and will extend to the more negative side (Skewness < 0)
		- A zero value will indicate that there is no skewness in the distribution, which means that the distribution is perfectly symmetrical (Skewness = 0)
	- Skewness is mostly calculated using the Fisher-Pearson Coefficient of Skewness. However, there are many more ways to calculate it such as Kelly’s Measure, Bowley, and Momental.
```math
||{"id":1187101283533}||

g_{1} = \frac{\sum ^{N}(Y_{i} - \bar{Y})^{3}/N} {s^{3}}

```
where $\bar{Y}$ is the mean, **_s_** is the standard deviation, and _N_ is the number of data points. Note that in computing the skewness, the _s_ is computed with _N_ in the denominator rather than _N_ - 1.
```python
import sciPy
spicy.stats.skew(array, axis = 0, bias = True)
```
**axis** signifies the axis along which we want to find the skewness value, and **bias** = True or False, based on the calculations that are determined upon the statistical bias.

- **kurtosis** : is a measure of whether the data are heavy-tailed or light-tailed relative to a normal distribution
![[../figures/Time-Series-2.jpg]]
	- Kurtosis describes the flatness/peakedness of the curve.
	- This flatness/peakedness is directly related to the outliers.
	- Kurtosis of a normal distribution is equal to 3. When the kurtosis is less than 3, it is known as platykurtic, and when it is greater than 3, it is leptokurtic. If it is leptokurtic, it will signify that it produces outliers rather than a normal distribution.
```math
||{"id":1305490250540}||

\mbox{kurtosis} = \frac{\sum ^{N}(Y_{i} - \bar{Y})^{4}/N} {s^{4}}  - 3
```
```python
import sciPy
spicy.stats.kurtosis(array, axis = 0, fisher = True, bias = True)
```

**axis** represents the axis along with the kurtosis value that needs to be measured.
**Fisher** = True when normal is 0.0. It will be False when the normal is 3.0. **Bias** is True or False, based on statistical bias.

🏹These functions calculate moments of the [probability density distribution](https://en.wikipedia.org/wiki/Probability_density_function) (that's why it takes only one parameter) and doesn't care about the "functional form" of the values.

### Stationarity and differencing
[ref](https://otexts.com/fpp2/stationarity.html)
* A **stationary time series** is one whose properties do not depend on the time at which the series is observed.
	* In such a time series the statistical measures such as the mean,standard deviation,auto correlation are somewhat similar over time.
	* No Trend

* Time series with trends, or with seasonality, are **not stationary**: the trend and seasonality will affect the value of the time series at different times.
	* In such a time series the statistical measures such as the mean,standard deviation,auto correlation show a decreasing or increasing trend over time.


* Compute the differences between consecutive observations. This is known as **differencing**.
	* Transformations such as logarithms can help to **stabilise the variance of a time series.**
	* Differencing can help stabilise the mean of a time series by removing changes in the level of a time series, and therefore eliminating (or reducing) trend and seasonality.

## Frequency-domain features
- entropy :
- spectral entropy :