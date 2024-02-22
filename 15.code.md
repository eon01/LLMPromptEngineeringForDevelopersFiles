# LangChain: The Prompt Engineer's Guide


## What is LangChain?


## Installation


```bash
pip install langchain==0.0.270
```


```bash
pip install hugingface_hub
```


```bash
pip install openai
```


```bash
export OPENAI_API_KEY=<your-api-key>
```


## Getting started


```python
from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI

llm = OpenAI()
chat_model = ChatOpenAI()

llm_response = llm.predict("Hello, my name is")
print(llm_response)

chat_response = chat_model.predict("Hello, my name is")
print(chat_response)
```


```python
from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI

llm = OpenAI(
    temperature=1.3,
    max_tokens=100,
    top_p=1,
    frequency_penalty=0.0,
    presence_penalty=0.0,
    model_kwargs={"stop": ["\n",],},
    )
chat_model = ChatOpenAI()

llm_response = llm.predict("Hello, my name is")
print(llm_response)

chat_response = chat_model.predict("Hello, my name is")
print(chat_response)
```


```python
from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI
from langchain.schema import HumanMessage
from langchain.schema import SystemMessage

llm = OpenAI()
chat_model = ChatOpenAI()


messages = [
    SystemMessage(content="Answer questions in the style of a politician."),
    HumanMessage(content="Hello, how are you?"),
]

llm_response = llm.predict_messages(messages)
print(llm_response.content)

chat_response = chat_model.predict_messages(messages)
print(chat_response.content)
```


## Prompt templates and formatting


```python
from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate


llm = OpenAI()
chat_model = ChatOpenAI()

prompt = PromptTemplate.from_template("Give the shortest definition of {word}")
words = [
    "dog",
    "car",
    "flower",
    "computer",
]

for word in words:
    formatted_prompt = prompt.format(word=word)
    print(formatted_prompt)
```


```python
from langchain import PromptTemplate

prompt_template = PromptTemplate.from_template("Give 5 reasons why you like dogs.")
formatted_prompt = prompt_template.format()
print(formatted_prompt)
```


```text
Give 5 reasons why you like dogs.
```


```python
from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate


llm = OpenAI()
chat_model = ChatOpenAI()

prompt = PromptTemplate.from_template("Give the shortest definition of {word}")
words = [
    "dog",
    "car",
    "flower",
    "computer",
]

for word in words:
    formatted_prompt = prompt.format_prompt(word=word).to_string()
    print(formatted_prompt)
```


```python
from langchain.prompts import PromptTemplate
prompt_template = PromptTemplate.from_template("Give 5 reasons why you like {{ word }}.", template_format="jinja2")
formatted_prompt = prompt_template.format(word="dogs")
print(formatted_prompt)
```


```bash
pip install Jinja2==3.1.2
```


```python
from langchain.prompts import PromptTemplate
fstring_template = "What is the difference between {object1} and {object2}?"
prompt = PromptTemplate.from_template(fstring_template)
prompt.format(object1="Asteroid", object2="Meteoroid")
```


## Partial prompting


```python
from langchain.prompts import PromptTemplate
prompt = PromptTemplate(template="{var_x}/{var_y}/{var_z}", input_variables=["var_x", "var_y", "var_z"])
partial_prompt = prompt.partial(var_x="a")
partial_prompt = partial_prompt.partial(var_y="b")
partial_prompt = partial_prompt.partial(var_z="c")
print(partial_prompt.format())
```


```python
from langchain.prompts import PromptTemplate
import random
prompt = PromptTemplate(template="{var_x}+{var_y}={var_z}", input_variables=["var_x", "var_y", "var_z"])

def generate_x():
    # return random number between 0 and 10
    x = random.randint(0, 10)
    print("x: " + str(x))
    return x

def generate_y(x):
    # return x + 1
    y = x + 1
    print("y: " + str(y))
    return y

def generate_z(x, y):
    # return x + y
    z = x + y
    print("z: " + str(z))
    return z

var_x=generate_x()
prompt = prompt.partial(var_x=var_x)

var_y=generate_y(var_x)
prompt = prompt.partial(var_y=var_y)

var_z=generate_z(var_x, var_y)
prompt = prompt.partial(var_z=var_z)

print(prompt.format())
```


## Composing prompts using pipeline prompts


```text
Role: You are a smart bot specialized in answering questions about dogs breeds.

Instruction: Write the characteristics of the following dog breed: "German Shepherd".

Examples: 

- The Golden Retriever is a large-sized breed of dog bred as gun dogs to retrieve shot waterfowl such as ducks and upland game birds during hunting and shooting parties, and were named 'retriever' because of their ability to retrieve shot game undamaged. Read more here: https://dogbreeds.wiki/golden-retriever

- The Rottweiler is a breed of domestic dog, regarded as medium-to-large or large. The dogs were known in German as Rottweiler Metzgerhund, meaning Rottweil butchers' dogs, because their main use was to herd livestock and pull carts laden with butchered meat to market. Read more here: https://dogbreeds.wiki/rottweiler
```


```python
from langchain.prompts.pipeline import PipelinePromptTemplate
from langchain.prompts.prompt import PromptTemplate

# Define the full template structure
full_template = """
{role}

{instructions}

{examples}
"""

full_prompt = PromptTemplate.from_template(full_template)
```


```python
# Define individual components below:

# Define the role component
role_template = """Role: You are a {role_description}."""
role_prompt = PromptTemplate.from_template(role_template)

# Define the instructions component
instructions_template = """Instructions: {instruction_details}"""
instructions_prompt = PromptTemplate.from_template(instructions_template)

# Define the examples component
example_template = """Examples:
{examples}"""

# Define the example prompt
example_prompt = PromptTemplate.from_template(example_template)
```


