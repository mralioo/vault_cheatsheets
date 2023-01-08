## Statistics
### nested dfs
- we have list of dataframes of length N, one df has the following shape: 
```python
          a         b         c         d         e         f         g
0  0.071094  0.077545  0.299540  0.377555  0.751840  0.879995  0.933399
1  0.538251  0.066780  0.415607  0.796059  0.718893  0.679950  0.502138
2  0.096001  0.680868  0.883778  0.210488  0.642578  0.023881  0.250317
```
- Calculate e.g mean  :
```python
 list_mean_dfs = [df.mean(axis=0) for df in list_dfs] 
```
	 - axis= 0 , means it will calculate the average over index.
	 - axis= 1, means it will calculate the average over columns.
- concatenate the results (series) to new dataframe with new columns name
```python
mean_df = pd.concat(list_mean_dfs, axis=1, keys=[f"{s.name}_mean" for s in list_mean_dfs[0]])
```
- change the index name 
```python
mean_df = mean_df.rename(index={k:f"{k}_mean" for k in list(list_mean_dfs[0].keys())}).T
```

- you can transpose the new dataframe, using the ``.T``  method

## Indexing
### loc & iloc
#### Label vs Location
[ref](https://stackoverflow.com/questions/31593201/how-are-iloc-and-loc-different)

-   `loc` gets rows (and/or columns) with particular **labels**.
    
-   `iloc` gets rows (and/or columns) at integer **locations**.


```python
>>> s = pd.Series(list("abcdef"), index=[49, 48, 47, 0, 1, 2]) 
49    a
48    b
47    c
0     d
1     e
2     f

>>> s.loc[0]    # value at index label 0
'd'

>>> s.iloc[0]   # value at index location 0
'a'

>>> s.loc[0:1]  # rows at index labels between 0 and 1 (inclusive)
0    d
1    e

>>> s.iloc[0:1] # rows at index location between 0 and 1 (exclusive)
49    a
```

<p>Here are some of the differences/similarities between <code>s.loc</code> and <code>s.iloc</code> when passed various objects:</p>

<div class="s-table-container">

<table class="s-table">

<thead>

<tr>

<th>&lt;object&gt;</th>

<th>description</th>

<th><code>s.loc[&lt;object&gt;]</code></th>

<th><code>s.iloc[&lt;object&gt;]</code></th>

</tr>

</thead>

<tbody>

<tr>

<td><code>0</code></td>

<td>single item</td>

<td>Value at index <em>label</em> <code>0</code> (the string <code>'d'</code>)</td>

<td>Value at index <em>location</em> 0 (the string <code>'a'</code>)</td>

</tr>

<tr>

<td><code>0:1</code></td>

<td>slice</td>

<td><strong>Two</strong> rows (labels <code>0</code> and <code>1</code>)</td>

<td><strong>One</strong> row (first row at location 0)</td>

</tr>

<tr>

<td><code>1:47</code></td>

<td>slice with out-of-bounds end</td>

<td><strong>Zero</strong> rows (empty Series)</td>

<td><strong>Five</strong> rows (location 1 onwards)</td>

</tr>

<tr>

<td><code>1:47:-1</code></td>

<td>slice with negative step</td>

<td><strong>three</strong> rows (labels <code>1</code> back to <code>47</code>)</td>

<td><strong>Zero</strong> rows (empty Series)</td>

</tr>

<tr>

<td><code>[2, 0]</code></td>

<td>integer list</td>

<td><strong>Two</strong> rows with given labels</td>

<td><strong>Two</strong> rows with given locations</td>

</tr>

<tr>

<td><code>s &gt; 'e'</code></td>

<td>Bool series (indicating which values have the property)</td>

<td><strong>One</strong> row (containing <code>'f'</code>)</td>

<td><code>NotImplementedError</code></td>

</tr>

<tr>

<td><code>(s&gt;'e').values</code></td>

<td>Bool array</td>

<td><strong>One</strong> row (containing <code>'f'</code>)</td>

<td>Same as <code>loc</code></td>

</tr>

<tr>

<td><code>999</code></td>

<td>int object not in index</td>

<td><code>KeyError</code></td>

<td><code>IndexError</code> (out of bounds)</td>

</tr>

<tr>

<td><code>-1</code></td>

<td>int object not in index</td>

<td><code>KeyError</code></td>

<td>Returns last value in <code>s</code></td>

</tr>

<tr>

<td><code>lambda x: x.index[3]</code></td>

<td>callable applied to series (here returning 3<sup>rd</sup> item in index)</td>

<td><code>s.loc[s.index[3]]</code></td>

<td><code>s.iloc[s.index[3]]</code></td>

</tr>

</tbody>

</table>

</div>

<p><code>loc</code>'s label-querying capabilities extend well-beyond integer indexes and it's worth highlighting a couple of additional examples.</p>
#### Rows and Columns

`loc` and `iloc` work the same way with DataFrames as they do with Series. It's useful to note that both methods can address columns and rows together.

When given a tuple, the first element is used to index the rows and, if it exists, the second element is used to index the columns.

Consider the DataFrame defined below:

```python
>>> import numpy as np 
>>> df = pd.DataFrame(np.arange(25).reshape(5, 5),  
                      index=list('abcde'), 
                      columns=['x','y','z', 8, 9])
>>> df
    x   y   z   8   9
a   0   1   2   3   4
b   5   6   7   8   9
c  10  11  12  13  14
d  15  16  17  18  19
e  20  21  22  23  24
```

Then for example:

```python
>>> df.loc['c': , :'z']  # rows 'c' and onwards AND columns up to 'z'
    x   y   z
c  10  11  12
d  15  16  17
e  20  21  22

>>> df.iloc[:, 3]        # all rows, but only the column at index location 3
a     3
b     8
c    13
d    18
e    23
```

## Map values 
- use `.repalce` to map specific value in the df 
```python
>>> df = pd.DataFrame({'col2': {0: 'a', 1: 2, 2: np.nan}, 'col1': {0: 'w', 1: 1, 2: 2}})
>>> di = {1: "A", 2: "B"}
>>> df
  col1 col2
0    w    a
1    1    2
2    2  NaN
>>> df.replace({"col1": di})
  col1 col2
0    w    a
1    A    2
2    B  NaN
```


```python
df = pd.DataFrame({'a': [10,20],'b':[100,200],'c': ['a','b']})

df.loc['Column_Total']= df.sum(numeric_only=True, axis=0)
df.loc[:,'Row_Total'] = df.sum(numeric_only=True, axis=1)

print(df)

                 a      b    c  Row_Total
0             10.0  100.0    a      110.0
1             20.0  200.0    b      220.0
Column_Total  30.0  300.0  NaN      330.0
```