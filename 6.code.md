# Chain of Thought (CoT)


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


```text
When James was 2 years old, his sister was 2*2 years old. James is now 30 years old. How old is his sister?
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: When James was 2 years old, his sister was 2*2 years old. James is now 30 years old. How old is his sister?
Felix: If James was 2 years old, then his sister was 2*2 = 4 years old.
Since James is now 30 years old, his sister would be 30 - 2 = 28 years old.
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: When Harry was 25 years old, his sister was 29 years old. Harry is now 30 years old. How old is his sister?
Felix: If Harry is now 30 years old, then his sister is 29 years old, assuming their age difference remains constant.
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
                {"role": "user", "content": "When James was 2 years old, his sister was 2*2 years old. James is now 30 years old. How old is his sister?"},
                {"role": "assistant", "content": "When James was 2 years old, his sister was 4 years old. There's 2 year differences between them and the sister is older than James. When James is 30 years old, his sister should be 32 years old."},
                {"role": "user", "content": "When Harry was 25 years old, his sister was 29 years old. Harry is now 30 years old. How old is his sister?"},
                {"role": "assistant", "content": "When Harry was 25 years old, his sister was 29 years old. There's 4 year differences between them and the sister is older than Harry. When Harry is 30 years old, his sister should be 34 years old."},
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
You: When James was 50 years old, his sister was 50/2 years old. James is now 100 years old. How old is his sister?     
Felix: When James was 50 years old, his sister was 50/2 years old, which is 25 years old. There's a 25-year difference between them and the sister is younger than James. 

Now that James is 100 years old, his sister would be 100 - 25 = 75 years old.
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: When James was 10 years old, his sister was 10*3 years old. James is now 30 years old. How old is his sister?
Felix: When James was 10 years old, his sister was 10*3 = 30 years old.
Since then, the age difference between them remains the same.
Therefore, if James is now 30 years old, his sister would be 30 + (30-10) = 50 years old.
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: When James was 10 years old, his sister was 10*3 years old. James is now 30 years old. How old is his sister?
Felix: When James was 10 years old, his sister was 10*3 = 30 years old. This age difference of 30 years remains constant over time. Since James is now 30 years old, his sister would be 30 + 30 = 60 years old.
```