#NLP #LLM #DNN 


Slide : https://docs.google.com/presentation/d/1dqXrTicABaeswUHuYOEyqywa-8e6IRJU_0lihqYZaFA/edit?usp=sharing

* https://docs.google.com/presentation/d/1O0j4Pz1Ef8rHzrptKRlR5M5Lu9tomUqtJYrH7ZtHxHc/edit?usp=sharing

Notebook : https://colab.research.google.com/drive/1WC6M0f1ycusNmAPUIwiMZN27reMmOF2O?usp=sharing


transcript speech to text using whisper model and 
### OpenAI’s Whisper

![](https://lh7-us.googleusercontent.com/slidesz/AGV_vUeZSNiIIKAKcSsDLf4A8bRRMUTZyiwInTIuN7a1WEeurwks_9zbhOBn6YihX1Gsz96cLWi4dLg3f89UXPVI2jr2e9PAj-x4RMHmMZ1gVFevx3Z3qt3Wx_LTU-XZO--72MfWhQwnC7GqNgh2ap3A2OnK5GOWtRA=s2048?key=x4ytx-bu5gxo1jL03Kq2DQ)**






## Phi model



### phi-3 model : 
https://huggingface.co/microsoft/Phi-3-mini-4k-instruct



instruct : models that always predict the next token 



prompt formatting : 


better use :

```python 

    "Consider the following text:\n"
    "\n"
    f"{transcription}\n"
    "\n"
    "Please provide me a summary of that text."


```

## Evaluation

consider the following assigemt of a student , 





please first provide a thorough analysis of the students summary and then grade it according to german grade system 1 to 6 


In context learning![](../../figures/Speech2Summary.png)



representation learning 
