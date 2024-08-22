#CV #datascience #AI_session  #Transformer

Slides : https://docs.google.com/presentation/d/1SDmgmpxShLskfGlC_Z_kNm2ixb6a-Jn-v_F5tATn2o4/edit?usp=sharing

Paper: [2010.11929 (arxiv.org)](https://arxiv.org/pdf/2010.11929)

Notebook : [Vision Transfomer on MNIST (kaggle.com)](https://www.kaggle.com/code/andandand/vision-transfomer-on-mnist)
[Google Colab](https://colab.research.google.com/drive/1zwxyfYOCJlQiggZVc0OP8LeA4Xd0M_CS?usp=sharing)

## Comparison CNN and ViT


* ViT is more expensive
* ViT required more data
* ViT has less inductive bias 
* CNN is based on inductive bias 
	* e.g if consider only dataset that taken in winter 


## Inductive bias : 

prior knowledge 

bias-free learning and search : 
(no free lunch theory : http://www.no-free-lunch.org/) 



![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUddv4juQN3nxZ2yoKZHr2OJAUldS5MsYx5hXs-D7Y7aPulN_qYdcCeyoTHk-8L3jwPG0kFIIwKNVk5B249nvTOrrkn8EtPjMjEXyEcDybvmxkaR2w08-39Ql7wsmH2zYVq1uTtJ4T5ahSKU4c7dTK9RuYg-Op4b=s2048?key=wSKDGT7AxXF68A6IXsBo0Q)


1 The image is split into small patches. Unlike CNNs, which assume nearby pixels are related ("Locality Bias"), ViT treats each patch independently at first, not assuming any specific structure. 

2 Transformer Encoder: The Transformer processes these patches as a sequence, without inherently knowing they are part of a 2D grid like a CNN would. It has to learn the relationships between patches from scratch, rather than assuming any spatial structure.

3 Position Embedding: so model understand the order of patches, position embeddings are added, but this is still weaker than the strong spatial assumptions CNNs make.



## Normalization is crucial : 

the number disappeared into zero, this is numerical underflow and what would happened with a vanishing gradient , to avoid this use skip connection


### Notes 

* features need to be in range [-1, 1] which are the parameters interval , because data could explose

* Class token , (related to semantic class for each patch )

* Average in neural network ruin the backprop (not linear , we can not calculate derivative

* For best practices : should always to cast type of model or the input  and what are the effect on model performance 

* Integer division : (img_size // patch_size)


![](../../figures/Vision%20Transformer%20(ViT).png)

+ No overlap btw patches , because there is a patch that contain all information 


* Conv layer increase the dim of the data , orig (28 *28) to (64, 4,4) : overparameterize
	* overparameterization and grokking
	* [MATHAI_29_paper.pdf (mathai-iclr.github.io)](https://mathai-iclr.github.io/papers/papers/MATHAI_29_paper.pdf)



## Data quality 
#CNN 

| Tools         | Website                                                                                                                                                                                    |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Cleanvision   | [Documentation (cleanvision.readthedocs.io)](https://cleanvision.readthedocs.io/en/latest/)                                                                                                |
| Cleanlab      | [cleanlab/cleanlab: The standard data-centric AI package for data quality and machine learning with messy, real-world data and labels. (github.com)](https://github.com/cleanlab/cleanlab) |
| label errors  | [Label Errors: Explore](https://labelerrors.com/)                                                                                                                                          |

## Tensor operation in pytorch 


* Matrix operation

		View  (like flatten)
		
		transpose (swap axis )
		
		!!!  not to use **view** to **transpose** not the same 

* create a dummy tensor a like 

```
causal_mask = torch.full_like(qk_scaled, fill_value=float('-inf'))
>>>tensor([[[-inf, -inf, -inf, -inf, -inf, -inf],
         [-inf, -inf, -inf, -inf, -inf, -inf],
         [-inf, -inf, -inf, -inf, -inf, -inf],
         [-inf, -inf, -inf, -inf, -inf, -inf],
         [-inf, -inf, -inf, -inf, -inf, -inf],
         [-inf, -inf, -inf, -inf, -inf, -inf]]])

causal_mask = torch.triu(causal_mask, diagonal=1)
>>>tensor([[[0., -inf, -inf, -inf, -inf, -inf],
         [0., 0., -inf, -inf, -inf, -inf],
         [0., 0., 0., -inf, -inf, -inf],
         [0., 0., 0., 0., -inf, -inf],
         [0., 0., 0., 0., 0., -inf],
         [0., 0., 0., 0., 0., 0.]]])

```

# Transformer


* self attention : 
[Why multi-head self attention works: math, intuitions and 10+1 hidden insights | AI Summer (theaisummer.com)](https://theaisummer.com/self-attention/)

* causal attentional mask 
* sequence size = context window or how long is the 
* one batch = one input token with multiple dim 

![](../../figures/Vision%20Transformer%20(ViT)-1.png)

## Single head attention

Code 
```python

import torch

import torch.nn as nn

from torchtyping import TensorType

  

# 0. Instantiate the linear layers in the following order: Key, Query, Value.

# 1. Biases are not used in Attention, so for all 3 nn.Linear() instances, pass in bias=False.

# 2. torch.transpose(tensor, 1, 2) returns a B x T x A tensor as a B x A x T tensor.

# 3. This function is useful:

#    https://pytorch.org/docs/stable/generated/torch.nn.functional.softmax.html

# 4. Apply the masking to the TxT scores BEFORE calling softmax() so that the future

#    tokens don't get factored in at all.

#    To do this, set the "future" indices to float('-inf') since e^(-infinity) is 0.

# 5. To implement masking, note that in PyTorch, tensor == 0 returns a same-shape tensor

#    of booleans. Also look into utilizing torch.ones(), torch.tril(), and tensor.masked_fill(),

#    in that order.

class SingleHeadAttention(nn.Module):

    def __init__(self, embedding_dim: int, attention_dim: int):

        super().__init__()

        torch.manual_seed(0)

        self.key = nn.Linear(embedding_dim, attention_dim, bias=False)

        self.query = nn.Linear(embedding_dim, attention_dim, bias=False)

        self.value = nn.Linear(embedding_dim, attention_dim, bias=False)

        self.attention_dim = attention_dim

  
    def forward(self, embedded: TensorType[float]) -> TensorType[float]:

        # Return your answer to 4 decimal places
        k = self.key(embedded).transpose(1, 2)

        q = self.query(embedded)

        v = self.value(embedded)

        # get dim
        batch_dim, seq_dim, _ = embedded.shape

        # causal mask
        causal_mask = torch.full((batch_dim, seq_dim, seq_dim), float('-inf'))
        causal_mask = torch.triu(causal_mask, diagonal=1) # shift to creat upper mask 

        # compute probs from key and query, scale the variance and add mask to prevent 
        # lookingto futur with upper triangle with -inf
        attention_probs = torch.matmul(q, k) / math.sqrt(self.attention_dim)

        attention_probs += causal_mask # upper trinagle with -inf (prevent future)

        attention_probs = torch.nn.functional.softmax(attention_probs, dim=-1) # compute prob

        print(attention_probs)

        return torch.matmul(attention_probs, v).round(decimals=4) # output of single attention
        



```


good explanation : 

https://www.youtube.com/watch?v=eMlx5fFNoYc



## Multi Head attention
![](../../figures/Vision%20Transformer%20(ViT).avif)


**Inputs:**

- `embedding_dim` - the input dimensionality where `embedding_dim > 0`.
- `attention_dim` - the output dimensionality where `attention_dim > 0`.
- `num_heads` - number of self-attention instances where `num_heads > 0` and `attention_dim % num_heads = 0`.
- `embedded` - the input to `forward()` where `embedded.shape = (batch_size, context_length, embedding_dim)`.



``` python
import torch

import torch.nn as nn

from torchtyping import TensorType

  

class MultiHeadedSelfAttention(nn.Module):

    def __init__(self, embedding_dim: int, num_heads: int):

        super().__init__()

        torch.manual_seed(0)

        # Hint: nn.ModuleList() will be useful. It works the same as a Python list

        # but is useful here since instance variables of any subclass of nn.Module

        # must also be subclasses of nn.Module
        head_size = int(embedding_dim / num_heads) # simple divide attention dim to the number of head u want to implement 
       
        
        self.num_heads = num_heads
        # Use self.SingleHeadAttention(embedding_dim, head_size) to instantiate. You have to calculate head_size.
        self.heads = nn.ModuleList( 
	        self.SingleHeadAttention(embedding_dim, head_size) for _ in range(num_heads)
	         )
        


    def forward(self, embedded: TensorType[float]) -> TensorType[float]:

        # Return answer to 4 decimal places
		# Apply each attention head
		head_outputs = [head(embedded) for i, head in enumerate(self.heads)]

		# Concatenate the outputs from all heads
		output = torch.cat(head_outputs, dim=-1)

        # Round to 4 decimal places
	    return torch.round(output, decimals=4)>

	# alternative implementation of the previous
    class SingleHeadAttention(nn.Module):

        def __init__(self, embedding_dim: int, attention_dim: int):

            super().__init__()

            torch.manual_seed(0)

            self.key_gen = nn.Linear(embedding_dim, attention_dim, bias=False)

            self.query_gen = nn.Linear(embedding_dim, attention_dim, bias=False)

            self.value_gen = nn.Linear(embedding_dim, attention_dim, bias=False)

        def forward(self, embedded: TensorType[float]) -> TensorType[float]:

            k = self.key_gen(embedded)

            q = self.query_gen(embedded)

            v = self.value_gen(embedded)

  

            scores = q @ torch.transpose(k, 1, 2) # @ is the same as torch.matmul()
            # context length is the same as sequence length
            context_length, attention_dim = k.shape[1], k.shape[2] 

            scores = scores / (attention_dim ** 0.5)

  

            lower_triangular = torch.tril(torch.ones(context_length, context_length))

            mask = lower_triangular == 0 # retunr bool of the comparision

            scores = scores.masked_fill(mask, float('-inf')) # top right is true , lower is flase; is using for indexing / also can use assign like scores = 

            scores = nn.functional.softmax(scores, dim = 2)
  
            return scores @ v
```



* Transformers models  are data agnostic
* any data that is sequence and can be token