# Transfer Learning


## What is transfer learning?


## Inductive transfer


## Transductive transfer


## Inductive vs. transductive transfer


## Transfer learning, fine-tuning, and prompt engineering


## Fine-tuning with a prompt dataset: a practical example


```text
Q. What is SkillUp Academy?
A. SkillUp Academy is an online learning platform offering a variety of courses across multiple disciplines, enabling learners to gain knowledge and skills at their own pace from anywhere in the world.

Q. How do I sign up?
A. Just click on the "Sign Up" button on our homepage, provide your details, and get started!

Q. How can I pay for a course?
A. We accept payments through PayPal, and Stripe.

Q. I am not satisfied with the course. Can I get a refund?
A. Yes, we offer a 30-day money-back guarantee. If you're not satisfied within this period, you can request a refund. Please see our refund policy for details.

Q. Where can I find the terms of service for the website?
A. Our terms of service can be found at the footer of our website or by clicking here.

Q. Is my personal information secure?
A. Absolutely. We prioritize your privacy and have stringent measures in place to protect your data. Read more in our privacy policy here.

Q. Can I learn at my own pace?
A. Yes, our courses are designed to allow you to learn at your convenience. Once you enroll, you'll have access to the course materials for a specific period, during which you can learn at your own pace.

Q. I have questions about the course content. How can I get them answered?
A. Most courses have a discussion forum where you can ask questions, engage with fellow learners, and sometimes get responses from the course instructors.

Q. Can I access the courses on mobile?
A. Yes, our platform is mobile-friendly, and we also have a dedicated app available for both Android and iOS.

Q. I'm an instructor. How can I offer a course on your platform?
A. We're always looking for knowledgeable instructors. Just head to our 'Become an Instructor' page for details on how to collaborate with us.

Q. Do you offer certificates upon course completion?
A. Yes, once you successfully complete a course and pass any required assessments, you will receive a certificate of completion.

Q. What should I do if I face technical issues?
A. Please reach out to our support team via the 'Contact Us' page, and they'll be happy to assist you.
```


```json
{"prompt": "lorem ipsum dolor sit amet.END_PROMPT", "completion": " consectetur adipiscing elit.\n"}
{"prompt": "Vivamus vel lacus quis diam vestibulum scelerisque.END_PROMPT", "completion": " Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas.\n"}
...etc
```


```json
{"prompt": "What is SkillUp Academy->", "completion": " SkillUp Academy is an online learning platform offering a variety of courses across multiple disciplines, enabling learners to gain knowledge and skills at their own pace from anywhere in the world.\n"}
{"prompt": "How do I sign up->", "completion": " Just click on the \"Sign Up\" button on our homepage, provide your details, and get started!\n"}
{"prompt": "How can I pay for a course->", "completion": " We accept payments through PayPal, and Stripe.\n"}
{"prompt": "I am not satisfied with the course. Can I get a refund->", "completion": " Yes, we offer a 30-day money-back guarantee. If you're not satisfied within this period, you can request a refund. Please see our refund policy for details.\n"}
{"prompt": "Where can I find the terms of service for the website->", "completion": " Our terms of service can be found at the footer of our website or by clicking here.\n"}
{"prompt": "Is my personal information secure->", "completion": " Absolutely. We prioritize your privacy and have stringent measures in place to protect your data. Read more in our privacy policy here.\n"}
{"prompt": "Can I learn at my own pace->", "completion": " Yes, our courses are designed to allow you to learn at your convenience. Once you enroll, you'll have access to the course materials for a specific period, during which you can learn at your own pace.\n"}
{"prompt": "I have questions about the course content. How can I get them answered->", "completion": " Most courses have a discussion forum where you can ask questions, engage with fellow learners, and sometimes get responses from the course instructors.\n"}
{"prompt": "Can I access the courses on mobile->", "completion": " Yes, our platform is mobile-friendly, and we also have a dedicated app available for both Android and iOS.\n"}
{"prompt": "I'm an instructor. How can I offer a course on your platform->", "completion": " We're always looking for knowledgeable instructors. Just head to our 'Become an Instructor' page for details on how to collaborate with us.\n"}
{"prompt": "Do you offer certificates upon course completion->", "completion": " Yes, once you successfully complete a course and pass any required assessments, you will receive a certificate of completion.\n"}
{"prompt": "What should I do if I face technical issues->", "completion": " Please reach out to our support team via the 'Contact Us' page, and they'll be happy to assist you.\n"}
```