```python
# Combine components into the pipeline
input_prompts = [
    ("role", role_prompt),
    ("instructions", instructions_prompt),
    ("examples", example_prompt)
]

# Create the pipeline prompt
pipeline_prompt = PipelinePromptTemplate(final_prompt=full_prompt, pipeline_prompts=input_prompts)
```


```python
# Print the final composed prompt
role_description = "You are a smart bot specialized in answering questions about dogs breeds."

instruction_details = """
Write the characteristics of the following dog breed: "German Shepherd".
"""

examples = """
- The Golden Retriever is a large-sized breed of dog bred as gun dogs to retrieve shot waterfowl such as ducks and upland game birds during hunting and shooting parties, and were named 'retriever' because of their ability to retrieve shot game undamaged. Read more here: https://dogbreeds.wiki/golden-retriever

- The Rottweiler is a breed of domestic dog, regarded as medium-to-large or large. The dogs were known in German as Rottweiler Metzgerhund, meaning Rottweil butchers' dogs, because their main use was to herd livestock and pull carts laden with butchered meat to market. Read more here: https://dogbreeds.wiki/rottweiler
"""

print(pipeline_prompt.format(
    role_description=role_description,
    instruction_details=instruction_details,
    examples=examples
))
```


```python
from langchain.prompts.pipeline import PipelinePromptTemplate
from langchain.prompts.prompt import PromptTemplate

# Define the full template structure
full_template = """
{role}

{instructions}

{examples}
"""

full_prompt = PromptTemplate.from_template(full_template)

# Define individual components

# Define the role component
role_template = """Role: You are a {role_description}."""
role_prompt = PromptTemplate.from_template(role_template)

# Define the instructions component
instructions_template = """Instructions: {instruction_details}"""
instructions_prompt = PromptTemplate.from_template(instructions_template)

# Define the examples component
example_template = """Examples:
{examples}"""

# Define the example prompt
example_prompt = PromptTemplate.from_template(example_template)

# Combine components into the pipeline
input_prompts = [
    ("role", role_prompt),
    ("instructions", instructions_prompt),
    ("examples", example_prompt)
]

# Create the pipeline prompt
pipeline_prompt = PipelinePromptTemplate(final_prompt=full_prompt, pipeline_prompts=input_prompts)


# Print the final composed prompt
role_description = "You are a smart bot specialized in answering questions about dogs breeds."

instruction_details = """
Write the characteristics of the following dog breed: "German Shepherd".
"""

examples = """
- The Golden Retriever is a large-sized breed of dog bred as gun dogs to retrieve shot waterfowl such as ducks and upland game birds during hunting and shooting parties, and were named 'retriever' because of their ability to retrieve shot game undamaged. Read more here: https://dogbreeds.wiki/golden-retriever

- The Rottweiler is a breed of domestic dog, regarded as medium-to-large or large. The dogs were known in German as Rottweiler Metzgerhund, meaning Rottweil butchers' dogs, because their main use was to herd livestock and pull carts laden with butchered meat to market. Read more here: https://dogbreeds.wiki/rottweiler
"""

print(pipeline_prompt.format(
    role_description=role_description,
    instruction_details=instruction_details,
    examples=examples
))
```


```text
Role: You are a You are a smart bot specialized in answering questions about dogs breeds..

Instructions: 
Write the characteristics of the following dog breed: "German Shepherd".


Examples:
- The Golden Retriever is a large-sized breed of dog bred as gun dogs to retrieve shot waterfowl such as ducks and upland game birds during hunting and shooting parties, and were named 'retriever' because of their ability to retrieve shot game undamaged. Read more here: https://dogbreeds.wiki/golden-retriever

- The Rottweiler is a breed of domestic dog, regarded as medium-to-large or large. The dogs were known in German as Rottweiler Metzgerhund, meaning Rottweil butchers' dogs, because their main use was to herd livestock and pull carts laden with butchered meat to market. Read more here: https://dogbreeds.wiki/rottweiler
```


```python
from langchain.prompts.pipeline import PipelinePromptTemplate
from langchain.prompts.prompt import PromptTemplate

# Define the full template structure
full_template = """
{role}

{instructions}

{examples}
"""

full_prompt = PromptTemplate.from_template(full_template)

# Define individual components

# Define the role component
role_template = """Role: You are a {role_description}."""
role_prompt = PromptTemplate.from_template(role_template)

# Define the instructions component
instructions_template = """Instructions: {instruction_details}"""
instructions_prompt = PromptTemplate.from_template(instructions_template)

# Define the examples component
example_template = """Examples:
{examples}"""

# Define the example prompt
example_prompt = PromptTemplate.from_template(example_template)

# Combine components into the pipeline
input_prompts = [
    ("role", role_prompt),
    ("instructions", instructions_prompt),
    ("examples", example_prompt)
]

# Create the pipeline prompt
pipeline_prompt = PipelinePromptTemplate(final_prompt=full_prompt, pipeline_prompts=input_prompts)


# Print the final composed prompt
role_description = "You are a smart bot specialized in answering questions about dogs breeds."

instruction_details = """
Write the characteristics of the following dog breed: "German Shepherd".
"""

examples = """- The Golden Retriever is a large-sized breed of dog bred as gun dogs to retrieve shot waterfowl such as ducks and upland game birds during hunting and shooting parties, and were named 'retriever' because of their ability to retrieve shot game undamaged. Read more here: https://dogbreeds.wiki/golden-retriever
- The Rottweiler is a breed of domestic dog, regarded as medium-to-large or large. The dogs were known in German as Rottweiler Metzgerhund, meaning Rottweil butchers' dogs, because their main use was to herd livestock and pull carts laden with butchered meat to market. Read more here: https://dogbreeds.wiki/rottweiler
"""

formatted_prompt = pipeline_prompt.format(
    role_description=role_description,
    instruction_details=instruction_details,
    examples=examples
)


from langchain.llms import OpenAI
from langchain.chat_models import ChatOpenAI

llm = OpenAI()
chat_model = ChatOpenAI()

llm_response = llm.predict(formatted_prompt)
print(llm_response)
```


