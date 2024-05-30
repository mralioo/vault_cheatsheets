
#pytorch #machinelearning #datascience 

## Calculus

### Derivitives 
[ref](https://machinelearningmastery.com/calculating-derivatives-in-pytorch/?utm_source=drip&utm_medium=email&utm_campaign=Calculating+derivatives+in+PyTorch&utm_content=Calculating+Derivatives+in+PyTorch)
#### Autograd

The autograd – an auto differentiation module in PyTorch – is used to calculate the derivatives and optimize the parameters in neural networks. It is intended primarily for gradient computations.

```python
import torch
x = torch.tensor(3.0, requires_grad = True) # simple tensor

y = 3 * x ** 2 # simple equation

print("Result of the equation is: ", y)

y.backward()

print("Dervative of the equation at x = 3 is: ", x.grad)


>>Result of the equation is:  tensor(27., grad_fn=<MulBackward0>) # before grad
>>Dervative of the equation at x = 3 is:  tensor(18.)


```

* `.backward` : forms acyclic graph storing the computation history on the variable y 
* evaluate the result with `.grad` for the given value.

#### Computational Graph
PyTorch generates derivatives by **building a backwards graph behind the scenes**, while tensors and backwards functions are the graph’s nodes.

**In a graph**, PyTorch computes **the derivative of a tensor** depending on ***whether it is a leaf or not.***

```python
# For x
print('data attribute of the tensor:',x.data)
data attribute of the tensor: tensor(3.)
print('grad attribute of the tensor::',x.grad)
grad attribute of the tensor:: tensor(18.)
print('grad_fn attribute of the tensor::',x.grad_fn)
grad_fn attribute of the tensor:: None
print("is_leaf attribute of the tensor::",x.is_leaf)
is_leaf attribute of the tensor:: True
print("requires_grad attribute of the tensor::",x.requires_grad)
requires_grad attribute of the tensor:: True
