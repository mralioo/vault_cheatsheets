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


# hack to dict 

```python 
class dotdict(dict):
	"""dot.notation access to dictionary attributes"""
	__getattr__ = dict.get 
	__setattr__ = dict.__setitem__
	__delattr__ = dict.__delitem__ 


mydict = {'val':'it works'}
nested_dict = {'val':'nested works too'}
mydict = dotdict(mydict) mydict.val # 'it works' 

mydict.nested = dotdict(nested_dict) 
mydict.nested.val # 'nested works too'
```