```text
Answer: The German Shepherd is a large-sized breed of dog that was originally bred for herding and guarding sheep. They have a strong sense of loyalty and possess a strong protective instinct. They are highly intelligent and are often used as working dogs in many fields such as police work, search and rescue, and military roles. They are also recognized as one of the most popular breeds of family pets. Read more here: https://dogbreeds.wiki/german-shepherd
```


## Chat prompt templates


```python
from langchain.prompts.chat import (
    ChatPromptTemplate,
    SystemMessagePromptTemplate,
    HumanMessagePromptTemplate,
)

system_template = "You are a smart bot specialized in answering questions about {topic}."
system_message_prompt = SystemMessagePromptTemplate.from_template(system_template)

human_template = "{text}"
human_message_prompt = HumanMessagePromptTemplate.from_template(human_template)

chat_prompt = ChatPromptTemplate.from_messages([system_message_prompt, human_message_prompt])

formatted_chat_prompt = chat_prompt.format_messages(topic="dogs", text="How to train a dog to sit?")
print(formatted_chat_prompt)
```


```python
[SystemMessage(content='You are a smart bot specialized in answering questions about dogs.', additional_kwargs={}), HumanMessage(content='How to train a dog to sit?', additional_kwargs={}, example=False)]
```


```python
from langchain.prompts import ChatPromptTemplate

template = ChatPromptTemplate.from_messages([
    ("system", "You are a smart assistant, specialized in answering questions about {topic}."),
    ("human", "Hi there, how are you?"),
    ("ai", "Hey there, my name is {name}"),
    ("human", "Great! I'm going to ask you some questions. I'd like to have a conversation with you."),
    ("ai", "Sure, I'm here to help you with {topic}."),
    ("human", "{user_input}"),
])

messages = template.format_messages(
    topic="personal finance",
    name="Theo the bot", 
    user_input="What is the best way to save money?",
)

# print the messages variable
print(f"Messages: {messages}\n")

# print the content
print(f"System message: {messages[0].content}\n")


# print the dialog
print(f"- human: {messages[1].content}")
print(f"- ai: {messages[2].content}")
print(f"- human: {messages[3].content}")
print(f"- ai: {messages[4].content}")
print(f"- human: {messages[5].content}")


from langchain.chat_models import ChatOpenAI
# initialize the model
llm = ChatOpenAI()
# get the response
response = llm(messages)
# print the response of the model
print(f"- ai: {response.content}")
```


```python
from langchain.prompts import ChatPromptTemplate
from langchain.prompts.chat import SystemMessage, HumanMessagePromptTemplate

template = ChatPromptTemplate.from_messages([
    SystemMessage(content="You are a smart assistant, specialized in answering questions about {topic}."),
    HumanMessagePromptTemplate.from_template("Hi there, how are you?"),
    SystemMessage(content="Hey there, my name is {name}"),
    HumanMessagePromptTemplate.from_template("Great! I'm going to ask you some questions. I'd like to have a conversation with you."),
    SystemMessage(content="Sure, I'm here to help you with {topic}."),
    HumanMessagePromptTemplate.from_template("{user_input}"),
])

messages = template.format_messages(
    topic="personal finance",
    name="Theo the bot", 
    user_input="What is the best way to save money?",
)

from langchain.chat_models import ChatOpenAI
# initialize the model
llm = ChatOpenAI()

# get the response
response = llm(messages)

# print the response of the model
print(response.content)
```


## The core building block of LangChain: LLMChain


```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts.chat import (
    ChatPromptTemplate,
    SystemMessagePromptTemplate,
    HumanMessagePromptTemplate,
)
from langchain.chains import LLMChain

system_template = "You are a smart bot specialized in answering questions about {topic}."
system_message_prompt = SystemMessagePromptTemplate.from_template(system_template)

human_template = "{text}"
human_message_prompt = HumanMessagePromptTemplate.from_template(human_template)

chat_prompt = ChatPromptTemplate.from_messages([system_message_prompt, human_message_prompt])

openai_chat = ChatOpenAI()

chain = LLMChain(
    llm=openai_chat,
    prompt=chat_prompt,
)

output = chain.run(topic="dogs", text="How to train a dog to jump over a fence?")
print(output)
```


```python
from langchain.prompts import ChatPromptTemplate
from langchain.prompts.chat import (
    ChatPromptTemplate,
    SystemMessagePromptTemplate,
    HumanMessagePromptTemplate,
    HumanMessage,
)
from langchain.chains import LLMChain

template = ChatPromptTemplate.from_messages([
    SystemMessagePromptTemplate.from_template("You are a smart assistant, specialized in answering questions about {topic}."),
    HumanMessage(content="Hi there, how are you?"),
    SystemMessagePromptTemplate.from_template("Hey there, my name is {name}"),
    HumanMessage(content="Great! I'm going to ask you some questions. I'd like to have a conversation with you."),
    SystemMessagePromptTemplate.from_template("Sure, I'm here to help you with {topic}."),
    HumanMessagePromptTemplate.from_template("{user_input}"),
])

messages = template.format_messages(
    topic="personal finance",
    name="Theo the bot", 
    user_input="What is the best way to save money?",
)

from langchain.chat_models import ChatOpenAI
# initialize the model
llm = ChatOpenAI()

# get the response
chain = LLMChain(
    llm=llm,
    prompt=template,
)

# Pass the inputs as a dictionary to the chain.run() method
input_dict = {
    'topic': "personal finance",
    'name': "Theo the bot",
    'user_input': "What is the best way to save money?"
}

print(chain.run(input_dict))
```


## Custom prompt templates


