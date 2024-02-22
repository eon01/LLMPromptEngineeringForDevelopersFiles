# Introduction to Azure Prompt Flow


## What is Azure Prompt Flow?


## Prompt engineering agility


## Considerations before using Azure Prompt Flow


## Creating your fist prompt flow


```text
Banana
```


```python
from promptflow import tool
@tool
def my_python_tool(input: str) -> str:
  return input
```


```python
${inputs.food}
```


```js
[
  {
    "system_metrics": {
      "duration": 0.000577
    },
    "output": "Banana"
  }
]
```


```text
What are the macros of 100 grams of this food: {{ food }}
```


```js
[1 item
    0: {
        2 items "system_metrics": {
            1 item "duration": 0.002656
        }
        "output": "What are the macros of 100 grams of this food: Banana"
    }
]
```


```text
system: 
You are a nutrition specialist.
user: 
{{question}}
```


```js
[
  {
    "system_metrics": {
      "completion_tokens": 61,
      "duration": 6.065642,
      "prompt_tokens": 30,
      "total_tokens": 91
    },
    "output": "In 100 grams of banana, you will find:\n\n- Carbohydrates: Approximately 23g\n- Protein: Approximately 1.1g\n- Fat: Approximately 0.3g\n\nPlease note, specific nutritional content might vary slightly based on the size and ripeness of the banana."
  }
]
```


## Deploying the flow for real time inference


```python
import urllib.request
import json
import os
import ssl

def allowSelfSignedHttps(allowed):    
    if allowed and not os.environ.get('PYTHONHTTPSVERIFY', '') and getattr(ssl, '_create_unverified_context', None):
        ssl._create_default_https_context = ssl._create_unverified_context

allowSelfSignedHttps(True)
data = {
    "food": "<food goes here>",
}
body = str.encode(json.dumps(data))

url = '<endpoint url goes here>'
api_key = '<api key goes here>'

if not api_key:
    raise Exception("A key should be provided to invoke the endpoint")

headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key), 'azureml-model-deployment': 'blue' }

req = urllib.request.Request(url, body, headers)

try:
    response = urllib.request.urlopen(req)
    result = response.read()
    print(result)
except urllib.error.HTTPError as error:
    print("The request failed with status code: " + str(error.code))    
    print(error.info())
    print(error.read().decode("utf8", 'ignore'))
```