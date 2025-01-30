+++
author = "Safayat Borhan"
title = "Generative AI and LLMs"
date = "2025-01-11"
description = "Generative AI and LLMs"
tags = [
    "Generative AI",
    "LLM"
]
+++

# Concepts

## Large Language Model
![alt text](image.png)
The way we interact with LLM are different than typical programming paradigm. In programming world, we write code, use libraries. In contrast LLM are able to take *natural language* or human written instruction and perform tasks. 

**Prompt**: The text we pass as a human written form.

**Inference**: The act of generating text is known as inference.

**Context Window**: The space or memory that is available to the prompt. Usually limited to few thousands words but that depends on model.

**Completion**: The output.
![alt text](image-2.png)

## Transformers
Generative algorithms are not new. There was an algorithm before called **Recurrent Neural Network**(RNN). It was mainly to predict word. But it was not very convenient to use and hard to scale. 

Language is complex. One word can mean lot of thing. 
![alt text](image-1.png)
In this example, it's hard to understand the true meaning of this sentence. 

Then **Transformers** architecture has arrived.
![alt text](image-4.png)
The input need to be tokenized first:
![alt text](image-5.png)
After the input is ready, we will pass it to embedding layer. This layer is a trainable vector embedding space. A high dimensional space where each token is represented as a vector and occupies an unique location within that space. Each token id in the vocabulary is matched to a multi dimensional verctor and the vector is learning to encode the meaning and context of individual tokens in the input sequence. 

![alt text](image-6.png)
Here, each word has been matched to a token id and each token is mapped into a vector. Let's imagine a vector size of 3:
![alt text](image-7.png)
We can plot the words into a 3 dimensional space and see the relationship between the words. 
![alt text](image-8.png)
We can calculate the distance between the words as an angle. That is the way it can be understood mathematically. 

As we add the token vector into the base of encoder and decoder, we also need to add positional encoding.  
![alt text](image-9.png)
The model processes each of the input token in parallel. By adding the positioning encoding, we preserve the information about the word order and don't loose the relevance of the position of the word in the sentence. Once we sum the input tokens and positional encoding, we pass the resulting vector to the **self attention layer**. 
![alt text](image-10.png)

The model analyzes the relationship between the tokens in input sequence. This allows the model to attend to different parts of the input sequence to better capture the textual dependencies between the words. 

![alt text](image-11.png)
This simply means multiple heads are learning in parallel. The max number of parallelism is 12-100 and varies from model to model. Each head will learn from different aspect of the language. 

Example: 
![alt text](image-12.png)
One head may seem the relationship between people entities in the sentence. 

One head may focus on activies in the sentence. 

One head may focus on the other properties.

The output is processed in **Feed forward network**. 
![alt text](image-13.png)

It's a vector of logic propotional to the probability score for each and every token. Then we pass this logics to **Softmax** layer.

![alt text](image-14.png)

This output will have probability for every single word. There will be thousands of scores. One token will have score higher than the rest. And that will be most likely predicted token. There are a number of methods we can use to vary the final selection. 

## Example
Let's consider a scenario where we want to translate french sentence to english. 

First of all, we need to tokenize the input and pass them to encoder.
![alt text](image-15.png)

Then it's passed through *Multi Headed Attention* layer. The output of this layer are passed to *Feed Forward Network* to the output of the encoder. 
Now the data passed to the decoder is the deep representation of structure and meaning of the sentence. 
The output is inserted to middle of the decoder to influence it's *Self Attention* mechanism.
![alt text](image-16.png)

Now, the start of the sequence token is added to input of the decoder. This triggers the decoder to predict the next token.
Which it does based on the contextual understanding from the encoder. 
![alt text](image-17.png)

Then the output is passed through the decoder *Feed Forward Network* and finally through *Softmax Layer*. 
At this point, we have the first token.
![alt text](image-18.png)

Continue this loop by passing the output token back to the input to trigger the generation of the next token untill the model predicts 
and end of sequence token. Final sequence token can be de-tokenized into words and we have our output.
![alt text](image-19.png)