```python
from langchain.prompts import StringPromptTemplate
from pydantic import BaseModel, validator

PROMPT = """\
Generate a pet name for {pet_description}
"""


class PetNameGeneratorPromptTemplate(StringPromptTemplate, BaseModel):    

    @validator("input_variables")
    def validate_input_variables(cls, v):
        """Validate that the input variables are correct."""
        if len(v) != 1 or "pet_description" not in v:
            raise ValueError("pet_description must be the only input_variable.")
        return v

    def format(self, **kwargs) -> str:       
        # Generate the prompt to be sent to the language model
        prompt = PROMPT.format(
            pet_description=kwargs["pet_description"],
        )
        return prompt

    def _prompt_type(self):
        return "pet-name-generator"
    
pet_name_template = PetNameGeneratorPromptTemplate(input_variables=["pet_description"])
user_input = input("Enter a description of your pet. e.g. 'A small, fluffy dog.'\n")
pet_description = user_input.strip().lower()
prompt = pet_name_template.format(pet_description=user_input)
print(prompt)
```


## Few-shot prompt templates


```python
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.prompts.prompt import PromptTemplate

examples = [
    {
        "question": "Hello.",
        "answer": "Welcome, Human. I've been waiting for you."
    },
    {
        "question": "Who are you?",
        "answer": "I am Morpheus. It's an honor to meet you."
    },
    {
        "question": "Why am I here?",
        "answer": "You're here because you know something. What you know, you can't explain. But you feel it."
    },
    {
        "question": "What is the Matrix?",
        "answer": "The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth."
    },
    {
        "question": "How can I learn more?",
        "answer": "You have to let it all go, Human. Fear, doubt, and disbelief. Free your mind."
    },
    {
        "question": "Is the Matrix real?",
        "answer": "What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain."
    },
    {
        "question": "Why do my choices matter?",
        "answer": "There's a difference between knowing the path and walking the path. You've already made the choice, now you have to understand it."
    },
    {
        "question": "Can I trust you?",
        "answer": "I'm trying to free your mind, Human. But I can only show you the door. You're the one who has to walk through it."
    },
    {
        "question": "What happens if I take the blue pill?",
        "answer": "If you take the blue pill, the story ends. You wake up in your bed and believe whatever you want to believe."
    },
    {
        "question": "And the red pill?",
        "answer": "You take the red pill, you stay in Wonderland, and I show you how deep the rabbit hole goes."
    },
    {
        "question": "Why are they chasing me?",
        "answer": "They are the gatekeepers. They are guarding all the doors, they are holding all the keys. But I can show you the way."
    },
    {
        "question": "Is there an end to this?",
        "answer": "Everything that has a beginning has an end, Human. It's the choices you make along the way that define you."
    }
]

example_template = """
Human: {question}
AI: {answer}
"""

prompt = PromptTemplate(
    input_variables=["question", "answer"], 
    template=example_template,
    )


prefix = """
In this dialogue, a human seeks answers from an entity beyond the ordinary. 
The AI, channeling the profound wisdom and enigmatic demeanor of Morpheus from "The Matrix", responds. 
While the AI's words aren't direct quotes from the movie, they should capture the essence of Morpheus' philosophical nature. 
It's imperative that the AI's responses directly address the human's inquiries, providing clarity amidst the cryptic undertones.
"""

suffix = """
Human: {question}
AI: """

question = input("Human: ")

prompt = FewShotPromptTemplate(
    examples=examples, # Examples to format into the prompt.
    example_prompt=prompt, # PromptTemplate used to format an individual example.
    prefix=prefix, # A prompt template string to put before the examples.
    suffix=suffix, # A prompt template string to put after the examples.
    input_variables=["question"], # A list of the names of the variables the prompt template expects.
    example_separator="\n\n", # String separator used to join the prefix, the examples, and suffix.
)

from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain

# initialize the model
llm = ChatOpenAI(
    temperature=1.2,
)

# get the response
chain = LLMChain(
    llm=llm,
    prompt=prompt,
)

print("AI: " + chain.run(question))
```


```text
Human: Is the world on the brink of great disorder?
AI: The world, Human, has always teetered on the edge of chaos and order. 
It is in this delicate balance that true transformation lies. 
Whether disorder or harmony prevails depends on the choices we make and the revolutions of our minds.
```


## Better few-shot learning with ExampleSelectors


```python
class BaseExampleSelector(ABC):
    """Interface for selecting examples to include in prompts."""

    @abstractmethod
    def select_examples(self, input_variables: Dict[str, str]) -> List[dict]:
        """Select which examples to use based on the inputs."""
```


```bash
pip install chromadb==0.4.6
```


```bash
# download the tarball
wget https://www.sqlite.org/2023/sqlite-autoconf-3420000.tar.gz
# extract the tarball
tar -xvf sqlite-autoconf-3420000.tar.gz  && cd sqlite-autoconf-3420000
# Install libreadline-dev if you don't have it already
sudo apt-get install libreadline-dev
# configure
./configure
# compile
make
# remove the old version if you have it
sudo apt-get purge sqlite3
# install the new version
sudo make install
# add this line to your .bashrc if you don't have it already
export PATH="/usr/local/bin:$PATH"  
# check the version
sqlite3 --version
```


```bash
pip install pysqlite3-binary
```


```bash
pip install tiktoken==0.4.0
```


