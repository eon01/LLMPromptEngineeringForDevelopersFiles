# Zero-shot CoT Prompting


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
When James was 2 years old, his sister was 2*2 years old. James is now 30 years old. How old is his sister? Think step by step (who's older, the age difference..etc)
```


```yaml
Felix: Hi there. I am Felix, the chatbot. Let's play a game.
You: When James was 2 years old, his sister was 2*2 years old. James is now 30 years old. How old is his sister? Think step by step (who's older, the age difference..etc)
Felix: To find out how old James's sister is, we can work through the problem step by step:

1. When James was 2 years old, his sister was 2 * 2 years old, which means his sister was 4 years old at that time.

2. This implies that James's sister is two years older than him, as she was 4 years old when he was 2 years old.

3. Since James is now 30 years old, we need to account for the age difference of two years between him and his sister.

4. To calculate his sister's current age, we add the age difference to James's age: 30 + 2 = 32.

Therefore, James's sister is 32 years old.
```