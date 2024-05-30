#scikt-learn #machinelearning #aws #experiment 


To train a Scikit-learn model by using the SageMaker Python SDK:

1.  [Prepare a training script](https://sagemaker.readthedocs.io/en/stable/frameworks/sklearn/using_sklearn.html#prepare-a-scikit-learn-training-script)
    
2.  [Create a `sagemaker.sklearn.SKLearn` Estimator](https://sagemaker.readthedocs.io/en/stable/frameworks/sklearn/using_sklearn.html#create-an-estimator)
    
3.  [Call the estimator’s `fit` method](https://sagemaker.readthedocs.io/en/stable/frameworks/sklearn/using_sklearn.html#call-the-fit-method)



## deploy model from tar.gz

#deploy

To deploy the model, first create a class with at least four required methods:

-   `model_fn` – to load the model into memory
-   `input_fn` – to transform the payload that endpoint receives into some format, before the model can process it
-   `predict_fn` – to get the model prediction
-   `output_fn` – to transform the prediction into the format required by the requester that made the request to the endpoint (after prediciton)