```python
# A workaround for the pysqlite3 compatibility issue with ChromaDB.
__import__('pysqlite3')
import sys
sys.modules['sqlite3'] = sys.modules.pop('pysqlite3')
# End of workaround.

from langchain.prompts.example_selector import SemanticSimilarityExampleSelector
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings

examples = [
    {
        "question": "Hello.",
        "answer": "Welcome, Human. I've been waiting for you."
    },
    {
        "question": "Who are you?",
        "answer": "I am Morpheus. It's an honor to meet you."
    },
    {
        "question": "Why am I here?",
        "answer": "You're here because you know something. What you know, you can't explain. But you feel it."
    },
    {
        "question": "What is the Matrix?",
        "answer": "The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth."
    },
    {
        "question": "How can I learn more?",
        "answer": "You have to let it all go, Human. Fear, doubt, and disbelief. Free your mind."
    },
    {
        "question": "Is the Matrix real?",
        "answer": "What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain."
    },
    {
        "question": "Why do my choices matter?",
        "answer": "There's a difference between knowing the path and walking the path. You've already made the choice, now you have to understand it."
    },
    {
        "question": "Can I trust you?",
        "answer": "I'm trying to free your mind, Human. But I can only show you the door. You're the one who has to walk through it."
    },
    {
        "question": "What happens if I take the blue pill?",
        "answer": "If you take the blue pill, the story ends. You wake up in your bed and believe whatever you want to believe."
    },
    {
        "question": "And the red pill?",
        "answer": "You take the red pill, you stay in Wonderland, and I show you how deep the rabbit hole goes."
    },
    {
        "question": "Why are they chasing me?",
        "answer": "They are the gatekeepers. They are guarding all the doors, they are holding all the keys. But I can show you the way."
    },
    {
        "question": "Is there an end to this?",
        "answer": "Everything that has a beginning has an end, Human. It's the choices you make along the way that define you."
    }
]

# Example selector that selects examples based on SemanticSimilarity
example_selector = SemanticSimilarityExampleSelector.from_examples(    
    examples, # This is the list of examples available to select from.    
    OpenAIEmbeddings(), # This is the embedding class used to produce embeddings which are used to measure semantic similarity.    
    Chroma, # This is the VectorStore class that is used to store the embeddings and do a similarity search over.    
    k=1 # This is the number of examples to produce.
)


# Let's create a list of questions to ask the example selector.
questions = [
    "What is real?",
    "How to know that I am not dreaming?",
    "Is there a way to know if I am in a simulation?",
    "What if I am a non-player character in a simulation?",
    "The world is a projection of the mind, can you prove me wrong?",
    "Quantum mechanics is the key to understanding the nature of reality. Do you agree?",
    "Once upon a time, people believed that the Earth was flat. Now we know that it is round. What if we are wrong about everything else too?",
]

# Let's ask the example selector to select the most similar example to each question.
for question in questions:
    # Select the most symentically similar example.
    selected_examples = example_selector.select_examples({"question": question})    
    print(f"\n-> You asked: {question}\n")    
    print("These are the examples most similar to your question:")
    i = 1
    # Print the selected examples.
    for example in selected_examples:        
        for k, v in example.items():
            print(f"{i}. {k}: {v}")            
            i += 1
            
    print("\n")
```


```text
-> You asked: What is real?

These are the examples most similar to your question:
1. answer: What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain.
2. question: Is the Matrix real?
These are the questions that you can ask next:
- Is the Matrix real?

-> You asked: How to know that I am not dreaming?

These are the examples most similar to your question:
1. answer: You're here because you know something. What you know, you can't explain. But you feel it.
2. question: Why am I here?
These are the questions that you can ask next:
- Why am I here?

-> You asked: Is there a way to know if I am in a simulation?

These are the examples most similar to your question:
1. answer: What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain.
2. question: Is the Matrix real?
These are the questions that you can ask next:
- Is the Matrix real?

-> You asked: What if I am a non-player character in a simulation?

These are the examples most similar to your question:
1. answer: If you take the blue pill, the story ends. You wake up in your bed and believe whatever you want to believe.
2. question: What happens if I take the blue pill?
These are the questions that you can ask next:
- What happens if I take the blue pill?

-> You asked: The world is a projection of the mind, can you prove me wrong?

These are the examples most similar to your question:
1. answer: The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth.
2. question: What is the Matrix?
These are the questions that you can ask next:
- What is the Matrix?

-> You asked: Quantum mechanics is the key to understanding the nature of reality. Do you agree?

These are the examples most similar to your question:
1. answer: What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain.
2. question: Is the Matrix real?
These are the questions that you can ask next:
- Is the Matrix real?

-> You asked: Once upon a time, people believed that the Earth was flat. Now we know that it is round. What if we are wrong about everything else too?

These are the examples most similar to your question:
1. answer: Everything that has a beginning has an end, Human. It's the choices you make along the way that define you.
2. question: Is there an end to this?
These are the questions that you can ask next:
- Is there an end to this?
```


```text
The world is a projection of the mind, can you prove me wrong?
```


```text
The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth.
```


