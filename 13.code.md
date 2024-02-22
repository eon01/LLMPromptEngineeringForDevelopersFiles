# General Knowledge Prompting


## What is general knowledge prompting?


## Example of general knowledge prompting


```text
Write a short article about the advantages of using a chatbot on a website.
```


```text
What are the different types of chatbots? AI chatbots, rule-based chatbots, live chat agents..etc
Why is it a good idea to use an AI chatbot on a website? 
What are the benefits of using a AI chatbot?
Describe briefly the technical implementation process of an AI chatbot using OpenAI's API.
```


```python
import os
import openai
openai.api_key = os.getenv("OPENAI_API_KEY")

print("=== Knowledge Generation 1 ===")
prompt1 = """
What are the different types of chatbots? AI chatbots, rule-based chatbots, live chat agents..etc
"""
response1 = openai.Completion.create(
  model="text-davinci-003",
  prompt=prompt1,
  temperature=1,
  max_tokens=500,
  top_p=1,
  frequency_penalty=0,
  presence_penalty=0
)
text1 = response1['choices'][0]['text']
print(text1)


print("=== Knowledge Generation 2 ===")
prompt2 = """
Why is it a good idea to use an AI chatbot on a website? 
"""
response2 = openai.Completion.create(
  model="text-davinci-003",
  prompt=prompt2,
  temperature=1,
  max_tokens=500,
  top_p=1,
  frequency_penalty=0,
  presence_penalty=0
)
text2 = response2['choices'][0]['text']
print(text2)

print("=== Knowledge Generation 3 ===")
prompt3 = """
What are the benefits of using a AI chatbot?
"""
response3 = openai.Completion.create(
    model="text-davinci-003",
    prompt=prompt3,
    temperature=1,
    max_tokens=500,
    top_p=1,
    frequency_penalty=0,
    presence_penalty=0
)
text3 = response3['choices'][0]['text']
print(text3)

print("=== Knowledge Generation 4 ===")
prompt4 = """
Describe briefly the technical implementation process of an AI chatbot using OpenAI's API.
"""
response4 = openai.Completion.create(
    model="text-davinci-003",
    prompt=prompt4,
    temperature=1,
    max_tokens=500,
    top_p=1,
    frequency_penalty=0,
    presence_penalty=0
)
text4 = response4['choices'][0]['text']
print(text4)

print("=== Generated knowledge prompting ===") 
final_prompt = f"""
The different types of chatbots are: {text1}.

It is a good idea to use an AI chatbot on a website because: {text2}.

The benefits of using an AI chatbot are: {text3}.

The technical implementation process of an AI chatbot using OpenAI's API is: {text4}.

Write a blog post titled "Why and how to use an AI chatbot on your website".
"""
response = openai.Completion.create(
    model="text-davinci-003",
    prompt=final_prompt,
    temperature=1,
    max_tokens=2000,
    top_p=1,
    frequency_penalty=0,
    presence_penalty=0
)
print(response['choices'][0]['text'])
```


```text
The current system is an online whiteboard system. Tech stack: typescript, react, redux, konvajs and react-konva. And vitest, react testing library for model, view model and related hooks, cypress component tests for view.

All codes should be written in the tech stack mentioned above. Requirements should be implemented as react components in the MVVM architecture pattern.

There are 2 types of view model in the system.

Shared view model. View model that represents states shared among local and remote users.

Local view model. View model that represents states only applicable to local user

Here are the common implementation strategy:

Shared view model is implemented as Redux store slice. Tested in vitest.

Local view model is implemented as React component props or states(by useState hook), unless for global local view model, which is also implemented as Redux store slice. Tested in vitest.

Hooks are used as the major view helpers to retrieve data from shared view model. For most the case, it will use ‘createSelector’ and ‘useSelector’ for memorization. Tested in vitest and react testing library.

Don’t dispatch action directly to change the states of shared view model, use an encapsulated view model interface instead. In the interface, each redux action is mapped to a method. Tested in vitest.

View is consist of konva shapes, and implemented as react component via react-konva. Tested in cypress component tests

Here are certain patterns should be followed when implement and test the component

When write test, use describe instead of test

Data-driven tests are preferred.

When test the view component, fake view model via the view model interface

Awareness Layer

Requirement:

Display other users’ awareness info(cursor, name and online information) on the whiteboard.

AC1: Don’t display local user

AC2: When remote user changes cursor location, display the change in animation.

Provide an overall solution following the guidance mentioned above. Hint, keep all awareness information in a Konva layer, and an awareness info component to render cursor, and name. Don’t generate code. Describe the solution, and breaking the solution down as a task list based on the guidance mentioned above. And we will refer this task list as our master plan.
```


```text
Task List:

Create a new component named "AwarenessInfo" to render cursor and name for remote users. This component will receive props such as user name, cursor position, and online status.

Create a new Konva layer named "AwarenessLayer" to hold all the "AwarenessInfo" components.

Add the "AwarenessLayer" to the main whiteboard component.

Create a new Redux slice named "awareness" to store the awareness information for remote users.

Create an encapsulated view model interface named "AwarenessViewModel" for the "awareness" Redux slice. This interface will contain a method named "updateUserAwarenessInfo" to update the awareness information for each remote user.

Implement the "AwarenessInfo" component to retrieve and display the awareness information for each remote user from the view model. The component will use the "useSelector" hook to retrieve the relevant information.

Implement the "updateUserAwarenessInfo" method in the "AwarenessViewModel" interface to dispatch a Redux action to update the awareness information for each remote user. This method will be called by the "MainWhiteboard" component.

When a remote user changes the cursor location, dispatch the "updateUserAwarenessInfo" method in the "AwarenessViewModel" interface to update the corresponding awareness information.

In the "AwarenessInfo" component, use the new location to animate the cursor change. This can be accomplished using the Konva "Tween" animation.

Write tests for the new "AwarenessInfo" component, including data-driven tests to ensure it properly displays the awareness information for all remote users. The tests will be written using the React Testing Library.

Write tests for the encapsulated view model interface to ensure it properly updates the awareness information for remote users. These tests will use the Vitest framework.

Write end-to-end Cypress tests to ensure the awareness layer is properly displayed and updated when remote users change their cursor location. The tests will simulate user interactions with the whiteboard and verify the state of the "AwarenessLayer" and "AwarenessInfo" components.
```


```text
Provide me example tests and implementation for the awareness slice and AwarenessViewModel. Test first.
```