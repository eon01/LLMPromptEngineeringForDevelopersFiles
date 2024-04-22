# Setting up the environment


## Choosing the model


## Choosing the programming language


## Installing the prerequisites


```bash
sudo apt install python3-pip
sudo pip3 install virtualenvwrapper
```


```bash
choco install pip
pip install virtualenvwrapper-win
```


```bash
mkvirtualenv pe --python=$(which python3.9)
```


```bash
mkvirtualenv pe --python=<path to python.exe>
```


```bash
python --version
```


## Installing the OpenAI Python library


```bash
pip install openai==0.27.8
```


## Getting an OpenAI API key


```bash
export OPENAI_API_KEY=<your API key>
```


## A hello world example


```python
# Import necessary libraries
import os           # Used for accessing environment variables
import openai       # OpenAI's Python client library

# Retrieve the OpenAI API key from environment variables
openai.api_key = os.getenv("OPENAI_API_KEY")

# Print an initial greeting message from Felix, the chatbot
print("Felix: Hi there. I am Felix, the chatbot. How can I help you today?")

# Get the user's message/input
user_message = input("You: ")

# Use the OpenAI API to generate a response based on the user's message
response = openai.ChatCompletion.create(
  model="gpt-3.5-turbo",  # Specify the model to be used
  messages=[
    # System message to set the behavior of the assistant
    {
      "role": "system",
      "content": "You are smart and helpful assistant."
    },
    # Initial user message (this is hardcoded and not used for any dynamic interaction)
    {
      "role": "user",
      "content": "Hi there."
    },
    # Initial assistant's response to the hardcoded user message
    {
      "role": "assistant",
      "content": "Hi there. \n\nI am Felix, the chatbot.\n\nHow can I help you today?"
    },
    # Actual user's message that was inputted earlier
    {
      "role": "user",
      "content": f"{user_message}"
    },
  ],
  # Parameters to guide the generation of the response
  temperature=1,          # Controls randomness. Higher values make output more random.
  max_tokens=256,         # Maximum number of tokens (words/characters) in the response
  top_p=1,                # Nucleus sampling parameter. Controls diversity of the output.
  frequency_penalty=0,    # Controls the penalty for using frequent tokens. 0 means no penalty.
  presence_penalty=0      # Controls the penalty for using new tokens. 0 means no penalty.
)

# Try to print the generated response
try:
  print("Felix: " + response.choices[0].message.content)
# If there's an error (e.g., API error, network issues), print an error message
except:
  print("Felix: Sorry, a problem occured. Please try again later.")
```


## Interactive prompting


```bash
pip install click==8.1.7
```


```python
import os
import openai
import click

openai.api_key = os.getenv("OPENAI_API_KEY")

@click.command()
def chat_with_felix():
    """Chat with Felix, the chatbot."""
    print("Felix: Hi there. I am Felix, the chatbot. How can I help you today?")
    
    while True:  # This will keep the chat session active
        message = input("You: ")

        # Exit the loop (and the program) if the user types 'exit' or 'quit'
        if message.lower() in ['exit', 'quit']:
            print("Felix: Goodbye!")
            break

        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are smart and helpful assistant."},
                {"role": "user", "content": "Hi there."},
                {"role": "assistant", "content": "Hi there. \n\nI am Felix, the chatbot.\n\nHow can I help you today?"},
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


```bash
python app.py
```


## Interactive prompting with mutliline prompt


```text
Write a story about a robot that is trying to learn how to be a human.

The robot is called Tobor. Once Tobor acquired the ability to speak, he started to learn how to be a human. He started by learning how to think and develop a creative imagination.
```


```python
import os
import openai
import click

openai.api_key = os.getenv("OPENAI_API_KEY")

@click.command()
def chat_with_felix():
    """Chat with Felix, the chatbot."""
    print("Felix: Hi there. I am Felix, the chatbot. How can I help you today?")
    
    while True:  # This will keep the chat session active
        print("You: (Type your message. When done, type 'END' on a new line)")
        lines = []
        while True:
            line = input()
            if line == "END":
                break
            lines.append(line)
        message = '\n'.join(lines)

        # Exit the loop (and the program) if the user types 'exit' or 'quit'
        if message.lower() in ['exit', 'quit']:
            print("Felix: Goodbye!")
            break

        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are smart and helpful assistant."},
                {"role": "user", "content": "Hi there."},
                {"role": "assistant", "content": "Hi there. \n\nI am Felix, the chatbot.\n\nHow can I help you today?"},
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