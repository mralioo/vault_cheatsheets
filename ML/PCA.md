## Definition

[Principal Component Analysis (PCA)](https://towardsdatascience.com/principal-component-analysis-pca-explained-visually-with-zero-math-1cbf392b9e7d) is an exploratory approach to reduce the data set's dimensionality to 2D or 3D.

 Principal Component Analysis is a **linear transformation of data set** that defines a new coordinate rule such that:

**The greater the variance, the more the information. Vice versa.**

-   The highest variance by any projection of the data set appears to laze on the first axis.
-   The second biggest variance on the second axis, and so on.

![[Principal%20Component%20Analysis%20second%20principal.gif]]

**![](https://lh5.googleusercontent.com/j8sejouDw9H7tOSJb2CHLXXsWCJ3xkTyrHMM9SIGe-H-RCjOpkCNKdnTH4j5CYe1-cgX0Bp4EsnqSORZ4mf8szhLazHJEpReZDaLbqt4aTSiEWNkFJzBD11WRqlpPLGvqTNlVY62Ivr_AxORFDGUhrQmDc-tJofcz02SDQpZUbmUsZW-3vuTWe6A)


Mathematically the main objective of PCA is to:

-   Find an orthonormal basis for the data.
-   Sort dimensions in the order of importance.
-   Discard the low significance dimensions.
-   Focus on uncorrelated and Gaussian components.


## Workflow 
-   Standardize the PCA.
-   Calculate the covariance matrix.
-   Find the eigenvalues and eigenvectors for the covariance matrix.
-   Plot the vectors on the scaled data.

## [[scikit-learn]]

```python
sklearn.decomposition.PCA(_n_components=None_, _*_, _copy=True_, _whiten=False_, _svd_solver='auto'_, _tol=0.0_, _iterated_power='auto'_, _n_oversamples=10_, _power_iteration_normalizer='auto'_, _random_state=None_)
```


- `n_components`_:_ Number of components to keep. 
	- If `n_components == 'mle'` and `svd_solver == 'full'`, Minka’s MLE is used to guess the dimension. Use of `n_components == 'mle'` will interpret `svd_solver == 'auto'` as `svd_solver == 'full'`.
	- If `0 < n_components < 1` and `svd_solver == 'full'`, select the number of components such that the amount of variance that needs to be explained is greater than the percentage specified by n_components.
    - If `svd_solver == 'arpack'`, the number of components must be strictly less than the minimum of n_features and n_samples.
		
		Hence, the None case results in:
		
		n_components == min(n_samples, n_features) - 1
- `svd_solver` {‘auto’, ‘full’, ‘arpack’, ‘randomized’}, default=’auto’
	- If auto :
		The solver is selected by a default policy based on `X.shape` and `n_components`: if the input data is larger than 500x500 and the number of components to extract is lower than 80% of the smallest dimension of the data, then the more efficient ‘randomized’ method is enabled. Otherwise the exact full SVD is computed and optionally truncated afterwards.
	- If full :
		run exact full SVD calling the standard LAPACK solver via `scipy.linalg.svd` and select the components by postprocessing
	- If arpack :
		run SVD truncated to n_components calling ARPACK solver via `scipy.sparse.linalg.svds`. It requires strictly 0 < n_components < min(X.shape)
	- If randomized :
		run randomized SVD by the method of Halko et al.