## Indexing 
* numpy.take(_a_, _indices_, _axis=None_, _out=None_, _mode='raise'_) : take elements from an array along an axis.
```python
a = [4, 3, 5, 7, 6, 8]
>>> indices = [0, 1, 4]
>>> np.take(a, indices)
array([4, 3, 6])
```