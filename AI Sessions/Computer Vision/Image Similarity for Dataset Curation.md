#CV #AI_session #DNN 


slides : https://docs.google.com/presentation/d/15XCivxOI2PUYFE8j0f9DVM8kdBbSXTgdg4h8uhoRDs4/edit?usp=sharing
Repo: [practical-computer-vision-with-pytorch/notebooks/Tutorial_Part1_Pairwise_Comparison_of_Embeddings.ipynb at main · aihpi/practical-computer-vision-with-pytorch (github.com)](https://github.com/aihpi/practical-computer-vision-with-pytorch/blob/main/notebooks/Tutorial_Part1_Pairwise_Comparison_of_Embeddings.ipynb)
Notebook: 

Sratchpad_notebook: https://colab.research.google.com/drive/1ym7521XjeRnqMJqMnDFD72pQ7ibfhZ28?usp=sharing


# data Curation 


data leakage --> 
avoid all duplicate 


# ResNet model to Embedding 

last layer is transformed to embedding 

> [!NOTE]
> 4 channel bacause of the alpha channel : 
> apha is added, making it an ARGB color scheme. The alpha channel then describes the level of transparency of that pixel. 
> it has inforamtion of the mask , Transparency containt no info, thus irrelevant for tasks like classification or object detection


> [!NOTE]
> Model.eval() , to turn off the stochastic of the inferencing woth the model 
> turn off 


[Setosa data visualization and visual explanations](https://setosa.io/#/)



#  Understanding Image Embeddings through Pairwise Comparison

## Compare image pairs with the cosine similarity

  

The cosine similarity metric is computed as the cosine of the angle between these two vectors.

  

$$ \text{Cosine Similarity} = \frac{A \cdot B}{\|A\| \|B\|} = \frac{\sum_{i=1}^{n} A_i B_i}{\sqrt{\sum_{i=1}^{n} A_i^2} \sqrt{\sum_{i=1}^{n} B_i^2}} $$

  
  
  

where:
- $A$ and $ B $ are the feature vectors of the two images.

- $ A \cdot B $ is the dot product of vectors $ A $ and $ B $.

- $ \|A\| $ and $ \|B\| $ are the magnitudes (or norms) of vectors $ A $ and $ B $.

  

The result is a value between -1 and 1 that we can interpret easily:

- **1 indicates the vectors are identical:** 0 degree angle between the vectors, like a pair of vectors comprised of `vector_a = [1, 1, 1]`, `vector_b = [1, 1, 1]`.

- **0 indicates orthogonality: no similarity**, 90 degree angle between the vectors, the most extreme case of dissimilarity, like a pair of vectors comprised of `vector_a = [1, 1, 1]`, `vector_b = [0, 0, 0]`  .

- **-1 indicates signed opposite vectors:** 180 degree angle between the vectors, like a pair of vectors comprised of `vector_a = [5, 5, 5]`, `vector_b = [-5, -5, -5]`.

  

![](https://github.com/andandandand/image-dataset-curation/blob/main/images/orthogonal.png?raw=true)

  
  

Notice that the cosine similarity will output values between 0 and 1 when the input vectors are positive. This is how we most commonly use it to compare embeddings. It's advantageous that it's bounded between these two values for similar and  It's also important to know that there are many other ways that we can use to compute distance between vectors, with the [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance) being an alternative. The cosine similarity is strongly preferred over the Euclidean distance on many information-retrieval tasks as it is indifferent to the magnitude of the embedding vectors.

  

We can implement the cosine similarity using Numpy and the following code: