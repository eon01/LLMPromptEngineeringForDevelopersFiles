# A Practical Guide to Testing and Scoring Prompts


## What and how to evaluate a prompt


```text
Version 1: Summarize the main idea of the article in 1-5 sentences.
Version 2: If this article was very short (1-5 sentences), how would you summarize its main idea?
Version 3: Create a short summary of each paragraph in the article. Connect the summaries to create a summary of the entire article. Do not exceed 5 sentences.
...
```


```text
A = Enumerate 10 things humans do better than machines.
B = List 10 things humans do better than machines.
```


## Testing and scoring prompts with promptfoo


```bash
npm install -g promptfoo
promptfoo init
```


```yaml
Your first prompt goes here
---
Next prompt goes here. You can substitute variables like this: {{var1}} {{var2}} {{var3}}
---
This is the next prompt.

These prompts are nunjucks templates, so you can use logic like this:
{% if var1 %}
  {{ var1 }}
{% endif %}
---
[
  {"role": "system", "content": "This is another prompt. JSON is supported."},
  {"role": "user", "content": "Using this format, you may construct multi-shot OpenAI prompts"}
  {"role": "user", "content": "Variable substitution still works: {{ var3 }}"}
]
---
If you prefer, you can break prompts into multiple files (make sure to edit promptfooconfig.yaml accordingly)
```


```yaml
# This configuration runs each prompt through a series of example inputs and checks if they meet requirements.
prompts: [prompts.txt]
providers: [openai:gpt-3.5-turbo-0613]
tests:
  - description: First test case - automatic review
    vars:
      var1: first variable's value
      var2: another value
      var3: some other value
    assert:
      - type: equals
        value: expected LLM output goes here
      - type: contains
        value: some text
      - type: javascript
        value: 1 / (output.length + 1)  # prefer shorter outputs

  - description: Second test case - manual review
    # Test cases don't need assertions if you prefer to manually review the output
    vars:
      var1: new value
      var2: another value
      var3: third value

  - description: Third test case - other types of automatic review
    vars:
      var1: yet another value
      var2: and another
      var3: dear llm, please output your response in json format
    assert:
      - type: contains-json
      - type: similarity
        value: ensures that output is semantically similar to this text
      - type: llm-rubric
        value: ensure that output contains a reference to X                                                                                                                  
```


```bash
export OPENAI_API_KEY=<your-api-key>
```


```bash
npx promptfoo eval
```


```bash
npx promptfoo view
```


```bash
Server listening at http://localhost:15500
Do you want to open the browser to the URL? (y/N): 
Press Ctrl+C to stop the server
```


## promptfoo: using variables


```yaml
You are an {{ role }}. Write a short ad for a the following product: Online course for learning how to write, test and deploy prompts.
```


```yaml
prompts: [prompts.txt]
providers: [openai:gpt-3.5-turbo-0613]
tests:
  - vars:
      role: Ad copywriter
  - vars:
      role: Software developer
  - vars:
      role: Marketing manager
```


```bash
npx promptfoo eval
```


```bash
npx promptfoo view
```


## promptfoo: testing with assertions


```yaml
cat << EOF > prompts.txt
When I was 10 years old, my sister was half my age. Now I am 30 years old. How old is my sister?
---
user: When Harry was 4 years old, his sister was half of his age. Harry is now 50 years old. How old is his sister?
ai: When Harry was 4 years old, his sister was 4/2 years old. This means there are 2 years difference between them. Harry is now 50 years old. His sister is 50 - 2 = 48 years old.
user: When I was 10 years old, my sister was half my age. Now I am 30 years old. How old is my sister?
ai:
EOF
```


```yaml
cat << EOF > promptfooconfig.yaml
prompts: [prompts.txt]
providers: [openai:completion:text-davinci-002]
tests:
  - description: Test 1
    assert:
      - type: contains
        value: 25 years old
EOF
```


## promptfoo integration with LangChaing


```python
cat << EOF > lc.py
import sys
from langchain.prompts.chat import (
    ChatPromptTemplate,
    SystemMessagePromptTemplate,
    HumanMessagePromptTemplate,
)

# This is the template for the prompt
template = """You are a helpful assistant that translates from {from_lang} to {to_lang}.
Your output should be in JSON format.

Examples:
user: translate(Hello, en, es)
ai:{{
    "sentence": "Hello",
    "translation": "Hola",
    "from_lang": "en",
    "to_lang": "es"
}}

user: translate(Would you like to play a game?, en, es)
ai:{{
    "sentence": "Would you like to play a game?",
    "translation": "¿Te gustaría jugar un juego?",
    "from_lang": "en",
    "to_lang": "es"
}}

A user will pass in the sentence to translate, and your output should ONLY return the translation in the JSON format above, and nothing more.
"""

# System message prompt template. This is a message that is not sent to the user.
system_message_prompt = SystemMessagePromptTemplate.from_template(template)
# The text template that the user will use to send a message to the system.
human_template = "translate({sentence}, {from_lang}, {to_lang})"
# Human message prompt template.
human_message_prompt = HumanMessagePromptTemplate.from_template(human_template)
# Chat prompt template = system message prompt + human message prompt
chat_prompt = ChatPromptTemplate.from_messages([system_message_prompt, human_message_prompt])

# import the ChatOpenAI class and the LLMChain class
from langchain.chat_models import ChatOpenAI
from langchain.chains import LLMChain

# Create a new chain
chain = LLMChain(
    llm=ChatOpenAI(),
    prompt=chat_prompt,
)

# Read and parse the user input
def read_parse_user_input(input):
    # parse the input
    input = input.replace("translate(", "")
    input = input.replace(")", "")
    input = input.split(",")
    sentence = input[0].strip()
    from_lang = input[1].strip()
    to_lang = input[2].strip()
    return sentence, from_lang, to_lang

# Get the user input
user_input = user_input = sys.argv[1]

sentence, from_lang, to_lang = read_parse_user_input(user_input)

# Run the chain and print the output
output = chain.run(sentence=sentence, from_lang=from_lang, to_lang=to_lang)
print(output)
EOF
```


```bash
python lc.py
```


```text
translate(Hello there, en, tr)
```


```text
translate(What is the weather like today?, en, lt)
```


```yaml
cat << EOF > promptfooconfig.yaml
prompts: prompts.txt
providers: exec:python lc.py
tests:
    - vars:
        fn: translate(Hi, en, dk)
      assert:
        - type: is-json
        - type: contains
          value: "\"translation\""
        - type: contains
          value: "\"from_lang\""
        - type: contains
          value: "\"to_lang\""
        - type: contains
          value: "\"sentence\""    
EOF
```


```yaml
cat << EOF > prompts.txt
{{ fn }}
EOF
```


## promptfoo and reusing assertions with templates (DRY)


```yaml
prompts: prompts.txt
providers: exec:python lc.py
tests:
    - vars:
        fn: translate(Hi, en, dk)
      assert:
        - type: is-json
        - type: contains
          value: "\"translation\""
        - type: contains
          value: "\"from_lang\""
        - type: contains
          value: "\"to_lang\""
        - type: contains
          value: "\"sentence\""    
    - vars:
        fn: translate(Hi, en, fr)
      assert:
        - type: is-json
        - type: contains
          value: "\"translation\""
        - type: contains
          value: "\"from_lang\""
        - type: contains
          value: "\"to_lang\""
        - type: contains
          value: "\"sentence\""  
    - vars:
        fn: translate(Hi, en, ar)
      assert:
        - type: is-json
        - type: contains
          value: "\"translation\""
        - type: contains
          value: "\"from_lang\""
        - type: contains
          value: "\"to_lang\""
        - type: contains
          value: "\"sentence\""                      
...etc
```


```yaml
cat << EOF > promptfooconfig.yaml
assertionTemplates: &default_assertions
  - type: is-json
  - type: contains
    value: "\"translation\""
  - type: contains
    value: "\"from_lang\""
  - type: contains
    value: "\"to_lang\""
  - type: contains
    value: "\"sentence\""

prompts: prompts.txt
providers: exec:python lc.py
tests:
    - vars:
        fn: translate(Hi, en, dk)
      assert: *default_assertions
    
    - vars:
        fn: translate(Hi, en, ar)
      assert: *default_assertions
          
    - vars:
        fn: translate(Hi, en, es)
      assert: *default_assertions
          
    - vars:
        fn: translate(Hi, en, fr)
      assert: *default_assertions
          
    - vars:
        fn: translate(Hi, en, de)
      assert: *default_assertions
          
    - vars:
        fn: translate(Hi, en, it)
      assert: *default_assertions
EOF
```


