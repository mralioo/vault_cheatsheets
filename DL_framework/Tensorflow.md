## OOP 

[custom_layers_and_models](https://www.tensorflow.org/guide/keras/custom_layers_and_models)


### The `Model` class

The `Model` class has the same API as `Layer`, with the following differences:

-   It exposes built-in training, evaluation, and prediction loops (`model.fit()`, `model.evaluate()`, `model.predict()`).
-   It exposes the list of its inner layers, via the `model.layers` property.
-   It exposes saving and serialization APIs (`save()`, `save_weights()`...)