#datascience #AI_session #pytorch #deeplearning


https://learn.nvidia.com/courses/course?course_id=course-v1:DLI+C-FX-01+V3&unit=block-v1:DLI+C-FX-01+V3+type@vertical+block@e2b8cfd88f2a45c89ef908f7929c266c

https://docs.google.com/presentation/d/1h7PddQc6wuUXsjOSl5bq2ttWXtcDqveiFJoNh7OJNos/edit?usp=sharing

![](../../figures/Fundamentals%20of%20Deep%20Learning-1.pdf)



![](../../figures/Fundamentals%20of%20Deep%20Learning.png)

# Notes 
## Softmax 




ootnote for softmax:The floating point numbers we use in computers get inaccurate or out of bounds for big values. You can achieve better numerical stability for the softmax calculation with a trick (already implemented like this in torch/numpy): [https://jamesmccaffrey.wordpress.com/2016/03/04/the-max-trick-when-computing-softmax/](https://jamesmccaffrey.wordpress.com/2016/03/04/the-max-trick-when-computing-softmax/)


```
def my_softmax(logits):
    max_x = torch.max(logits)
    return torch.exp(logits - max_x) / torch.sum(torch.exp(logits - max_x))
```


## Multiple GPU training 
#tocheck

https://pytorch.org/tutorials/beginner/ddp_series_fault_tolerance.html
[https://github.com/huggingface/accelerate](https://github.com/huggingface/accelerate)

Multi-GPU training (also integrated in the Trainer class in huggingface transformers [https://huggingface.co/learn/nlp-course/en/chapter3/3](https://huggingface.co/learn/nlp-course/en/chapter3/3))

[https://pytorch.org/tutorials/beginner/ddp_series_fault_tolerance.html](https://pytorch.org/tutorials/beginner/ddp_series_fault_tolerance.html) (torchrun, alternative to accelerate. looks more complicated to me on first glance !


To check which device a model is on, we can check which device the model parameters are on. Check out this [stack overflow](https://stackoverflow.com/questions/58926054/how-to-get-the-device-type-of-a-pytorch-module-conveniently) post for more information.

```python
next(model.parameters()).device

--> device(type='cuda', index=0)
```


# Batch size 

Are batch sizes of powers of two necessary: [https://www.toolify.ai/ai-news/is-using-powers-of-two-as-batch-sizes-really-necessary-953304](https://www.toolify.ai/ai-news/is-using-powers-of-two-as-batch-sizes-really-necessary-953304)


# Optimizer 
#tocheck

Adam : [https://www.youtube.com/watch?v=MD2fYip6QsQ](https://www.youtube.com/watch?v=MD2fYip6QsQ)



# Image kernels 
#CV


https://setosa.io/ev/image-kernels/


# Transfer learning 

### Freezing the Base Model[](http://dli-69a8471a1f06-834fc9.aws.labs.courses.nvidia.com/lab/lab/tree/05b_presidential_doggy_door.ipynb#5b.2.2-Freezing-the-Base-Model)

Before we add our new layers onto the [pre-trained model](https://developers.google.com/machine-learning/glossary#pre-trained-model), let's take an important step: freezing the model's pre-trained layers. This means that when we train, we will not update the base layers from the pre-trained model. Instead we will only update the new layers that we add on the end for our new classification. We freeze the initial layers because we want to retain the learning achieved from training on the ImageNet dataset. If they were unfrozen at this stage, we would likely destroy this valuable information. There will be an option to unfreeze and train these layers later, in a process called fine-tuning.

Freezing the base layers is as simple as setting [requires_grad_](https://pytorch.org/docs/stable/generated/torch.Tensor.requires_grad.html) on the model to `False`.


```python
vgg_model.requires_grad_(False)
print("VGG16 Frozen")
```
### Adding New Layers

We can now add the new trainable layers to the pre-trained model. They will take the features from the pre-trained layers and turn them into predictions on the new dataset. We will add two layers to the model. In a previous lesson, we created our own [custom module](https://pytorch.org/tutorials/beginner/examples_nn/two_layer_net_module.html). A transfer learning module works in the exact same way. We can use is a layer in a [Sequential Model](https://pytorch.org/docs/stable/generated/torch.nn.Sequential.html).

Then, we'll add a `Linear` layer connecting all `1000` of VGG16's outputs to `1` neuron.

```python 
N_CLASSES = 1

my_model = nn.Sequential(
    vgg_model,
    nn.Linear(1000, N_CLASSES)
)

my_model.to(device)
```