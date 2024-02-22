# OpenAI GPT and Prompting: An Introduction


## Generative Pre-trained Transformers (GPT) models


## What is GPT and how it is different from ChatGPT?


## The GPT models series: a closer look


### GPT-3.5


### GPT-4


### Other models


## API usage vs. web interface


## Tokens


## Costs, tokens and initial prompts: how to calculate the cost of using a model


## Prompting: how does it work?


```yaml
Prompt: What is the capital of Sweden?
Response: Stockholm
```


```text
Create a list of unhealthy products that should not be bought at the supermarket.

I - 
```


```text
I - Canned foods
II - Processed meats
III - Sugary drinks
IV - Frozen foods
V - Snacks
```


```text
Create a list of unhealthy products that should not be bought at the supermarket.

1 - 
```


```text
1 - Canned foods
2 - Processed meats
...
```


```yaml
Prompt: How many legs does it have?
Response: To answer the question "How many legs does it have?", it is necessary to know which animal you are referring to.

Prompt: I have a dog.
Response: A dog has 4 legs.
```


```yaml
Prompt: Continue adding elements to the following list:

"""
apple,
"""

Response:
banana, cherry, grape, orange, pear, pineapple, strawberry, watermelon
```


```yaml
Prompt: Continue adding elements to the following list:

"""
apple, samsung,
"""

Response:
apple, samsung, acer, asus, dell, hp, huawei, lenovo, lg, microsoft, msi, nokia, oppo, razer, sony, xiaomi
```


```yaml
Prompt: Hello, how are you?
Response: I'm fine, thank you. And you?
Prompt: I'm fine too. What did you do today?
Response: ..
```


```yaml
Prompt: Write a 500-word essay about Carthage and Rome.
Response: Carthage and Rome: An Epic Rivalry of Antiquity

In the annals of history, few rivalries have had as profound an impact on the course of civilization as that of Carthage and Rome. Both empires, boasting vast territories and immense power, were destined to clash in the Mediterranean for supremacy. This confrontation, often romanticized but undeniably brutal, would determine the trajectory of the Western world for centuries to come.

...
```


```yaml
Prompt: I'm going to share with you a very long text split into multiple prompts. Every part of the text will end with an "END PART" message. You can use this  to understand where a part ends and where the next part begins. When you see the "END PART" message, reply with "NEXT PART PLEASE" until you see "END ALL".
"END ALL" means that you have reached the end of the text. You can now reply with "END ALL" to confirm that you have read the entire text. 
Are you ready?
```


## Probability and sampling: at the heart of GPT


## Understanding the API parameters


```yaml
Prompt: Write a poem about quantum physics. Be more creative.
Prompt: Write a poem about quantum physics. Only use simple words.
Prompt: Write a poem about quantum physics. Use a diverse vocabulary.
...
```


### Temperature


### Top-p


```yaml
Context: What is the best restaurant in town according to the reviews?"
```


```yaml
Response: The best restaurant in town is Restaurant A. It offers delicious cuisine and exceptional service. The reviews love it!
```


```yaml
Response: The best restaurant in town is undoubtedly Restaurant A. It's incredibly popular and highly appreciated by the critics.
```


### Top-k


### Sequence length (max_tokens)


### Presence penalty (presence_penalty)


### Frequency penalty (frequency_penalty)


### Number of responses (n)


### Best of (best_of)


## OpenAI official examples


## Using the API without coding


```text
As the sun set, [insert] with a touch of enchantment.
```


```text
As the sun set, a crowd gathered outside the building's entrance. Some of them brought firewood and logs. Eventually, the flame of hope lit up the foggy night sky, lending the scene with a touch of enchantment.
```


```python
import os
import openai

openai.api_key = os.getenv("OPENAI_API_KEY")

response = openai.Completion.create(
  model="text-davinci-003",
  prompt="As the sun set, ",
  suffix=" with a touch of enchantment.",
  temperature=1,
  max_tokens=256,
  top_p=1,
  frequency_penalty=0,
  presence_penalty=0
)
```


## Completion (deprecated)


```text
Once upon a time
```


```text
# Introduction

Discover the benefits of mindfulness meditation, a practice that transports you to a state of Zen and tranquility. Meditation, with its power of deep relaxation, is often likened to a clear blue sky. By engaging in this regular practice, you cultivate mindful attention to the present moment. Mindfulness meditation allows you to connect with yourself, calm the mental chatter, and find inner balance. Dive into this ocean of serenity and let meditation guide you to a state of calm and equilibrium.

## How to Practice
```


```text
The list of things I should not buy at the grocery store:

1. 
```


## Chat


```text
You are a helpful assistant helping users with their questions about diet and nutrition. You are talking to a person who wants to build muscle and lose fat.
```


## Insert (deprecated)


```text
When the [insert] is over, the world will be a better place.
```


## Edit (deprecated)


```text
When the moon sets, all the birds fly back to their nests.
```


```txt
Delete the word "moon" and replace it with "sun". Delete the word "fly" and replace it with "return".
```


```text
When the sun sets, all the birds return to their nests.
```


```yaml
Input: I have 2 apples and 3 oranges. My friend has 1 apple and 2 oranges.
User: Format the input as a JSON object
AI: {
  "I": {
    "apples": 2,
    "oranges": 3
  },
  "My friend": {
    "apples": 1,
    "oranges": 2
  }
}
```