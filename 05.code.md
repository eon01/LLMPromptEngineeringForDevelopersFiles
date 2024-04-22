# Few-shot Learning and Chain of Thought


## What is few-shot learning?


## Zero-shot vs few-shot learning


## Approaches to few-shot learning


### Prior knowledge about similarity


### Prior knowledge about learning


### Prior knowledge of data


## Examples of few-shot learning


```python
import os
import openai
import click

openai.api_key = os.getenv("OPENAI_API_KEY")

@click.command()
def chat_with_felix():
    """Chat with Felix, the chatbot."""
    print("Felix: Hi there. I am Felix, the chatbot. Let's play a game. Give me a number and I'll tell you if it's even or odd.")
    
    while True:
        message = input("You: ")

        if message.lower() in ['exit', 'quit']:
            print("Felix: Goodbye!")
            break

        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a smart and helpful assistant."},

                {"role": "user", "content": "1"},
                {"role": "assistant", "content": "1 is an odd number because it is not divisible by 2."},

                {"role": "user", "content": "2"},
                {"role": "assistant", "content": "2 is an even number because it is divisible by 2."},
                
                {"role": "user", "content": "3"},
                {"role": "assistant", "content": "3 is an odd number because it is not divisible by 2."},
                
                {"role": "user", "content": f"{message}"},
            ],
            temperature=1,
            max_tokens=256,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0
        )

        try:
            print("Felix:", response.choices[0].message.content)
        except:
            print("Felix: Sorry, a problem occurred. Please try again later.")

if __name__ == '__main__':
    chat_with_felix()
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game. Give me a number and I'll tell you if it's even or odd.
You: 6
exitFelix: 6 is an even number because it is divisible by 2.
You: 
Felix: Goodbye!
```


```python
import os
import openai
import click

openai.api_key = os.getenv("OPENAI_API_KEY")

@click.command()
def chat_with_felix():
    """Chat with Felix, the chatbot."""
    print("Felix: Hi there. I am Felix, the chatbot. Let's play a game. Name an animal, and I'll tell you if it's a mammal or not.")
    
    while True:
        message = input("You: ")

        if message.lower() in ['exit', 'quit']:
            print("Felix: Goodbye!")
            break

        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a smart and helpful assistant."},

                {"role": "user", "content": "dog"},
                {"role": "assistant", "content": "A dog is a mammal because it gives birth to live young and has fur."},

                {"role": "user", "content": "fish"},
                {"role": "assistant", "content": "A fish is not a mammal because it lays eggs and lives in water."},

                {"role": "user", "content": f"{message}"},
            ],
            temperature=1,
            max_tokens=256,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0
        )

        try:
            print("Felix:", response.choices[0].message.content)
        except:
            print("Felix: Sorry, a problem occurred. Please try again later.")

if __name__ == '__main__':
    chat_with_felix()
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game. Name an animal, and I'll tell you if it's a mammal or not.
You: cow
Felix: A cow is also a mammal. It is a large domesticated ungulate animal that is raised as livestock for meat, milk, and other dairy products. Cows give birth to live young and produce milk to feed their offspring.
You: Exit
Felix: Goodbye!
```


## Limitations of few-shot learning


```python
import os
import openai
import click

openai.api_key = os.getenv("OPENAI_API_KEY")

@click.command()
def chat_with_felix():
    """Chat with Felix, the chatbot."""
    print("Felix: Hi there. I am Felix, the chatbot. Let's play a game.")
    
    while True:
        message = input("You: ")

        if message.lower() in ['exit', 'quit']:
            print("Felix: Goodbye!")
            break

        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a smart and helpful assistant."},

                {"role": "user", "content": "1"},
                {"role": "assistant", "content": "X"},

                {"role": "user", "content": "2"},
                {"role": "assistant", "content": "Y"},

                {"role": "user", "content": f"{message}"},
            ],
            temperature=1,
            max_tokens=256,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0
        )

        try:
            print("Felix:", response.choices[0].message.content)
        except:
            print("Felix: Sorry, a problem occurred. Please try again later.")

if __name__ == '__main__':
    chat_with_felix()
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: 1
Felix: X
You: 2
Felix: I'm sorry, but I'm not sure what you're referring to with "2." Could you please provide more context or clarify your question?
You: 3
Felix: Z
You: 4
Felix: Z
You: 5
Felix: Z
```


```python
import os
import openai
import click

openai.api_key = os.getenv("OPENAI_API_KEY")

@click.command()
def chat_with_felix():
    """Chat with Felix, the chatbot."""
    print("Felix: Hi there. I am Felix, the chatbot. Let's play a game.")
    
    while True:
        message = input("You: ")

        if message.lower() in ['exit', 'quit']:
            print("Felix: Goodbye!")
            break

        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a smart and helpful assistant."},

                {"role": "user", "content": "1"},
                {"role": "assistant", "content": "1 is an odd number so the answer is X."},

                {"role": "user", "content": "2"},
                {"role": "assistant", "content": "2 is an even number so the answer is Y."},

                {"role": "user", "content": "3"},
                {"role": "assistant", "content": "3 is an odd number so the answer is X."},

                {"role": "user", "content": f"{message}"},
            ],
            temperature=1,
            max_tokens=256,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0
        )

        try:
            print("Felix:", response.choices[0].message.content)
        except:
            print("Felix: Sorry, a problem occurred. Please try again later.")

if __name__ == '__main__':
    chat_with_felix()
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: 10
Felix: 10 is an even number so the answer is Y.
You: 22
Felix: 22 is an even number so the answer is Y.
You: 44
Felix: 44 is an even number so the answer is Y.
You: 12
Felix: 12 is an even number so the answer is Y.
You: 13
Felix: 13 is an odd number so the answer is X.
You: 19
Felix: 19 is an odd number so the answer is X.
```