# Hugging face

- [Hugging face](https://huggingface.co/)

## What is NLP?

Natural Language Processing, or NLP, is a field of artificial intelligence (AI) that focuses on enabling computers to understand, interpret, and interact with human language. Essentially, it’s about teaching computers to "read," "write," and "understand" text or speech, just like humans do. 

NLP powers many everyday applications, like:

- **Chatbots** that understand and respond to questions
- **Voice assistants** like Siri or Alexa that recognize spoken words and carry out tasks
- **Spam filters** that identify unwanted emails based on their content
- **Translation apps** that convert text from one language to another
- **Sentiment analysis** that identifies emotions in text, like spotting a positive or negative review

> For example ChatGPT is an NLP Model

## What is hugging face?

Hugging Face is a company and an open-source community that specializes in developing and hosting machine learning models, particularly in natural language processing (NLP).  
They are known for their user-friendly tools and an ecosystem that makes machine learning accessible for developers, researchers, and businesses. Some key elements of Hugging Face include:

1. **Transformers Library**: This open-source library offers pre-trained models for tasks like text classification, translation, summarization, and question-answering. It supports popular frameworks like PyTorch, TensorFlow, and JAX, and simplifies working with models like BERT, GPT, T5, and more.

2. **Hub**: The Hugging Face Hub hosts thousands of models shared by the community and Hugging Face itself. It's a repository where anyone can find, upload, or deploy models and datasets for various machine-learning tasks. It also allows developers to build and fine-tune models easily.

3. **Datasets Library**: A library of diverse, high-quality datasets for machine learning. The library includes datasets from various domains and languages, helping developers train models on specific tasks without manually sourcing data.

4. **Spaces**: Hugging Face Spaces is a feature that enables users to create and share demos and applications of machine learning models with ease. It's popular for deploying models with Gradio or Streamlit, making it easy to showcase ML models interactively.

5. **Inference API**: Hugging Face provides APIs for deploying models in production environments. Users can host models on Hugging Face’s servers, which handle the backend infrastructure, allowing for fast integration of models into applications.

6. **Community and Research**: Hugging Face has become a central place for the ML community, supporting open science and democratizing AI by hosting workshops, challenges, and community events.


## Installation

- Full documentation [here](https://huggingface.co/docs/transformers/installation)

### Transformers

Transformers are a type of model architecture that has revolutionized how machines process and understand text.  
Developed by researchers at Google in 2017, the Transformer model introduced a new way for models to handle language data, and it became the foundation for many advanced language models, including GPT, BERT, T5, and others.

- How to install

```bash
mkdir transformers-env
cd transformers-env
python3 -m venv .
source ./bin/activate
pip install transformers
```
### Modal

- [Official documentation](https://modal-python.readthedocs.io/en/latest/)

"modAL is an active learning framework for Python3, designed with modularity, flexibility and extensibility in mind. Built on top of scikit-learn, it allows you to rapidly create active learning workflows with nearly complete freedom. What is more, you can easily replace parts with your custom built solutions, allowing you to design novel algorithms with ease."

```bash
pip install modal
python -m modal setup
```

### Pipeline

A pipeline is a tool that chains together a series of steps, simplifying complex workflows so that they run in a sequence.  
Think of it as an assembly line where data flows through multiple stages, each performing a specific task.  
In NLP, pipelines are commonly used to streamline tasks like text classification, translation, or summarization by automating the stages required to process and interpret the data.

## Resources

- [Hugging Face 101: Your First Steps Coding with Transformers - Harper Carroll AI](https://youtu.be/Mji39uWdhLU?si=3tgqwfabTMBuNoFH)
- [Getting Started With Hugging Face in 15 Minutes | Transformers, Pipeline, Tokenizer, Models - Assembly AI](https://youtu.be/QEaBAZQCtwE?si=KTq1rs53043Z4n1R)