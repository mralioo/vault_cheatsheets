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

