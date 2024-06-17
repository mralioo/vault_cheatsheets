
#NLP #AI_session


## Transformers for Text Summarization

- Abstractive (generates new text)

	- BART
	- T5 
	- Pegasus 
	- GPT


- Extractive (no new text, picks and stitches text together) 

	- BERTSum

[BART Text Summarization vs. GPT-3 vs. BERT: An In-Depth Comparison](https://www.width.ai/post/bart-text-summarization)




# Cosine-similarity 

**![](https://lh7-us.googleusercontent.com/slidesz/AGV_vUes1X7DrnUw2t9odqNI2d-GW2sojWz54IcmAVWPhzzpHb3HKIEtJQeKmWZeG8zGxLRzFz8L46KZeBLlbAxJ0frY6dBSYpeYs11giaJ9IBU2ANd9I0hJFhGHQAIbX5Z2jOVy7_lHNnN5l1liLCQkilY8WA1-nD52=s2048?key=tdLJcBW7jZpL2kKM0R5dyA)![](https://lh7-us.googleusercontent.com/slidesz/AGV_vUdWlNzJsG_3BK8w3NqJAkjPnpkz0IcY0f7m2Chi3FAz8V3YG10b9Tsg3iarZWrzHtWGijsZb0-_gb3FqYI9YvsCYTtyWKZ0UR78oev0EvxYwRApHAB2bV5Hx4dS7HwDDhMVrSeyvrNcF5HuYKBLlfWn0uUhZB6c=s2048?key=tdLJcBW7jZpL2kKM0R5dyA)




Number of token should be same to input of the model , can be :

- Can be word
- set of words / sentences
- voice
should be unique , and is unit of conversation, arbitrary number and 



# BFloat16
#LLM 

Float32, Float16 or BFloat16! Why does that matter for Deep Learning?


Those are just different levels of precision. Float32 is a way to represent a floating point number with 32 bits (1 or 0), and Float16 / BFloat16 is a way to represent the same number with just **16 bits**. With Float32, we allocate the first bit to represent the sign, the next 8 bits to represent the exponent, and the next 23 bits to represent the decimal points (also called Mantissa). We can go from the bits representation to the decimal representation by using the simple formula:

Float32 = (-1)^sign * 2^(exponent - 127) * (1 + mantissa)

And this can range between -3.4e^38 and 3.4e^38.

Float16 uses 1 bit for the sign, 5 bits for the exponent, and 10 bits for the Mantissa with the formula:

Float16 = (-1)^sign * 2^(exponent - 15) * (1 + mantissa)

And the range is between -6.55e^4 and 6.55e^4 (so a much smaller range!). To convert from Float32 to Float16, you just need to remove the digits that cannot fit in the 5 and 10 bits allocated for the exponent and the Mantissa. For the Mantissa, you are just creating a rounding error, but if the Float32 number is greater than 6.55e^4, you will create a float overflow error! So, it is quite possible to get conversion errors from Float32 to Float16.

Brain Float 16 (BFloat16) is another float representation in 16 bits. We give less decimal precision but as much range as Float32. We have 8 bits for the exponent and 7 bits for the Mantissa with the same conversion formula:

BFloat16 = (-1)^sign * 2^(exponent - 127) * (1 + mantissa)

Giving the same range as the Float32 [-3.4e^38 and 3.4e^38]. So, converting to BFloat16 from Float32 is trivial because you just need to round down the Mantissa.

This is quite important for Deep Learning because, in the backpropagation algorithm, the model parameters are updated by a gradient descent optimizer (e.g., Adam), and the computations are done with Float32 precision to ensure fewer rounding errors. The model parameters and the gradients are usually stored in memory in Float16 to reduce the pressure on the memory, so we need to convert back and forth between Float16 and Float32. BFloat16 is a good choice because it prevents float overflow errors while keeping enough precision for the forward and backward passes of the backpropagation algorithm.


![](../../figures/Text%20Summarization.jpg)

# LoRA Adapters
#algorithms


LoRA Adapters are, to me, one of the smartest strategies used in Machine Learning in recent years! It is one of those things where I think, "Wait! How didn't we think about that before?"

LoRA adapters came as a very natural strategy for fine-tuning models. The idea is to realize that any matrix of model parameters in a neural network of a trained model is just a sum of the initial values and the following gradient descent updates learned on the training data mini-batches:

𝜃(trained model) = 𝜃(initial value) + gradient descent updates

From there, we can understand a fine-tuned model as a set of model parameters where we continued to aggregate the gradients further on some specialized dataset:

𝜃(fine-tuned) = 𝜃(trained model) + more gradient descent updates

When we realize that we can decompose the pretraining learning and the fine-tuning learning into those 2 terms:

𝜃(fine-tuned) = 𝜃(trained model) + ΔW

then we understand that we don't need that decomposition to happen into the same matrix; we could sum the output of 2 different matrices instead. That is the idea behind LoRA: we allocate new weight parameters that will specialize in learning the fine-tuning data, and we freeze the original weights.

As such, it is not very interesting because new matrices of model parameters would just double the required memory allocated to the model. So, the trick is to use a low-rank matrix approximation to reduce the number of operations and required memory. We introduce 2 new matrices A and B to approximate ΔW:

ΔW ~ BA

It is important to realize that, typically, the amount of training data used for fine-tuning is much smaller than the data used for pretraining. As a consequence, it is unlikely that we could even have enough data to get good statistical convergence on the full matrix ΔW. The low-rank approximation acts as a regularization technique that will help the model generalize better on unseen data.

![](../../figures/Text%20Summarization-1.jpg)