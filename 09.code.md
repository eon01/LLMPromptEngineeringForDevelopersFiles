# Self-consistency


```text
If you have 5 apples and eat 2, how many are left?
```


```text
You start with 5 apples. You eat 2 of them. So, 5 minus 2 equals 3 apples. The answer is 3 apples left.
```


```text
You had 5 apples and after eating 2, you have 3 left
```


```text
Start with 5 apples, and if 2 are gone when eaten, that leaves 3 apples
```


```text
If there were 5 apples and 2 are no longer there because you ate them, then 5 minus 2 equals 3 apples remaining.
```


```text
If there are 3 cars in the parking lot and 2 more cars arrive, 1 car leaves, 15 cars arrives and 8 leaves, how many cars are in the parking?"
```


```python
import openai
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

prompt = """
Q: If there are 3 cars in the parking lot and 2 more cars arrive, 1 car leaves, 15 cars arrives and 8 leaves, how many cars are in the parking?"
A:
"""
# the answer should be: 3 + 2 - 1 + 15 - 8 = 11

response = openai.Completion.create(
  engine="text-davinci-002",
  prompt=prompt,
  temperature=0.5,
  max_tokens=260
)
print(response.choices[0].text.strip())
```


```text
There are 18 cars in the parking lot.
```


```python
import openai
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

# Few shot learning to improve the accuracy of the model
prompt = """
Q: There are 15 trees in the grove. Grove workers will plant trees in the grove today. After they are done,
there will be 21 trees. How many trees did the grove workers plant today?
A: We start with 15 trees. Later we have 21 trees. The difference must be the number of trees they planted.
So, they must have planted 21 - 15 = 6 trees. The answer is 6.
Q: If there are 3 cars in the parking lot and 2 more cars arrive, how many cars are in the parking lot?
A: There are 3 cars in the parking lot already. 2 more arrive. Now there are 3 + 2 = 5 cars. The answer is 5.
Q: Leah had 32 chocolates and her sister had 42. If they ate 35, how many pieces do they have left in total?
A: Leah had 32 chocolates and Leah’s sister had 42. That means there were originally 32 + 42 = 74
chocolates. 35 have been eaten. So in total they still have 74 - 35 = 39 chocolates. The answer is 39.
Q: Jason had 20 lollipops. He gave Denny some lollipops. Now Jason has 12 lollipops. How many lollipops
did Jason give to Denny?
A: Jason had 20 lollipops. Since he only has 12 now, he must have given the rest to Denny. The number of
lollipops he has given to Denny must have been 20 - 12 = 8 lollipops. The answer is 8.
Q: Shawn has five toys. For Christmas, he got two toys each from his mom and dad. How many toys does
he have now?
A: He has 5 toys. He got 2 from mom, so after that he has 5 + 2 = 7 toys. Then he got 2 more from dad, so
in total he has 7 + 2 = 9 toys. The answer is 9.
Q: There were nine computers in the server room. Five more computers were installed each day, from
monday to thursday. How many computers are now in the server room?
A: There are 4 days from monday to thursday. 5 computers were added each day. That means in total 4 * 5 =
20 computers were added. There were 9 computers in the beginning, so now there are 9 + 20 = 29 computers.
The answer is 29.
Q: Michael had 58 golf balls. On tuesday, he lost 23 golf balls. On wednesday, he lost 2 more. How many
golf balls did he have at the end of wednesday?
A: Michael initially had 58 balls. He lost 23 on Tuesday, so after that he has 58 - 23 = 35 balls. On
Wednesday he lost 2 more so now he has 35 - 2 = 33 balls. The answer is 33.
Q: Olivia has $23. She bought five bagels for $3 each. How much money does she have left?
A: She bought 5 bagels for $3 each. This means she spent 5
Q: If there are 3 cars in the parking lot and 2 more cars arrive, 1 car leaves, 15 cars arrives and 8 leaves, how many cars are in the parking?"
A:
"""
# the answer should be: 3 + 2 - 1 + 15 - 8 = 11

response = openai.Completion.create(
  engine="text-davinci-002",
  prompt=prompt,
  temperature=0.5,
  max_tokens=260
)
print(response.choices[0].text.strip())
```


```text
There are 3 cars in the parking lot already. 2 more arrive. Now there are 3 + 2 = 5 cars. 1 car leaves. So now there are 5 - 1 = 4 cars. 15 more cars arrive. So now there are 4 + 15 = 19 cars. 8 cars leave. So now there are 19 - 8 = 11 cars. The answer is 11.
```