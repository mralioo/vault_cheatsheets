
#datascience #dataengineering 

# General functions

https://pandas.pydata.org/docs/reference/general_functions.html

## Merge 
Select column name and concat dfs : 
```python
df1 = mean_df[['acc_mean','gyro_mean','mag_mean']]
df2 = std_df[['acc_std','gyro_std','mag_std']]
df3 = var_df[['acc_var','gyro_var','mag_var']]
frames = [df1, df2, df3]
result = pd.concat(frames, axis=1)
```

axis = 1 : remain the same index 


# Melt 

Unpivot a DataFrame from wide to long format, optionally leaving identifiers set.


```python 
pandas.melt(_frame_, _id_vars=None_, _value_vars=None_, _var_name=None_, _value_name='value'_, _col_level=None_, _ignore_index=True_)
```


```python 
>>> df = pd.DataFrame({'A': {0: 'a', 1: 'b', 2: 'c'},
...                    'B': {0: 1, 1: 3, 2: 5},
...                    'C': {0: 2, 1: 4, 2: 6}})
>>> df
   A  B  C
0  a  1  2
1  b  3  4
2  c  5  6



>>> pd.melt(df, id_vars=['A'], value_vars=['B'])
   A variable  value
0  a        B      1
1  b        B      3
2  c        B      5

```

