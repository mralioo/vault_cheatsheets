## Training with TensorFlow 

1.  [Prepare a training script](https://sagemaker.readthedocs.io/en/stable/frameworks/tensorflow/using_tf.html#prepare-a-training-script)

with useful properties about the training environment through various environment variables, including the following:

2.  `SM_MODEL_DIR`: A string that represents the local path where the training job writes the model artifacts to. After training, artifacts in this directory are uploaded to S3 for model hosting. This is different than the model_dir argument passed in your training script, which can be an S3 location. SM_MODEL_DIR is always set to /opt/ml/model.
3.  `SM_NUM_GPUS`: An integer representing the number of GPUs available to the host.
4.  `SM_OUTPUT_DATA_DIR`: A string that represents the path to the directory to write output artifacts to. Output artifacts might include checkpoints, graphs, and other files to save, but do not include model artifacts. These artifacts are compressed and uploaded to S3 to an S3 bucket with the same prefix as the model artifacts.
5.  `SM_CHANNEL_XXXX`: A string that represents the path to the directory that contains the input data for the specified channel. For example, if you specify two input channels in the TensorFlow estimator’s fit call, named ‘train’ and ‘test’, the environment variables SM_CHANNEL_TRAIN and SM_CHANNEL_TEST are set.
    

6.  [Create a sagemaker.tensorflow.TensorFlow estimator](https://sagemaker.readthedocs.io/en/stable/frameworks/tensorflow/using_tf.html#create-an-estimator)
    

The TensorFlow class allows you to run your training script on SageMaker infrastructure in a containerized environment. In this notebook, we refer to this container as the “training container.”

Configure it with the following parameters to set up the environment:
-   `entry_point`: A user-defined Python file used by the training container as the instructions for training. We will further discuss this file in the next subsection.    
-   `role`: An IAM role to make AWS service requests
-   i``nstance_type``: The type of SageMaker instance to run your training script. Set it to local if you want to run the training job on the SageMaker instance you are using to run this notebook.
-   ``model_dir``: S3 bucket URI where the checkpoint data and models can be exported to during training (default: None). To disable having model_dir passed to your training script, set model_dir=False
-   `instance_count`: The number of instances to run your training job on. Multiple instances are needed for distributed training.
-   `output_path`: the S3 bucket URI to save training output (model artifacts and output files).
-   `framework_version`: The TensorFlow version to use.    
-   `py_version`: The Python version to use.


For more information, see the [EstimatorBase API reference](https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html#sagemaker.estimator.EstimatorBase).

## Implement the training entry point

is a Python script that provides all the code for training a TensorFlow model. It is used by the SageMaker TensorFlow Estimator (TensorFlow class above) as the entry point for running the training job.

Under the hood, SageMaker TensorFlow Estimator downloads a docker image with runtime environments specified by the parameters to initiate the estimator class and it injects the training script into the docker image as the entry point to run the container.

7.  [Call the estimator’s fit method](https://sagemaker.readthedocs.io/en/stable/frameworks/tensorflow/using_tf.html#call-the-fit-method)
    