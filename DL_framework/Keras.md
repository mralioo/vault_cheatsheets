#datascience #machinelearning 
## Save and load models

#deploy 

[ref_1](https://www.tensorflow.org/guide/keras/save_and_serialize)
[ref_2](https://machinelearningmastery.com/save-load-keras-deep-learning-models/)
[ref3](https://stackoverflow.com/questions/66827371/difference-between-tf-saved-model-savemodel-path-to-dir-and-tf-keras-model-sa)

### easy mode
```python

# saving a Keras model

model = ...  # Get model (Sequential, Functional Model, or Model subclass)
model.save('path/to/location')

# loading the model back 

from tensorflow import keras
reconstructed_model = keras.models.load_model('path/to/location')
reconstructed_model.fit(test_input, test_target)

```

### whole-model

A Keras model consists of multiple components:

-   The architecture, or configuration, which specifies what layers the model contain, and how they're connected.
-   A set of weights values (the "state of the model").
-   An optimizer (defined by compiling the model).
-   A set of losses and metrics (defined by compiling the model or calling `add_loss()` or `add_metric()`).

#### pb format 
```python

# loading the model back 

from tensorflow import keras
reconstructed_model = keras.models.load_model('path/to/location')
reconstructed_model.fit(test_input, test_target)](<config_in_out_doormodel = get_model()

# Train the model.
test_input = np.random.random((128, 32))
test_target = np.random.random((128, 1))
model.fit(test_input, test_target)

# Calling `save('my_model')` creates a SavedModel folder `my_model`.
model.save("my_model")

# It can be used to reconstruct the model identically.
reconstructed_model = keras.models.load_model("my_model")

# Let's check:
np.testing.assert_allclose(
    model.predict(test_input), reconstructed_model.predict(test_input)
)

# The reconstructed model is already compiled and has retained the optimizer
# state, so training can resume:
reconstructed_model.fit(test_input, test_target)>)

```


SaveModel contains:
- **assets**
- **keras_metadata.pb** : stores the model architecture, and training configuration (including the optimizer, losses, and metrics)
- **saved_model.pb**
- **variables**: contain weights

#### h5 format
```python

model = get_model()
# Train the model.
test_input = np.random.random((128, 32))
test_target = np.random.random((128, 1))
model.fit(test_input, test_target)
# Calling `save('my_model.h5')` creates a h5 file `my_model.h5`.
model.save("my_h5_model.h5")
# It can be used to reconstruct the model identically.
reconstructed_model = keras.models.load_model("my_h5_model.h5")
# Let's check:
np.testing.assert_allclose(    model.predict(test_input), reconstructed_model.predict(test_input))
# The reconstructed model is already compiled and has retained the optimizer# state, so training can resume:
reconstructed_model.fit(test_input, test_target)
```

#### Limitations

Compared to the SavedModel format, there are two things that don't get included in the H5 file:

-   **External losses & metrics** added via `model.add_loss()` & `model.add_metric()` are not saved (unlike SavedModel). If you have such losses & metrics on your model and you want to resume training, you need to add these losses back yourself after loading the model. Note that this does not apply to losses/metrics created _inside_ layers via `self.add_loss()` & `self.add_metric()`. As long as the layer gets loaded, these losses & metrics are kept, since they are part of the `call` method of the layer.
-   The **computation graph of custom objects** such as custom layers is not included in the saved file. At loading time, Keras will need access to the Python classes/functions of these objects in order to reconstruct the model. See [Custom objects](https://www.tensorflow.org/guide/keras/save_and_serialize#custom-objects).