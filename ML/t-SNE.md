## Definition

[t-Distributed Stochastic Neighbor Embedding (t-SNE)](https://towardsdatascience.com/t-distributed-stochastic-neighbor-embedding-t-sne-bb60ff109561) is a technique for dimensionality reduction that is particularly well suited for **the visualization of high-dimensional datasets**.

-   An **unsupervised, randomized algorithm, used only for visualization**
-   Applies a **non-linear dimensionality reduction techniqu**e where the f**ocus is on keeping the very similar data points close together in lower-dimensional space**.
-   **Preserves the local structure of the data using student t-distribution** to compute the similarity between two points in lower-dimensional space.
-   t-SNE uses a **heavy-tailed Student-t distribution to compute the similarity between two points in the low-dimensional space** rather than a Gaussian distribution, which helps to address the **crowding and optimization problems**.
-   **Outliers do not impact t-SNE**


![](https://miro.medium.com/max/363/1*fjMbCSIwwZmvnfDOEK-R-w.png)


## Goals for reducing the dimensionality of the data

-   Preserve as much **significant structure or information of the data present in the high- dimensional data** as possible in the low-dimensional representation.
-   **Increase the interpretability** of the data in the lower dimension.
-  **Minimizing information loss** of data due to dimensionality reduction.

## Workflow 
#### Step 1
t-SNE converts the **high-dimensional Euclidean** distances between datapoints xᵢ and xⱼ into **conditional probabilities P(j|i)**.

![[t-SNE.png]]
#### Step 2 

Map each **point in high dimensional space** to a l**ow dimensional map** based on the **pairwise similarity** of points in the high dimensional space.


#### Step 3 
Find a low-dimensional data representation that minimizes the mismatch between Pᵢⱼ and qᵢⱼ (of the lower dimensional space)using gradient descent based on **Kullback-Leibler divergence(KL Divergence)**![[t-SNE-1.png]]


🏹 t-SNE optimizes the points in lower dimensional space using gradient descent.

#### Step 4 
Use Student-t distribution to compute the similarity between two points in the low-dimensional space


## [[scikit-learn]]
```python
TSNE(_n_components=2_, _*_, _perplexity=30.0_, _early_exaggeration=12.0_, _learning_rate='warn'_, _n_iter=1000_, _n_iter_without_progress=300_, _min_grad_norm=1e-07_, _metric='euclidean'_, _metric_params=None_, _init='warn'_, _verbose=0_, _random_state=None_, _method='barnes_hut'_, _angle=0.5_, _n_jobs=None_, _square_distances='deprecated'_)

```


t-SNE parameters : 
- `n_components`_:_ **Dimension of the embedded space, this is the lower dimension that we want the high dimension data to be converted to**. The **default value is 2** for 2-dimensional space.

- `Perplexity`: The perplexity is related to the number of nearest neighbors that are used in t-SNE algorithms. **Larger datasets usually require a larger perplexity. Perplexity can have a value between 5 and 50.** **The default value is 30.**

- `n_iter`: Maximum number of iterations for optimization. **Should be at least 250 and the default value is 1000**

- `learning_rate`: The learning rate for t-SNE is usually in the range [10.0, 1000.0] with the default value of 200.0


**Visualization for different values of perplexity**
![[t-SNE-2.png]]


**Visualization for different values for n_iter**![[t-SNE-3.png]]



We can see that the clusters generated from t-SNE plots are much more defined than the ones using [[PCA]].

-   **PCA is deterministic**, whereas t-SNE is not deterministic and is randomized.
-   t-SNE tries to map **only local neighbors** whereas PCA is just a diagonal rotation of our initial covariance matrix and the eigenvectors represent and preserve the global properties