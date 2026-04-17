# 04 — معالجة اللغات الطبيعية (NLP)

---

## المسار التطوري

```
Bag of Words → TF-IDF → Word2Vec → LSTM → BERT → GPT → LLMs
```

---

## الأساسيات

```python
import spacy
nlp = spacy.load("en_core_web_sm")

doc = nlp("Apple is building a data center in Iowa.")

# Tokenization
tokens = [token.text for token in doc]

# Named Entity Recognition
for ent in doc.ents:
    print(ent.text, ent.label_)  # Apple ORG, Iowa GPE

# POS Tagging
for token in doc:
    print(token.text, token.pos_)
```

---

## Transformers (HuggingFace)

```python
from transformers import pipeline

# Sentiment Analysis
classifier = pipeline("sentiment-analysis")
result = classifier("This movie is fantastic!")
# [{'label': 'POSITIVE', 'score': 0.9998}]

# Text Generation
generator = pipeline("text-generation", model="gpt2")
generator("Once upon a time", max_length=50)

# Question Answering
qa = pipeline("question-answering")
qa(question="What is the capital?", context="Paris is the capital of France.")

# Fine-tuning BERT
from transformers import AutoModelForSequenceClassification, Trainer
model = AutoModelForSequenceClassification.from_pretrained("bert-base-uncased", num_labels=2)
trainer = Trainer(model=model, train_dataset=dataset)
trainer.train()
```

---

## LLMs & Prompt Engineering

```python
from openai import OpenAI
client = OpenAI()

# Few-shot prompting
response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Translate to Arabic: Hello world"}
    ]
)

# RAG (Retrieval Augmented Generation)
# 1. Embed documents → Store in vector DB (Pinecone, Chroma)
# 2. Embed query → Find similar docs
# 3. Inject docs in prompt → Generate answer
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [HuggingFace Course](https://huggingface.co/learn/nlp-course) ← مجاني
- [CS224N - Stanford NLP](http://web.stanford.edu/class/cs224n/)
- [LangChain Docs](https://docs.langchain.com/) ← لبناء LLM apps