```yaml
assertionTemplates:
  isJson: 
    type: is-json
  containsTranslation:
    type: contains
    value: "\"translation\""
  containsFromLang:
    type: contains
    value: "\"from_lang\""
  containsToLang:
    type: contains
    value: "\"to_lang\""
  containsSentence:
    type: contains
    value: "\"sentence\""

prompts: prompts.txt
providers: exec:python lc.py
tests:
  - vars:
      fn: translate(Hi, en, dk)
    assert:
      - $ref: "#/assertionTemplates/isJson"
      - $ref: "#/assertionTemplates/containsTranslation"
      - $ref: "#/assertionTemplates/containsFromLang"
      - $ref: "#/assertionTemplates/containsToLang"
      - $ref: "#/assertionTemplates/containsSentence"

  - vars:
      fn: translate(Hi, en, ar)
    assert:
      - $ref: "#/assertionTemplates/isJson"
      - $ref: "#/assertionTemplates/containsTranslation"
      - $ref: "#/assertionTemplates/containsFromLang"
      - $ref: "#/assertionTemplates/containsToLang"
      - $ref: "#/assertionTemplates/containsSentence"

  # ... repeat the same structure for other languages like es, fr, de, and it.
```


## promptfoo scenarios and streamlining the test


```yaml
cat << EOF > prompts.txt
[
  {"role": "system", "content": "Provide a weather-related recommendation based on the user's input. context: the user is going for a hike"},
  {"role": "user", "content": "Sunny"},
  {"role": "assistant", "content": "It's a great day for outdoor activities! Temperature: 25°C, Humidity: 40%"}
]
---
[
  {"role": "system", "content": "Provide a weather-related recommendation based on the user's input. context: the user is going for a hike"},
  {"role": "user", "content": "Rainy"},
  {"role": "assistant", "content": "Don't forget your umbrella! Temperature: 18°C, Humidity: 90%"}
]
---
[
  {"role": "system", "content": "Provide a weather-related recommendation based on the user's input. context: the user is going for a hike"},
  {"role": "user", "content": "Cloudy"},
  {"role": "assistant", "content": "You might need a jacket today. Temperature: 20°C, Humidity: 60%"}
]
EOF
```


```yaml
cat << EOF > promptfooconfig.yaml
prompts: [prompts.txt]
providers: [openai:gpt-3.5-turbo-0613]
scenarios:
  - config:
      - vars:
          weatherCondition: Sunny
          expectedAdvice: "If you are going for a hike, bring sunscreen."
          expectedTemperature: "25°C"
          expectedHumidity: "40%"
      - vars:
          weatherCondition: Rainy
          expectedAdvice: "If you are going for a hike, bring an umbrella."
          expectedTemperature: "18°C"
          expectedHumidity: "90%"
      - vars:
          weatherCondition: Cloudy
          expectedAdvice: "If you are going for a hike, bring a jacket."
          expectedTemperature: "20°C"
          expectedHumidity: "60%"
    tests:
      - description: Forecast Advice based on Weather Condition
        vars:
          input: '{{weatherCondition}}'
        assert:
          - type: similar
            value: '{{expectedAdvice}}'
            threshold: 0.8
      - description: Forecast Temperature based on Weather Condition
        vars:
          input: '{{weatherCondition}}'
        assert:
          - type: similar
            value: '{{expectedTemperature}}'
            threshold: 0.8
      - description: Forecast Humidity based on Weather Condition
        vars:
          input: '{{weatherCondition}}'
        assert:
          - type: similar
            value: '{{expectedHumidity}}'
            threshold: 0.8
EOF
```


```bash
npx promptfoo eval
```


```bash
npx promptfoo view
```