#NLP #LLM #Transformer 

presentation :  https://docs.google.com/presentation/d/1LNx_1Fs4n6_5tk63I3shI0shHRqAJZhJUzaZ3aI9OAI/edit?usp=sharing


BERT visualization : 

* [jessevig/bertviz: BertViz: Visualize Attention in NLP Models (BERT, GPT2, BART, etc.) (github.com)](https://github.com/jessevig/bertviz)


[google-bert/bert-base-uncased · Hugging Face](https://huggingface.co/google-bert/bert-base-uncased)

![](https://lh7-us.googleusercontent.com/slidesz/AGV_vUfypJbLhxdqjdmkpzeP0LuzdkQHaYYsQKGGNvTWDVSyKdNIa_-8c4OgOJhapufr205Pk78dCj8xnLe7q7yki02Mc_AzBhfrtEDw9qLh3cSjG3lJkznwKE1nazrCan9wImzQjdb2kjWzMMHHo94Vr48WvP6zg65G=s2048?key=GNhf98lbYa4VBnFU7PkgHg)  

### STEPS (from down to up)

1. Inputs: Text is turned into numerical inputs  
    “Hello World” => [1064, 246]
2. Input Embedding: The inputs are converted into embeddings (index look-up)

3. Positional Encoding: position information is added to each token (i.e. “Hello” is the first token, “World” is the second token)

4. Multi-Head Attention: allows token embeddings to exchange information

5. Add & Norm: Add a residual connection and normalize the mean/variance in the embedding features for stability

6. Feed Forward: Multi-Layer Perceptron + Activation function (e.g. ReLU) for deeper learning

7. Linear + Softmax: Get probabilities for each token in the vocabulary



## Masked Language Modeling (Contextualized!) approach

![](https://lh7-us.googleusercontent.com/slidesz/AGV_vUc8xKAS54kqFCjujE68DTJaxQi-W2_6OHXn8EYvOnTQUSz1HeCi2eNpd4-PEm8vS69orL24U7bBZ1Bv0UelF8ujQT-h9RGMqBdO088-G-zPobrqkwCI8X1T1IxblsqF_YzEa_uDVqrNdgB0JrXQr8X-MBU2KYRT=s2048?key=GNhf98lbYa4VBnFU7PkgHg)


Notebook : [BERT MLM Demo (10_07_24) | Kaggle](https://www.kaggle.com/code/mrali92/bert-mlm-demo-10-07-24/edit)
## Cloze-style questions approach

https://aclanthology.org/2021.eacl-main.20.pdf
![](https://lh7-us.googleusercontent.com/slidesz/AGV_vUed0hnhP4cv_fOesJNDTN1vmYbldJs4MaTvPR4klSw9nAaPPZlTdWZhJ_ZkhGKf9vHGNx2dOllPgh59YB1W2kqvpSZZeYUYnGWpCB8tA8gD5OkzTLaVSZh2Si9te4seHMm_7qStlbE96RuVvkUOpSZkfwIBRQ=s2048?key=GNhf98lbYa4VBnFU7PkgHg)



## Other Models
#LLM #NLP 


* **ALBERT** paper with sentence order prediction: [https://arxiv.org/abs/1909.11942](https://arxiv.org/abs/1909.11942)
* Optimized BERT: **ROBERTA**: [https://arxiv.org/abs/1907.11692](https://arxiv.org/abs/1907.11692)
* Most commonly used encoder (based on **ELECTRA**) these days: **DEBERTAv3**: [https://arxiv.org/abs/2111.09543](https://arxiv.org/abs/2111.09543) 