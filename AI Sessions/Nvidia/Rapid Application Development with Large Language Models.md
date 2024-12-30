Link : https://learn.nvidia.com/courses/course?course_id=course-v1:DLI+S-FX-20+V1&unit=block-v1:DLI+S-FX-20+V1+type@vertical+block@e2b8cfd88f2a45c89ef908f7929c266c

Code : Check downloaded folder (it contains multiple notebooks)

## Notebook 1: Getting Started With Large Language Models

# Part 1 : 
## Pulling In Our First LLM " BERT model"

Rather than building models from scratch, this course focuses on using powerful tools to simplify the process. A great place to start is **HuggingFace**.

[**HuggingFace**](https://huggingface.co/) is an open-source community that offers simple strategies for accessing, developing, and hosting large deep learning models for testing and deployment. The topics they support span many tasks and modalities, but we'll be focusing on large language models (**LLMs**) for most of this course.

When searching through the [**HuggingFace Models catalog**](https://huggingface.co/models?sort=downloads&search=bert), you'll quickly stumble upon the [`bert-base-uncased`](https://huggingface.co/bert-base-uncased) model. Taking a look at its card, you'll see several interesting things:

- **The [`transformers`](https://github.com/huggingface/transformers) Package**: Loading in the model requires the use of the [`transformers`](https://github.com/huggingface/transformers) package, which is HuggingFace’s primary library for language models. Its name refers to the transformer architecture, which we’ll explore further in upcoming sections. You’ll be using `transformers` throughout this course, so feel free to dive into the source code as you go.

- **Pipelines**: HuggingFace simplifies complex deep learning tasks with its [Pipeline API](https://huggingface.co/docs/transformers/main_classes/pipelines). A pipeline allows you to move from input to output without worrying about the internal workings. We’ll demonstrate this with the `bert-base-uncased` model for mask-filling.

As a representative example, we can go ahead and pull in the discussed [`bert-base-uncased`](https://huggingface.co/bert-base-uncased) model and test it out:

While the pipeline provides a clean interface—taking strings in and returning a dictionary—it doesn’t give us much insight into what’s happening under the hood. Let’s peel back a layer and examine the approximate inner workings:

``` python
from transformers import AutoTokenizer, BertTokenizer, BertModel, FillMaskPipeline, AutoModelForMaskedLM, BertForMaskedLM, BertForPreTraining 

from transformers import AutoTokenizer, AutoModel        ## General-purpose fully-automatic
from transformers import AutoModelForMaskedLM            ## Default import for FillMaskPipeline
from transformers import BertTokenizer, BertForMaskedLM  ## Realized components after automatic resolution

class MyMlmPipeline(FillMaskPipeline):
    ## My Masked Language Modeling Pipeline

    ### CASE 0: Construct your pipeline automatically by pulling in the components
    ###   with their respective configs from HuggingFace. Pipeline assumes preprocessing/postprocessing.
    def __init__(self, *args, **kwargs):
        ## The fully-automatic version
        super().__init__(
            tokenizer = AutoTokenizer.from_pretrained('bert-base-uncased'),
            model = AutoModelForMaskedLM.from_pretrained("bert-base-uncased"),
            # model = BertForMaskedLM.from_pretrained("bert-base-uncased"),
            *args, **kwargs  ## <- pass in any extra arguments
        )

    ### CASE 1: Uncomment out the __call__ method to see what data is flowing.
    
    def __call__(self, string, verbose=True):
        ## Verbose argument just there for our convenience
        input_tensors = self.preprocess(string)
        if verbose: print('\npreprocess outputs:\n', input_tensors, '\n')
        output_tensors = self.forward(input_tensors)
        if verbose: print('forward outputs:\n', output_tensors, '\n')
        output = self.postprocess(output_tensors)
        return output
    
    
    ### CASE 2: Uncomment out the manual overrides below to verify the pipeline still works
    
    def preprocess(self, string):
        string = [string] if isinstance(string, str) else string
        inputs = self.tokenizer(string, return_tensors="pt")           ### strings -> indices
        inputs = {k: v.to("cuda") for k, v in inputs.items()}          ### move to GPU
        return inputs

    def forward(self, tensor_dict):
        output_tensors = self.model.forward(**tensor_dict)             ### indices -> vectors -> probabilities
        return {**output_tensors, **tensor_dict}

    def postprocess(self, tensor_dict):
        ## Very Task-specific; see FillMaskPipeline.postprocess
        tensor_dict = {k: v.to("cpu") for k, v in tensor_dict.items()} ### move off GPU
        return super().postprocess(tensor_dict)                        ### probabilities (or vectors) -> outputs
    
        
unmasker = MyMlmPipeline(device="cuda")
unmasker("Hello, Mr. Bert! How is it [MASK]?")

```

## **Notebook 2:** LLM Architecture Intuitions

```python
## Specifying multiple sentences
unmasker.tokenizer("Hello world!", "Have a great day!")

>>>{'input_ids': [101, 7592, 2088, 999, 102, 2031, 1037, 2307, 2154, 999, 102], 'token_type_ids': [0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1], 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}
```

When given our string, the **tokenizer** responds with several components:

- `input_ids`: These are just the IDs of the tokens that make up our sentence. Said tokens can be words, punctuation, letters, whatever. Just individual entries out of a set vocabulary, exactly like classes.
- `token_type_ids`: A flag indicating whether a token is part of the first or second sentence, which is useful for BERT’s training and select tasks but will not affect us.
- `attention_mask`: Regulates which sequence entry each other sequence entry can attend to during processing. We’ll explore this later.

For our purposes, `input_ids` are the most crucial. These token ID sequences allow LLMs to reason about natural language by treating samples as an ordered sequence of tokens. This should feel somewhat familiar if you’ve worked with classification tasks in deep learning, although reasoning over sequences introduces additional complexity.

From this, we can identify the 3 components discussed in the presentation:

- **Word Embeddings:** Semantic vectors representing the input tokens.
- **Position Embeddings**: Semantic vectors representing the position of the words.
- **Token Type Embedding**: Semantic vectors representing whether the token belongs to the first or second sentence.

Notice how the `Embedding` component is constructed with the format:

```
Embedding(in_channel, out_channel)
```

We can see from this that BERT uses 768-dimensional embeddings, and can speculate on how they are obtained. The word embeddings seem to be coming from a 30,522-dimensional vector (the number of unique tokens in the vocabulary), the positional ones from 512, and the token types from just a few. Let's explore these a bit further.

With these visualization and metric functions defined, we can view **the similarities of the embeddings in different measurement spaces:**

#### **Basic Cosine Similarity**

The following will compute the cosine similarity:

```python
plot_mtx(cosine_similarity(embeddings, embeddings), 'Cosine Sim', tokens)
```

This produces a normalized similarity matrix that shows the cosine similarity between each pair of token embeddings. While this captures angular relationships, it discards magnitude information.

#### **Softmax Attention**[](http://dli-b8c8d0f30b9c-dcf64b.azure.labs.courses.nvidia.com/lab/lab/tree/02_llm_intake.ipynb#Softmax-Attention)

As we'll soon see this idea being incorporated into the architecture, it's worth investigating what happens when we decide to transition to softmax-based similarity:

```python
plot_mtx(softmax_similarity(embeddings, embeddings), 'Softmax(x1) Sim', tokens)
```

You'll see that the matrix is no longer symmetric since we're applying softmax on a per-row basis, but it does have a nice intuitive analog when you format it as a matrix product: **Relative to the others, how much does a token contribute to every other token?** This formulation will come up later as "attention."

You'll also notice that the magnitudes are pretty small, but we can increase the magnitude of the embeddings and observe a much more polarizing similarity matrix.

```python
plot_mtx(softmax_similarity(embeddings*10, embeddings*10), 'Softmax(x10) Sim', tokens)
```

Because the metric now factors magnitude into the decision process but keeps the output bounded and under control, this is a great choice when you want to inject similarity into optimization (again, foreshadowing).

Regardless, the takehome message for word embeddings is roughly **"learned vector representation for each token based on its meaning and usage in sentences."**



#### **Investigating the Positional Embeddings**

Now that we've seen the word embeddings, we can take a look at the positional embeddings:

```python
model.bert.embeddings.position_embeddings ## -> Embedding(512, 768)
```

In contrast to the word embeddings, these are 512-dimensional and capture the semantics of the different positions in the input sequence. That way, our aggregate embedding can reason about both the words themselves and where they are in the sentence.


> [!NOTE]
> To wrap up our embedding discussions, we do still have our **token_type_embedding** embeddings, but they follow roughly the same logic; take in some extra semantic information about the sentence structure, encode it to some compatable representation, and incorporate it into the input. The authors saw this extra information as useful for supervising the model's training routine, so the overall embedding definition for BERT is:
> 
> `embed = WordEmbed[token] + PosEmbed[pos] + TypeEmbed[pos]`
>```python
>model.bert.embeddings
> 
> BertEmbeddings(
  (word_embeddings): Embedding(30522, 768, padding_idx=0)
  (position_embeddings): Embedding(512, 768)
  (token_type_embeddings): Embedding(2, 768)
  (LayerNorm): LayerNorm((768,), eps=1e-12, elementwise_affine=True)
  (dropout): Dropout(p=0.1, inplace=False)
)

Then at the end, the LayerNorm section and Dropout section are also included, and these will permeate your architectures going forward. A light discussion is sufficient to motivate them:

- The [**LayerNorm layer**](https://pytorch.org/docs/stable/generated/torch.nn.LayerNorm.html) normalizes the data flowing through it so that each minibatch subscribes to a similar distribution. You've probably seen [**BatchNorm**](https://pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html) from computer vision; this has a similar logic, but now the normalization covers the layer outputs instead of the batch. For some more discussion, we found the [**PowerNorm paper**](https://arxiv.org/abs/2003.07845) helpful.
- The [**Dropout layer**](https://pytorch.org/docs/stable/generated/torch.nn.Dropout.html) just masks out some of the values during training. You've probably seen this before, and the logic is the same as usual; prevent over-reliance on a selection of features and distribute the logic throughout the network.

**HuggingFace**, being open-source, provides insight into these components. You can find the BERT implementation in [`transformers/models/bert/modeling_bert.py`](https://github.com/huggingface/transformers/blob/0a365c3e6a0e174302debff4023182838607acf1/src/transformers/models/bert/modeling_bert.py#L180C11-L180C11), which may shed light on some lingering questions like "is this addition or concatenation" (it's addition) or "do we need more considerations to make this scheme work in practice" (yes).
## From Token-Level to Sequence-Level Reasoning
**To summarize the key points of the LLM intake strategy:**

- A passage is **tokenized** into an ordered sequence of tokens (unique class instances).
- The tokens are **embedded** into a vector that captures useful semantic features (meaning, position, etc).

**With this, we have some obvious options for how to reason about our data:**

1. **Token-by-token reasoning**: Treat each token in isolation (like classification tasks).
    - **Problem**: Tokens need to understand the context of other tokens in the sequence.
2. **Dense layer approach**: Reason about the sequence as a whole by combining all tokens at once.
    - **Problem**: Creates an intractable neural network.

> [!important] 
> The LLM solution is to do something between those two options:
> 
> > Allow reasoning to be done **on each token _(Token-Level Reasoning)_**, but also allow for small opportunities in which the network can **consider the sequence as a whole _(Sequence-Level Reasoning)_**!
> 
> That's where the **transformer** components come in!

#### **Transformer Attention**

Transformers, introduced in the 2017 paper [*Attention Is All You Need*](https://arxiv.org/abs/1706.03762), have become central to state-of-the-art language models. This architecture uses an ***attention mechanism*** to create an interface where the other entries of the sequence can communicate semantic information to other tokens in the series.

The formulation goes as follows: If we have semantic and positional information present in our embeddings, we can train a mapping from our embeddings into three semantic spaces $K$, $Q$, and $V$:

- **$K$ (Key)** and **$Q$ (Query)** are arguments to a similarity function (recall scaled softmax attention) to guage how much weight should be given between any pair of sequence entries in the input.

- **$V$ (Value)** is the information that should pass through to the next component, and is weighted by `SoftmaxAttention(Key, Query)` to produce an output that is positionally and semantically motivated.


**In other words:** Given a semantic/position-rich sequence of $d_k$-element embeddings ($S$) and three dense layers ($K$, $Q$, and $V$) that operate per-sequence-entry, we can train a neural network to make semantic/position-driven predictions via the forward pass equation:

$$\text{Self-Attention} = \text{SoftmaxAttention}(K_s, Q_s) \cdot V_s$$$$= \frac{K_s Q_s ^T}{\sqrt{d_k}}V_s$$

**This effectively lets us produce:**
- **$K(E),V(E),Q(E)$:** Three different embeddings derived from $E$.
- **$\sigma(A)$:** Gives intuition of how each embedding relates to all the other embeddings.
- **$\sigma(A)V$:** Embedding which considers both (pre-interface) $V$ and (post-interface) attention.

![](../../figures/Rapid%20Application%20Development%20with%20Large%20Language%20Models.png)

This mechanism is known as **self-attention** because the `Key`, `Query`, and `Value` vectors are derived from the same sequence. Other types of attention will be introduced later.

#### **Seeing Attention in the BERT Encoder**[](http://dli-b8c8d0f30b9c-e7f3a6.azure.labs.courses.nvidia.com/lab/lab/tree/02_llm_intake.ipynb#Seeing-Attention-in-the-BERT-Encoder)

Now that we understand self-attention, let's look at how BERT’s encoder handles our embeddings:

``` python
BertEncoder(
  (layer): ModuleList(
    (0-11): 12 x BertLayer(
      (attention): BertAttention(
        (self): BertSdpaSelfAttention(
          (query): Linear(in_features=768, out_features=768, bias=True)
          (key): Linear(in_features=768, out_features=768, bias=True)
          (value): Linear(in_features=768, out_features=768, bias=True)
          (dropout): Dropout(p=0.1, inplace=False)
        )
        (output): BertSelfOutput(
          (dense): Linear(in_features=768, out_features=768, bias=True)
          (LayerNorm): LayerNorm((768,), eps=1e-12, elementwise_affine=True)
          (dropout): Dropout(p=0.1, inplace=False)
        )
      )
      (intermediate): BertIntermediate(
        (dense): Linear(in_features=768, out_features=3072, bias=True)
        (intermediate_act_fn): GELUActivation()
      )
      (output): BertOutput(
        (dense): Linear(in_features=3072, out_features=768, bias=True)
        (LayerNorm): LayerNorm((768,), eps=1e-12, elementwise_affine=True)
        (dropout): Dropout(p=0.1, inplace=False)
      )
    )
  )
)
```
Those interested can dig much deeper into the implementation details, a high-level overview should be sufficient to see the big picture:
- `BertSdpa(ScaledDotProductAttention)SelfAttention`: This component takes a sequence of vectors (let's call it `x`) as input and gets the `Q`, `K`, and `V` components via `query(x)`, `key(x)`, and `value(x)`, respectively. As these are all $768$-dimensional vectors - and are thereby multiplicatively compatible under transpose - the layer performs the attention computation with a few key modifications:
    - **Multi-Headed Attention:** $K$, $Q$, and $V$ are all slices up along the embedding dimension such that we get 12 trios with dimension $768/12 = 64$. This will give us 12 different attention results, allowing the network to distribute attention in a variety of ways. After all 12 results are obtained, just concatenate embedding-wise and you'll be back up to 768-feature vectors.
    - **Masked Attention:** This is less useful for BERT but explains what the `attention_mask` input is doing. Essentially, it's a true-or-false "should-I-add-negative-infinity-to-the-attention" mask to keep the model from attending to things it shouldn't. This will be more useful in later architectures.
    - **Residual Connections:** To help the network keep the token-level information propagating through the network and improve the overall gradient flow, most architectures add residual connections around the transformer components.

- `BertSelfOutput -> BertIntermediate -> BertOutput`: These are all just token-level dense layers with non-linear activations and some `LayerNorm`/`Dropout` layers mixed in for regularization. Each element in the sequence is thereby fed through an MLP *(multi-later perceptron, or multi-layer dense network)* with dimensions progressing through $768 \to 768 \to 3072 \to 768$ to create a new data representation.
# **[Part 2] Encoders and Decoders**