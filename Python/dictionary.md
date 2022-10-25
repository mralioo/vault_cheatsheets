## initialize dict
#### with empty array 
```python
>>> x = dict.fromkeys([1, 2, 3, 4], [])
>>> x[1].append('test')
>>> x
{1: ['test'], 2: ['test'], 3: ['test'], 4: ['test']}
```


## Update dict

```python

default_data['item3'] = 3

default_data.update({'item3': 3})
```