Different models:
![alt text](image-20.png)
Decoder Only model is the most popular now a days. gpt is based on decoder only.


## Prompting and prompt engineering

**Prompt Engineering**: Sometimes model does not produce the outcome we want on the first try. We may have to revise the language of the prompt several times. 
This is called prompt engineering. Strategy is to include examples inside the context window. 

**In-context learning (ICL)**:  
![alt text](image-21.png)
In this example, we are asking the model the classify the sentiment of this model. That means, review of the movies is positive or negative.
Larger model can correctly identify the sentiment: 
![alt text](image-22.png)
Smaller model (example: GPT-2) will struggle with this below. This is *zero shot inference*.
![alt text](image-23.png)

Now prompt engineering comes into picture. If we add an example into the model (e.g., I loved this movie and sentiment is positive).
Then, the model will better chance to understand the sentiment and format of the response. The inclusion of a single example is known as *one shot inference*. 
![alt text](image-24.png)

Sometimes, a single example won't be enough to the model to learn. We can set multiple example. This is called *few shot inference*. 
We will add a positive and a negative review to the prompt. 
![alt text](image-25.png)


## Generative configuration - inference parameters
This configuration helps to configure the model so that, it can accept examples to generate it's words.

### Max new tokens
A cap on number of times the model will go through the selection process.
![alt text](image-27.png)
example:
![alt text](image-28.png)

So, as you can see, at max_new_token=200, it could not reach to max threshold due to some other constraints.
Such as, model predicting and end of sequence token. 

The output from the transformer softmax layer, is a probability distribution across the entire dictionary of word.
Here left side is the probability score.
![alt text](image-29.png)

*Greedy*: Most LLMs by default operates with greedy decoding. This way the model will always choose the word with 
highest probability. This method can word very will with short generation but is acceptable with repeated words
or repeated sequence of words. 
![alt text](image-30.png)

*Random*: But if you want to generate text that is more natural, more creative and avoids repeating words, you need to use
random sampling. 

Here, probability of banana is 0.02. So, in random sampling, this is a 2% chance that the word will be selected.
So, we reduce the likelyhood of the fact that, the word will be repeated. Depending on the settings, there is a 
possibility that, the output can be too creative. In those scenario, we need to disable greedy and enable random sampling.
![alt text](image-31.png)


### Top K

These helps to limit the random sampling and increase the chance that the output will be sensible. To limit the options, 
we will still allow some variability. We will specify some top K value which instructs the model to choose from only the k tokens, with
the highest probability.  
![alt text](image-32.png)
In this example, k is set to 3. So, we are restricting the model to choose from these 3 options. Then it selects donut randomly: 
![alt text](image-33.png)

### Top P

Alternatively, we can use top p to limit the random sampling to prediction, whoose combined probability do not exceed p. For example, if we set 
p = 0.3, then the options are cake and donut since their cumulative probability is (0.2+0.1) .3 which is equal or less than p. 
![alt text](image-34.png)

### Temperature

This parameter influences the shape of the probability distribution that the model calcuates for the next token. I.E. the higher the temperature,
the higher the randomness. The lower the temperature, the lower the randomness. 

Temperature value is a scaling factor that is applied via the final softmax layer that impacts the shape of the probability distribution of the 
next token. In contrast to the top k and top p parameters, changing the temperature alters the prediction of the model we make.

If we choose a low value of temperature, that is less than 1, the result of probability distribution of softmax layer will be strongly peaked.
The probability is concentrated into smaller number of words. We can see below in the blue bar, most of the probability here is concentrated on the word cake. 
The model will select from the distribution using random sampling and the resulting text will be less random and will closely follow the most likely words.  
![alt text](image-35.png)

Instead, if we set the temperature to a higher value, say greater than 1, then the model will calculate a broader, flatter probability distribution.
Now the probability is evenly spread across the tokens. This leads the model to generate a higher degree of randomness and more variability in the 
output compared to cool temperature setting. The text will be more creative. 
![alt text](image-36.png)


## Generative AI Project Lifecycle

![alt text](image-37.png)