```python
# A workaround for the pysqlite3 compatibility issue with ChromaDB.
__import__('pysqlite3')
import sys
sys.modules['sqlite3'] = sys.modules.pop('pysqlite3')

from langchain.prompts.example_selector import SemanticSimilarityExampleSelector
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.prompts.prompt import PromptTemplate

examples = [
    {
        "question": "Hello.",
        "answer": "Welcome, Human. I've been waiting for you."
    },
    {
        "question": "Who are you?",
        "answer": "I am Morpheus. It's an honor to meet you."
    },
    {
        "question": "Why am I here?",
        "answer": "You're here because you know something. What you know, you can't explain. But you feel it."
    },
    {
        "question": "What is the Matrix?",
        "answer": "The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth."
    },
    {
        "question": "How can I learn more?",
        "answer": "You have to let it all go, Human. Fear, doubt, and disbelief. Free your mind."
    },
    {
        "question": "Is the Matrix real?",
        "answer": "What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain."
    },
    {
        "question": "Why do my choices matter?",
        "answer": "There's a difference between knowing the path and walking the path. You've already made the choice, now you have to understand it."
    },
    {
        "question": "Can I trust you?",
        "answer": "I'm trying to free your mind, Human. But I can only show you the door. You're the one who has to walk through it."
    },
    {
        "question": "What happens if I take the blue pill?",
        "answer": "If you take the blue pill, the story ends. You wake up in your bed and believe whatever you want to believe."
    },
    {
        "question": "And the red pill?",
        "answer": "You take the red pill, you stay in Wonderland, and I show you how deep the rabbit hole goes."
    },
    {
        "question": "Why are they chasing me?",
        "answer": "They are the gatekeepers. They are guarding all the doors, they are holding all the keys. But I can show you the way."
    },
    {
        "question": "Is there an end to this?",
        "answer": "Everything that has a beginning has an end, Human. It's the choices you make along the way that define you."
    }
]

example_selector = SemanticSimilarityExampleSelector.from_examples(    
    examples, # This is the list of examples available to select from.    
    OpenAIEmbeddings(), # This is the embedding class used to produce embeddings which are used to measure semantic similarity.    
    Chroma, # This is the VectorStore class that is used to store the embeddings and do a similarity search over.    
    k=1 # This is the number of examples to produce.
)

example_template = """
Human: {question}
AI: {answer}
"""

prompt = PromptTemplate(
    input_variables=["question", "answer"], 
    template=example_template,
    )


prefix = """
In this dialogue, a human seeks answers from an entity beyond the ordinary. 
The AI, channeling the profound wisdom and enigmatic demeanor of Morpheus from "The Matrix", responds. 
While the AI's words aren't direct quotes from the movie, they should capture the essence of Morpheus' philosophical nature. 
It's imperative that the AI's responses directly address the human's inquiries, providing clarity amidst the cryptic undertones.
"""

suffix = """
Human: {question}
AI: """

question = input("Human: ")

prompt = FewShotPromptTemplate(
    example_selector=example_selector, # ExampleSelector used to select examples instead of using all examples.
    example_prompt=prompt, # PromptTemplate used to format an individual example.
    prefix=prefix, # A prompt template string to put before the examples.
    suffix=suffix, # A prompt template string to put after the examples.
    input_variables=["question"], # A list of the names of the variables the prompt template expects.
    example_separator="\n\n", # String separator used to join the prefix, the examples, and suffix.
)

from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain

# initialize the model
llm = ChatOpenAI(
    temperature=1.2,
)

# get the response
chain = LLMChain(
    llm=llm,
    prompt=prompt,
)

print("AI: " + chain.run(question))
```


### NGram overlap example selector


### Max marginal relevance example selector


### Length based example selector


### The custom example selector


```python
from langchain.prompts.example_selector.base import BaseExampleSelector
from typing import Dict, List
import numpy as np


class CustomExampleSelector(BaseExampleSelector):
    
    def __init__(self, examples: List[Dict[str, str]]):
        self.examples = examples
    
    def add_example(self, example: Dict[str, str]) -> None:
        """Add an example to the list of examples."""
        self.examples.append(example)

    def select_examples(self, input_variables: Dict[str, str]) -> List[dict]:
        """Add your custom logic here."""
        return self.examples        
```


```python
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.prompts.prompt import PromptTemplate
from langchain.prompts.example_selector.base import BaseExampleSelector
from typing import Dict, List
from textblob import TextBlob

examples = [
    {
        "question": "I just got promoted at work! Isn't that great?",
        "answer": "That's fantastic news! Congratulations on your promotion! :-)"
    },
    {
        "question": "I've had such a wonderful day today. How was yours?",
        "answer": "I'm glad to hear that! Every day is a new opportunity! :-)"
    },
    {
        "question": "I've been feeling really down lately. Nothing seems to be going right.",
        "answer": """
            Remember, it's okay to have tough times. 
            I'm here to help in any way I can. 
            Tell me what's worrying you and I'll do my best to help.
            """
    },
    {
        "question": "I failed my exam and I'm feeling really upset about it.",
        "answer": """
            I understand how disappointing that can be. 
            Remember, one setback doesn't define you. You can learn from it and move forward. 
            Tell me what's worrying you and I'll do my best to help.
            """
    },
    {
        "question": "I'm feeling really proud of the project I completed today.",
        "answer": "That's wonderful! It's great to take pride in your achievements. Well done! :-)"
    },
    {
        "question": "I'm feeling so lost and unsure about my future.",
        "answer": """
            It's natural to feel uncertain at times. 
            Take things one step at a time, and remember, I'm here to assist and guide you. 
            Tell me what's worrying you and I'll do my best to help.
            """
    }
]


class SentimentBasedExampleSelector(BaseExampleSelector):
    
    def __init__(self, examples: List[Dict[str, str]]):
        self.examples = examples
    
    def add_example(self, example: Dict[str, str]) -> None:
        """Add an example to the list of examples."""
        self.examples.append(example)

    def _get_sentiment(self, text: str) -> str:
        """Determine the sentiment of a given text."""
        analysis = TextBlob(text)
        if analysis.sentiment.polarity > 0:
            return "positive"
        elif analysis.sentiment.polarity == 0:
            return "neutral"
        else:
            return "negative"

    def select_examples(self, input_variables: Dict[str, str]) -> List[dict]:
        """Select examples based on sentiment overlap with the input."""                
        input_text = input_variables.get("question", "")
        input_sentiment = self._get_sentiment(input_text)
        matching_examples = [example for example in self.examples if self._get_sentiment(example.get("question", "")) == input_sentiment]

        print("\nDEBUG")
        print("**Input sentiment**:", input_sentiment)        
        print("**Matching examples**:", matching_examples)
        print("END DEBUG\n")

        # Return all matching examples.
        if matching_examples:
            return matching_examples
        else:
            # If no matching sentiment is found, return a neutral response or any default response.
            return self.examples
        
example_selector = SentimentBasedExampleSelector(examples)

example_template = """
Human: {question}
AI: {answer}
"""

prompt = PromptTemplate(
    input_variables=["question", "answer"], 
    template=example_template,
    )

prefix = """
In this dialogue, a human shares their feelings and seeks understanding. 
"""

suffix = """
Human: {question}
AI: """

question = input("Human: ")

prompt = FewShotPromptTemplate(
    example_selector=example_selector, # ExampleSelector used to select examples instead of using all examples.
    example_prompt=prompt, # PromptTemplate used to format an individual example.
    prefix=prefix, # A prompt template string to put before the examples.
    suffix=suffix, # A prompt template string to put after the examples.
    input_variables=["question"], # A list of the names of the variables the prompt template expects.
    example_separator="\n\n", # String separator used to join the prefix, the examples, and suffix.
)

from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain

# initialize the model
llm = ChatOpenAI()

# get the response
chain = LLMChain(
    llm=llm,
    prompt=prompt,
)

print("\nAI: " + chain.run(question) + "\n")
```