```bash
pip install openai[datalib]
```


```bash
export OPENAI_API_KEY=<your-api-key>
```


```bash
openai tools fine_tunes.prepare_data -f data.jsonl
```


```bash
Analyzing...

- Your file contains 12 prompt-completion pairs. In general, we recommend having at least a few hundred examples. We've found that performance tends to linearly increase for every doubling of the number of examples
- All prompts end with suffix `->`
- All completions end with suffix `\n`

No remediations found.

You can use your file for fine-tuning:
> openai api fine_tunes.create -t "data.jsonl"

After you’ve fine-tuned a model, remember that your prompt has to end with the indicator string `->` for the model to start generating completions, rather than continuing with the prompt. Make sure to include `stop=["\n"]` so that the generated texts ends at the expected place.
Once your model starts training, it'll approximately take 2.61 minutes to train a `curie` model, and less for `ada` and `babbage`. Queue will approximately take half an hour per job ahead of you.
```


```bash
openai api fine_tunes.create -t "data.jsonl" -m curie --suffix "SkillUpAcademy"
```


```bash
Upload progress: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2.47k/2.47k [00:00<00:00, 8.63Mit/s]
Uploaded file from data.jsonl: file-RdACszHknKQC49hdZdGxpoEA
Created fine-tune: ft-WEYpsygNbeHy8YvxLCaiyB4i
Streaming events until fine-tuning is complete...

(Ctrl-C will interrupt the stream, but not cancel the fine-tune)
[..] Created fine-tune: ft-WEYpsygNbeHy8YvxLCaiyB4i
[..] Fine-tune costs $0.01
[..] Fine-tune enqueued. Queue number: 0

...etc
```


```bash
openai api fine_tunes.follow -i ft-WEYpsygNbeHy8YvxLCaiyB4i
```


```bash
[..] Created fine-tune: ft-WEYpsygNbeHy8YvxLCaiyB4i
[..] Fine-tune costs $0.01
[..] Fine-tune enqueued. Queue number: 0
[..] Fine-tune started
[..] Completed epoch 1/4
[..] Completed epoch 2/4
[..] Completed epoch 3/4
[..] Completed epoch 4/4
[..] Uploaded model: curie:ft-org:skillupacademy-2023-08-18-14-32-18
[..] Uploaded result file: file-3fxL6gfapDyHqG7cjXDzFFOf
[..] Fine-tune succeeded

Job complete! Status: succeeded 🎉
Try out your fine-tuned model:

openai api completions.create -m curie:ft-faun:skillupacademy-2023-08-18-14-32-18 -p <YOUR_PROMPT>
```


```python
import openai
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

model = "curie:ft-faun:skillupacademy-2023-08-18-14-32-18"
prompt = input("Ask a question: ")
instruction = f"""
You are a helpful and smart customer service representative for SkillUp Academy. 
Answer the following questions based on the training data provided. 
If you don't know the answer, just say 'Sorry, I don't know the answer.'

AI: Hi there. I am Felix, the chatbot. How can I help you today?
User: {prompt}
"""

full_prompt = instruction + " " + prompt + "->"

response = openai.Completion.create(
    model=model,
    prompt=full_prompt,
    max_tokens=100,  # adjust based on your need
    stop=["\n"],
    temperature=0  # strikes a balance between deterministic and random
)

print(response.choices[0].text.strip())
```


## Why is prompt engineering vital for transfer learning and fine-tuning?