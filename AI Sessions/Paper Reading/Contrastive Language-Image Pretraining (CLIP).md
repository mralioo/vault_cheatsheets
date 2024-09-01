paper:
slide: https://docs.google.com/presentation/d/1LPFog15Cy5ZUw7gUA671_HIqGl_Giy0pof-d9nFyDQg/edit?usp=sharing
code: [Tutorial_Part7_Intro_to_CLIP_ZeroShot_Classification.ipynb - Colab (google.com)](https://colab.research.google.com/github/aihpi/image-dataset-curation-workshops/blob/main/notebooks/Tutorial_Part7_Intro_to_CLIP_ZeroShot_Classification.ipynb)
tutorial_code : https://colab.research.google.com/drive/1E2bYmDaX5zCGWNmD9gtSlxWTY2a13FUx?usp=sharing
## An Overview of CLIP

![](../../figures/Contrastive%20Language-Image%20Pretraining%20(CLIP).png)

CLIP works with two encoders. These are two separate neural networks. One produces embeddings for text and another that produces embeddings for images. Both models “speak the same language” by encoding similar concepts in text and images. These go into embedding vectors of the same size. The text encoder is a BERT-like transformer model or a Continuous Bag of Words (CBOW) model. The Image Encoder can be either a Vision Transformer or a Resnet.

  
Given $N$ images and their $N$ matching descriptions, CLIP is trained to predict which of the $NXN$ matchings of image pairs occurred.

We jointly train an image encoder and a text encoder to maximize cosine similarity of the matching $N$ pairs (main diagonal on the plot), while minimizing

similarity of tne $N ^ 2 - N$ incorrect pairings.

The cross entropy of images to text and the cross entropy of text to images is added and averaged. This is called 'symmetric cross entropy' and is the 'contrastive loss' used to train the model. The pseudocde of the method, from the original paper by OpenAI, is shown below.


[IBM Technology - YouTube](https://www.youtube.com/@IBMTechnology)


## Models Difference 

### Generative models : 
	* GANS
	* GPT
	* Diffusion models 
	* Denoising models DALLE using the CLIP to generate image

### Discriminative models : (classifier/regression)
	* zero shot classifier

[The Physics Principle That Inspired Modern AI Art | Quanta Magazine](https://www.quantamagazine.org/the-physics-principle-that-inspired-modern-ai-art-20230105/)

## Application 

1) zero-shot image classification
2) similarity search ( Use CLIP to check the similarity btw 2 images)
3) A component in diffusion models for image generation