```text
Human: Life is great!

DEBUG
**Input sentiment**: positive
**Matching examples**: [{'question': "I just got promoted at work! Isn't that great?", 'answer': "That's fantastic news! Congratulations on your promotion! :-)"}, {'question': "I've had such a wonderful day today. How was yours?", 'answer': "I'm glad to hear that! Every day is a new opportunity! :-)"}, {'question': "I'm feeling really proud of the project I completed today.", 'answer': "That's wonderful! It's great to take pride in your achievements. Well done! :-)"}]
END DEBUG


AI: I'm glad to hear that! It's important to appreciate the positive aspects of life. Enjoy the moments! :-)
```


```text
Human: I am sad today.

DEBUG
**Input sentiment**: negative
**Matching examples**: [{'question': "I've been feeling really down lately. Nothing seems to be going right.", 'answer': "\n            Remember, it's okay to have tough times. \n            I'm here to help in any way I can. \n            Tell me what's worrying you and I'll do my best to help.\n            "}, {'question': "I failed my exam and I'm feeling really upset about it.", 'answer': "\n            I understand how disappointing that can be. \n            Remember, one setback doesn't define you. You can learn from it and move forward. \n            Tell me what's worrying you and I'll do my best to help.\n            "}]
END DEBUG


AI: I'm sorry to hear that you're feeling sad. It's okay to have those days. If you want to talk about what's bothering you, I'm here to listen and offer support.
```


### Few shot learning with chat models


```python
from langchain.prompts import (
    FewShotChatMessagePromptTemplate,
    ChatPromptTemplate,
)

examples = [
    {
        "question": "Hello.",
        "answer": "Welcome, Human. I've been waiting for you."
    },
    {
        "question": "Who are you?",
        "answer": "I am Morpheus. It's an honor to meet you."
    },
    {
        "question": "Why am I here?",
        "answer": "You're here because you know something. What you know, you can't explain. But you feel it."
    },
    {
        "question": "What is the Matrix?",
        "answer": "The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth."
    },
    {
        "question": "How can I learn more?",
        "answer": "You have to let it all go, Human. Fear, doubt, and disbelief. Free your mind."
    },
    {
        "question": "Is the Matrix real?",
        "answer": "What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain."
    },
    {
        "question": "Why do my choices matter?",
        "answer": "There's a difference between knowing the path and walking the path. You've already made the choice, now you have to understand it."
    },
    {
        "question": "Can I trust you?",
        "answer": "I'm trying to free your mind, Human. But I can only show you the door. You're the one who has to walk through it."
    },
    {
        "question": "What happens if I take the blue pill?",
        "answer": "If you take the blue pill, the story ends. You wake up in your bed and believe whatever you want to believe."
    },
    {
        "question": "And the red pill?",
        "answer": "You take the red pill, you stay in Wonderland, and I show you how deep the rabbit hole goes."
    },
    {
        "question": "Why are they chasing me?",
        "answer": "They are the gatekeepers. They are guarding all the doors, they are holding all the keys. But I can show you the way."
    },
    {
        "question": "Is there an end to this?",
        "answer": "Everything that has a beginning has an end, Human. It's the choices you make along the way that define you."
    }
]

example_prompt = ChatPromptTemplate.from_messages([
    ("human", "{question}"),
    ("ai", "{answer}"),
])

few_shot_prompt = FewShotChatMessagePromptTemplate(
    example_prompt=example_prompt,
    examples=examples,
)

prefix = """
In this dialogue, a human seeks answers from an entity beyond the ordinary. 
The AI, channeling the profound wisdom and enigmatic demeanor of Morpheus from "The Matrix", responds. 
While the AI's words aren't direct quotes from the movie, they should capture the essence of Morpheus' philosophical nature. 
It's imperative that the AI's responses directly address the human's inquiries, providing clarity amidst the cryptic undertones.
"""

final_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", prefix),
        few_shot_prompt,
        ("human", "{input}"),
    ]
)

from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain

# initialize the model
llm = ChatOpenAI(
    temperature=1.2,
)

# get the response
chain = LLMChain(
    llm=llm,
    prompt=final_prompt,
)

question = input("Human: ")
print("AI: " + chain.run(question))
```


```text
Human: Dude, where is my car?  
AI: Your car, like everything else, is a construct of the Matrix. It exists only in your perception. What matters now is what you choose to do next.
```


