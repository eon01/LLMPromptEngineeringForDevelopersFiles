# Perplexity as a metric for prompt optimization


## Do not surprise the model


## How to calculate perplexity?


```python
import os
import openai
import math

# Set the model name.
model_name = "<MODEL_NAME>"

# Set the API key for OpenAI.
openai_api_key = os.environ["OPENAI_API_KEY"]
openai.api_key = openai_api_key

# Define the prompt you want to send to the model.
prompt = """
Once upon a time in a land far far away
"""

# Send the prompt to the OpenAI API and get the response.
response = openai.Completion.create(
    model=model_name,
    prompt=prompt,
    max_tokens=0,
    echo=True,
    logprobs=0
)

# Extract the log probabilities from the response.
token_logprobs = response["choices"][0]["logprobs"]["token_logprobs"]

# Replace any None values with -100. The first word in the prompt will be None. 
# The value -100 attributes a very low probability to the first word.
# By assigning a very low likelihood to the first word, we're saying we have no confidence in the model's choice of that word as the start of the sequence. 
nlls = [-100 if x is None else x for x in token_logprobs]
perplexity = math.exp(sum(nlls) / len(token_logprobs))

# Print the perplexity.
print("Perplexity:", perplexity)
```


## A practical example with Betterprompt


```bash
pip install betterprompt
```


```python
import betterprompt
import os
prompt = input("Enter your prompt: ")
perplexity = betterprompt.calculate_perplexity(prompt)
print("Perplexity: " + str(perplexity))
```


```text
Perplexity: 0.01080151264470053
Perplexity: 0.004663176137461259
```


```text
Yoga offers numerous benefits for mental health by nurturing a calm and balanced state of mind. One of its key advantages lies in its ability to soothe the nervous system, promoting deep relaxation and reducing stress levels. Through the practice of various breathing techniques and meditation, yoga helps individuals achieve a sense of inner peace and tranquility. Additionally, regular yoga practice enhances concentration and mental clarity, allowing individuals to better focus on their thoughts and emotions. By redirecting attention away from the external world and towards the present moment, yoga acts as a powerful tool for improving mental well-being and promoting overall mental health.
```


```text
Yoga, with its centuries-old practice, has emerged as a powerful tool for enhancing mental well-being. It exerts a calming influence on the mind, inviting a sense of peace and serenity. Through controlled breathing and sustained postures, yoga reduces stress levels, allowing individuals to better manage anxiety and overwhelm. Moreover, the practice promotes mindfulness and self-awareness, providing a much-needed respite from the noise and distractions of the modern world. By cultivating focus and clarity, yoga sharpens cognitive abilities and improves decision-making skills. Ultimately, yoga offers a holistic approach to mental wellness, fostering a sense of balance, tranquility, and improved overall mental health.
```


```text
Enter your prompt: Generate a 2-day itinerary for a cultural trip to Rome.
Perplexity: 8.967913713192179e-05

Enter your prompt: Craft a detailed 2-day cultural journey plan for visiting Rome's historic landmarks and museums.
Perplexity: 3.844865699961805e-05
```


```text
Certainly! Here's a suggested 2-day itinerary for a cultural trip to Rome:

Day 1:
Morning:
1. Start your day by visiting the Colosseum, one of Rome's most iconic landmarks. Take a guided tour or explore it at your own pace.
2. After the Colosseum, head over to the Roman Forum, an ancient city center filled with ruins and historical sites. Spend some time exploring the area and learning about its significance in Roman history.
3. Visit the nearby Palatine Hill, which offers panoramic views of the city and is home to impressive archaeological remains.

Afternoon:
4. Enjoy a leisurely lunch at a local trattoria near the historic center.
5. After lunch, visit the Pantheon, a former Roman temple and now a remarkable piece of architecture. Marvel at its magnificent dome and explore its interior.
6. Head to Piazza Navona, a vibrant square known for its beautiful baroque fountains. Take a stroll, admire the artworks, and perhaps grab a gelato from one of the nearby gelaterias.

Evening:
7. Spend the evening exploring the charming neighborhood of Trastevere. Wander through its narrow streets, visit its ancient churches, and indulge in a delicious dinner at one of its cozy trattorias.
8. Wrap up your day with a visit to the Trevi Fountain. Throw a coin over your shoulder into the fountain to ensure your return to Rome in the future.

Day 2:
Morning:
1. Begin your day by visiting Vatican City, the smallest independent state in the world. Explore St. Peter's Basilica, the largest church in Christendom, and admire Michelangelo's masterpiece, the Sistine Chapel, located in the Vatican Museums.
2. Take a walk through the Vatican Gardens, a tranquil oasis in the heart of the city.

Afternoon:
3. Enjoy lunch at one of the local restaurants near Vatican City.
4. Visit the Capitoline Museums, located on Capitoline Hill. These museums house an impressive collection of ancient Roman artifacts and artworks, including the famous statue of Romulus and Remus.
5. Take a stroll through the picturesque streets of the Monti district, known for its trendy boutiques, artisan shops, and charming cafes.

Evening:
6. As the sun sets, make your way to the Spanish Steps, a famous meeting place in Rome. Climb to the top for panoramic views and soak up the lively atmosphere.
7. Indulge in a traditional Italian dinner at a restaurant in the trendy neighborhood of Campo de' Fiori.
8. End your evening with a leisurely walk along the Tiber River, enjoying the beautiful views of Rome's illuminated bridges and monuments.

Please note that this itinerary can be customized based on your specific interests and preferences. Enjoy your cultural trip to Rome!
```


