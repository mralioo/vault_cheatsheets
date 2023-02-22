## Introduction to gradients and automatic differentiation
https://www.tensorflow.org/guide/autodiff
### Computing gradients

To differentiate automatically, TensorFlow **needs to remember what operations happen in what order** during the _forward_ pass. Then, during the _backward pass_, TensorFlow **traverses** this list of operations in reverse order to compute gradients.

### Gradient tapes

[`tf.GradientTape`](https://www.tensorflow.org/api_docs/python/tf/GradientTape) API for automatic differentiation; that is, computing the gradient of a computation with respect to some inputs, usually [`tf.Variable`](https://www.tensorflow.org/api_docs/python/tf/Variable)s. TensorFlow "records" relevant operations executed inside the context of a [`tf.GradientTape`](https://www.tensorflow.org/api_docs/python/tf/GradientTape) onto a "tape". TensorFlow then uses that tape to compute the gradients of a "recorded" computation using [reverse mode differentiation](https://en.wikipedia.org/wiki/Automatic_differentiation).

```python
x = tf.Variable(3.0)
with tf.GradientTape() as tape:
	y = x**2
```

use [`GradientTape.gradient(target, sources)`](https://www.tensorflow.org/api_docs/python/tf/GradientTape#gradient) to calculate the gradient of some target (often a loss) relative to some source (often the model's variables)

```
# dy = 2x * dx
dy_dx = tape.gradient(y, x)
dy_dx.numpy()

>> 6.0
```




## OOP 

[custom_layers_and_models](https://www.tensorflow.org/guide/keras/custom_layers_and_models)


### The `Model` class

The `Model` class has the same API as `Layer`, with the following differences:

-   It exposes built-in training, evaluation, and prediction loops (`model.fit()`, `model.evaluate()`, `model.predict()`).
-   It exposes the list of its inner layers, via the `model.layers` property.
-   It exposes saving and serialization APIs (`save()`, `save_weights()`...)