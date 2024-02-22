# Where and How Prompt Engineering is Used


## Creative writing


## Content generation, SEO, marketing and advertising


## Customer Service


```python
import nltk
import re
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize

nltk.download('punkt')
nltk.download('stopwords')

def text_normalization(text):
    # Convert text to lowercase
    text = text.lower()

    # Remove punctuation using regular expressions
    text = re.sub(r'[^\w\s]', '', text)

    # Tokenize the text into words
    words = word_tokenize(text)

    # Remove stopwords
    stop_words = set(stopwords.words('english'))
    words = [word for word in words if word not in stop_words]

    # Lemmatization using NLTK's WordNetLemmatizer
    lemmatizer = nltk.WordNetLemmatizer()
    words = [lemmatizer.lemmatize(word) for word in words]

    # Join the words back into a normalized sentence
    normalized_text = ' '.join(words)

    return normalized_text

# Test the function
input_text = "This is a sample sentence, showing off the stop words filtration."
normalized_text = text_normalization(input_text)
print(normalized_text)
```


```text
sample sentence showing stop word filtration
```


```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.svm import SVC
from sklearn.pipeline import make_pipeline

# Sample data for training
password_queries = [
    "How to reset my password?",
    "I forgot my password.",
    "What is my password?",
    "My password is not working.",
]

non_password_queries = [
    "How to create a new account?",
    "New user registration.",
    "How to delete my account?",
    "Close my account.",
    "I want to open a new account.",    
]

# Create labels for the data
password_labels = ["password inquiry"] * len(password_queries)
non_password_labels = ["non-password inquiry"] * len(non_password_queries)

# Combine the data and labels
data = password_queries + non_password_queries
labels = password_labels + non_password_labels

# Create the classifier pipeline
classifier = make_pipeline(TfidfVectorizer(), SVC(kernel='linear'))

# Train the classifier
classifier.fit(data, labels)

# Test the classifier with new queries
new_queries = [
    "I lost my password, please help.",
    "Can I change my password?",
    "I want to open two accounts. Is it possible?",
    "I want to recover my account.",
]

# Predict the intent for new queries
for query in new_queries:
    prediction = classifier.predict([query])
    print(f"Query: '{query}' => Prediction: {prediction[0]}")
```


```text
Query: 'I lost my password, please help.' => Prediction: password inquiry
Query: 'Can I change my password?' => Prediction: password inquiry
Query: 'I want to open two accounts. Is it possible?' => Prediction: non-password inquiry
Query: 'I want to recover my account.' => Prediction: non-password inquiry
```


## Data analysis, reporting, and visualization


## Virtual assistants and smart devices


## Game development


## Healthcare and medical


## Story generation and role-playing


## Business intelligence and analytics


## Image generation