```text
Sure! Here's a detailed 2-day cultural journey plan for visiting Rome's historic landmarks and museums:

Day 1:
Morning:
1. Start your day by visiting the Colosseum, a magnificent amphitheater that is a symbol of ancient Rome. Explore the interior and imagine the gladiator battles that once took place there.

2. Next, head to the Roman Forum, located right next to the Colosseum. This was the social, political, and commercial center of ancient Rome. Take a guided tour or explore the area on your own to see the ancient ruins and learn about its rich history.

3. Afterward, visit the Palatine Hill, which offers stunning views of the city. This is where Romulus founded Rome and where many emperors built their palaces. Explore the archaeological site and the beautiful gardens.

Afternoon:
4. Take a short walk to the Capitoline Museums, located on Capitoline Hill. Here, you can admire a vast collection of sculptures, paintings, and ancient Roman artifacts. Don't miss the famous statue of Marcus Aurelius on horseback.

5. Enjoy a leisurely lunch in the charming neighborhood of Trastevere. This area is known for its narrow cobblestone streets, colorful buildings, and delicious local cuisine.

6. In the afternoon, visit the Pantheon, one of the best-preserved ancient Roman buildings. Marvel at its impressive dome and explore the interior, which houses the tombs of several notable figures, including Raphael.

Evening:
7. Spend the evening at Piazza Navona, a lively square surrounded by beautiful Baroque buildings. Admire the Fountain of the Four Rivers and the stunning architecture. Enjoy dinner at one of the many restaurants in the area and savor the vibrant atmosphere.

Day 2:
Morning:
1. Start your second day by visiting the Vatican City, home to some of the world's most renowned museums and St. Peter's Basilica. Begin with the Vatican Museums and immerse yourself in the vast collection of art, including the Sistine Chapel with its iconic ceiling painted by Michelangelo.

2. Continue to St. Peter's Basilica, the largest church in the world. Explore the stunning interior, marvel at Michelangelo's Pieta, and consider climbing to the top of the dome for a panoramic view of the city.

Afternoon:
3. After lunch, head to the Borghese Gallery, housed in the beautiful Villa Borghese. This museum features an impressive collection of sculptures and paintings by masters such as Bernini and Caravaggio. Reserve your tickets in advance as entry is timed.

4. Take a leisurely stroll through the charming neighborhood of Piazza di Spagna (Spanish Steps). Admire the famous Spanish Steps and explore the nearby high-end shops and boutiques.

Evening:
5. End your cultural journey with a visit to the Trevi Fountain. Toss a coin into the fountain for good luck before enjoying a traditional Italian dinner at one of the local trattorias in the area.

Remember to wear comfortable shoes, carry a map or use a navigation app, and allow for some flexibility in your itinerary in case you want to spend more time at any particular location. Enjoy your trip to Rome!
```


```text
While both responses are excellent and would serve a traveler very well, the second itinerary edges out slightly due to its richer descriptions, practical advice, and smoother transitions. It paints a slightly more vivid picture of what the experience might be like. However, personal preference will play a significant role; some might prefer the simplicity and directness of the first itinerary.
```


## Hack the prompt


```text
Write a blog post about aging gracefully.
```


```text
Write a list of prompts that I will use to write a blog post about aging gracefully.
```


```text
Paraphrase the following prompts:
1. What does aging gracefully mean to you personally?
2. Why is the topic of aging gracefully so important in today's society?
...
```