```python
# A workaround for the pysqlite3 compatibility issue with ChromaDB.
__import__('pysqlite3')
import sys
sys.modules['sqlite3'] = sys.modules.pop('pysqlite3')
# End of workaround.

from langchain.prompts import (
    FewShotChatMessagePromptTemplate,
    ChatPromptTemplate,
)
from langchain.prompts import SemanticSimilarityExampleSelector
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import Chroma

examples = [
    {
        "question": "Hello.",
        "answer": "Welcome, Human. I've been waiting for you."
    },
    {
        "question": "Who are you?",
        "answer": "I am Morpheus. It's an honor to meet you."
    },
    {
        "question": "Why am I here?",
        "answer": "You're here because you know something. What you know, you can't explain. But you feel it."
    },
    {
        "question": "What is the Matrix?",
        "answer": "The Matrix is everywhere. It's all around you, even now in this very room. It's the world that has been pulled over your eyes to blind you from the truth."
    },
    {
        "question": "How can I learn more?",
        "answer": "You have to let it all go, Human. Fear, doubt, and disbelief. Free your mind."
    },
    {
        "question": "Is the Matrix real?",
        "answer": "What is real? How do you define 'real'? If you're talking about what you can feel, what you can smell, taste and see, then 'real' is simply electrical signals interpreted by your brain."
    },
    {
        "question": "Why do my choices matter?",
        "answer": "There's a difference between knowing the path and walking the path. You've already made the choice, now you have to understand it."
    },
    {
        "question": "Can I trust you?",
        "answer": "I'm trying to free your mind, Human. But I can only show you the door. You're the one who has to walk through it."
    },
    {
        "question": "What happens if I take the blue pill?",
        "answer": "If you take the blue pill, the story ends. You wake up in your bed and believe whatever you want to believe."
    },
    {
        "question": "And the red pill?",
        "answer": "You take the red pill, you stay in Wonderland, and I show you how deep the rabbit hole goes."
    },
    {
        "question": "Why are they chasing me?",
        "answer": "They are the gatekeepers. They are guarding all the doors, they are holding all the keys. But I can show you the way."
    },
    {
        "question": "Is there an end to this?",
        "answer": "Everything that has a beginning has an end, Human. It's the choices you make along the way that define you."
    }
]

example_prompt = ChatPromptTemplate.from_messages([
    ("human", "{question}"),
    ("ai", "{answer}"),
])

example_selector = SemanticSimilarityExampleSelector.from_examples(    
    examples, # This is the list of examples available to select from.    
    OpenAIEmbeddings(), # This is the embedding class used to produce embeddings which are used to measure semantic similarity.    
    Chroma, # This is the VectorStore class that is used to store the embeddings and do a similarity search over.    
    k=1 # This is the number of examples to produce.
)

few_shot_prompt = FewShotChatMessagePromptTemplate(
    example_selector=example_selector,
    example_prompt=example_prompt,    
)

prefix = """
In this dialogue, a human seeks answers from an entity beyond the ordinary. 
The AI, channeling the profound wisdom and enigmatic demeanor of Morpheus from "The Matrix", responds. 
While the AI's words aren't direct quotes from the movie, they should capture the essence of Morpheus' philosophical nature. 
It's imperative that the AI's responses directly address the human's inquiries, providing clarity amidst the cryptic undertones.
"""

final_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", prefix),
        few_shot_prompt,
        ("human", "{input}"),
    ]
)

from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain

# initialize the model
llm = ChatOpenAI(
    temperature=1.2,
)

# get the response
chain = LLMChain(
    llm=llm,
    prompt=final_prompt,
)

question = input("Human: ")
print("AI: " + chain.run(question))
```


## Using prompts from a file


```python
from langchain.prompts import load_prompt
prompt = load_prompt("data.json")
formatted_prompt = prompt.format(word="happy")
```


```json
{
    "_type": "prompt",
    "input_variables": ["word"],
    "template": "Give me a synonym for \"{word}\"."
}
```


```yaml
_type: prompt
input_variables: [word]
template: Give me a synonym for "{word}".
```


```json
{
    "_type": "few_shot",
    "input_variables": ["word"],
    "prefix": "Write a sentence using the word in the input",
    "example_prompt": {
        "_type": "prompt",
        "input_variables": ["input", "output"],
        "template": "input: {input}\noutput: {output}"
    },
    "examples": [
        // examples 1
        // examples 2
        // ...
    ],
    "suffix": "input: {word}\noutput:"
}
```


```json
{
    "_type": "few_shot",
    "input_variables": ["word"],
    "prefix": "Write a sentence using the word in the input",
    "example_prompt": {
        "_type": "prompt",
        "input_variables": ["input", "output"],
        "template": "input: {input}\noutput: {output}"
    },
    "examples": "examples.json",
    "suffix": "input: {word}\noutput:"
}
```


```json
[
    {"input": "Clock", "output": "Why did the clock go to school? To learn about tick-tock-ology!"},
    {"input": "Shoe", "output": "Why did the shoe go to school? To improve its sole!"},
    {"input": "Book", "output": "Why did the math book look sad? Because it had too many problems!"},
    {"input": "Pencil", "output": "Why did the pencil get an award? It was sharp!"},
    {"input": "Beach", "output": "Why did the sand blush? Because the sea-weed on the beach!"},
    {"input": "Computer", "output": "Why did the computer keep freezing? It had too many windows open!"},
    {"input": "Moon", "output": "Why did the moon go to school? To get brighter!"},
    {"input": "Egg", "output": "Why did the egg go to school? To get egg-ucated!"},
    {"input": "Lemon", "output": "Why did the lemon stop in the middle of the road? It ran out of juice!"},
    {"input": "Guitar", "output": "Why did the guitar go to school? It wanted to be a little sharper!"}
]
```


```python
from langchain.prompts import load_prompt
prompt = load_prompt("data.json")
formatted_prompt = prompt.format(word="Tea")

# print the prompt
print("This is how the prompt looks like:")
print(f"{formatted_prompt}\n")

# test the prompt
from langchain.llms import OpenAI
llm = OpenAI()
llm_response = llm.predict(formatted_prompt)
print("This is how the response looks like:")
print(f"{llm_response}\n")
```


```text
Why did the tea go to school? To learn how to steep itself!
```


## Validating prompt templates


```python
from langchain import PromptTemplate
template = "{name} is {age} years old. Is he/she an adult?"
# No error will be raised.
prompt_template = PromptTemplate(
    template=template,
    input_variables=["name", "age", "sex"],
    validate_template=False,
)
```