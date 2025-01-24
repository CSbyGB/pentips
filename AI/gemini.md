# Gemini

> Notes of the course Pair Programming with a LLM from DeepLearning.AI. Check it out [here](https://learn.deeplearning.ai/courses/pair-programming-llm/lesson/1/introduction)

## Things needed

- An API key, you can get one [here](https://aistudio.google.com/app/apikey)
- Generative AI Libraries
- Basic python skills

```python
from utils import get_api_key
import os
import google.generativeai as genai
from google.api_core import client_options as client_options_lib

genai.configure(
    api_key=get_api_key(),
    transport="rest",
    client_options=client_options_lib.ClientOptions(
        api_endpoint=os.getenv("GOOGLE_API_BASE"),
    )
)
# Explore the available models
for m in genai.list_models():
    print(f"name: {m.name}")
    print(f"description: {m.description}")
    print(f"generation methods:{m.supported_generation_methods}\n")
models = [m for m in genai.list_models() 
          if 'generateText' 
          in m.supported_generation_methods]
models
# Set the model to connect to the Gemini API
model_flash = genai.GenerativeModel(model_name='gemini-1.5-flash')
# Helper with Gemini API
def generate_text(prompt,
                  model=model_flash,
                  temperature=0.0):
    return model_flash.generate_content(prompt,
                                  generation_config={'temperature':temperature})
# Ask the LLM how to write some code
prompt = "Show me how to iterate across a list in Python."
# Generate the text
completion = generate_text(prompt)
print(completion.text)
# Ask it to write code right away instead of giving code along with explainations
prompt = "write code to iterate across a list in Python"
completion = generate_text(prompt)
print(completion.text)
```

## Using a String Template

```python
from utils import get_api_key
import os
import google.generativeai as genai
from google.api_core import client_options as client_options_lib

genai.configure(
    api_key=get_api_key(),
    transport="rest",
    client_options=client_options_lib.ClientOptions(
        api_endpoint=os.getenv("GOOGLE_API_BASE"),
    )
)
# Pick the model that generates text
models = [m for m in genai.list_models() if 'generateText' in m.supported_generation_methods]
model_bison = models[0]
model_bison
# Set the model to connect to the Gemini API
model_flash = genai.GenerativeModel(model_name='gemini-1.5-flash')
# Helper function to call the Gemini API
def generate_text(prompt,
                  model=model_flash,
                  temperature=0.0):
    return model_flash.generate_content(prompt,
                                  generation_config={'temperature':temperature})
# Prompt template
prompt_template = """
{priming}

{question}

{decorator}

Your solution:
"""
priming_text = "You are an expert at writing clear, concise, Python code."
question = "create a doubly linked list"
# option 1
# decorator = "Work through it step by step, and show your work. One step per line."

# option 2
decorator = "Insert comments for each line of code."
prompt = prompt_template.format(priming=priming_text,
                                question=question,
                                decorator=decorator)
# Review the prompt
print(prompt)
# Call the api to get the completion
completion = generate_text(prompt)
# Gemini API
print(completion.text)
```

## Pair programming scenarios

```python
from utils import get_api_key

import os
import google.generativeai as genai
from google.api_core import client_options as client_options_lib

genai.configure(
    api_key=get_api_key(),
    transport="rest",
    client_options=client_options_lib.ClientOptions(
        api_endpoint=os.getenv("GOOGLE_API_BASE"),
    )
)
# Pick the model that generates text
models = [m for m in genai.list_models() if 'generateText' in m.supported_generation_methods]
model_bison = models[0]
model_bison
# Set the model to connect to the Gemini API
model_flash = genai.GenerativeModel(model_name='gemini-1.5-flash')
# helper function
def generate_text(prompt,
                  model=model_flash,
                  temperature=0.0):
    return model_flash.generate_content(prompt,
                                  generation_config={'temperature':temperature})
```

### Improve existing code

```python
prompt_template = """
I don't think this code is the best way to do it in Python, can you help me?

{question}

Please explain, in detail, what you did to improve it.
"""
question = """
def func_x(array)
  for i in range(len(array)):
    print(array[i])
"""
completion = generate_text(
    prompt = prompt_template.format(question=question)
)
# Gemini API
print(completion.text)
# Ask for multiple waus to rewrite the code
prompt_template = """
I don't think this code is the best way to do it in Python, can you help me?

{question}

Please explore multiple ways of solving the problem, and explain each.
"""
completion = generate_text(
    prompt = prompt_template.format(question=question)
)
print(completion.text)
# ask the model to recommend one of the methods
prompt_template = """
I don't think this code is the best way to do it in Python, can you help me?

{question}

Please explore multiple ways of solving the problem, 
and tell me which is the most Pythonic
"""
completion = generate_text(
    prompt = prompt_template.format(question=question)
)
print(completion.text)
```

### Simplify code

```python
# option 1
prompt_template = """
Can you please simplify this code for a linked list in Python?

{question}

Explain in detail what you did to modify it, and why.
"""
# option 2
prompt_template = """
Can you please simplify this code for a linked list in Python? \n
You are an expert in Pythonic code.

{question}

Please comment each line in detail, \n
and explain in detail what you did to modify it, and why.
"""
question = """
class Node:
  def __init__(self, dataval=None):
    self.dataval = dataval
    self.nextval = None

class SLinkedList:
  def __init__(self):
    self.headval = None

list1 = SLinkedList()
list1.headval = Node("Mon")
e2 = Node("Tue")
e3 = Node("Wed")
list1.headval.nextval = e2
e2.nextval = e3

"""
completion = generate_text(
    prompt = prompt_template.format(question=question)
)
print(completion.text)
```

## Write test cases

```python
prompt_template = """
Can you please create test cases in code for this Python code?

{question}

Explain in detail what these test cases are designed to achieve.
"""
# Note that the code I'm using here was output in the previous
# section. Your output code may be different.
question = """
class Node:
  def __init__(self, dataval=None):
    self.dataval = dataval
    self.nextval = None

class SLinkedList:
  def __init__(self):
    self.head = None

def create_linked_list(data):
  head = Node(data[0])
  for i in range(1, len(data)):
    node = Node(data[i])
    node.nextval = head
    head = node
  return head

list1 = create_linked_list(["Mon", "Tue", "Wed"])
"""
completion = generate_text(
    prompt = prompt_template.format(question=question)
)
print(completion.text)
```

## Make code more efficient

```python
prompt_template = """
Can you please make this code more efficient?

{question}

Explain in detail what you changed and why.
"""
question = """
# Returns index of x in arr if present, else -1
def binary_search(arr, low, high, x):
    # Check base case
    if high >= low:
        mid = (high + low) // 2
        if arr[mid] == x:
            return mid
        elif arr[mid] > x:
            return binary_search(arr, low, mid - 1, x)
        else:
            return binary_search(arr, mid + 1, high, x)
    else:
        return -1

# Test array
arr = [ 2, 3, 4, 10, 40 ]
x = 10

# Function call
result = binary_search(arr, 0, len(arr)-1, x)

if result != -1:
    print("Element is present at index", str(result))
else:
    print("Element is not present in array")

"""
completion = generate_text(
    prompt = prompt_template.format(question=question)
)
print(completion.text)
```

## Debug your code

```python
prompt_template = """
Can you please help me to debug this code?

{question}

Explain in detail what you found and why it was a bug.
"""

# There is a bug into this code! Let's see if the LLM can find it.
# The bug is in the
# print function. There's a circumstance where nodes can be null, and trying
# to print them would give a null error.
question = """
class Node:
   def __init__(self, data):
      self.data = data
      self.next = None
      self.prev = None

class doubly_linked_list:
   def __init__(self):
      self.head = None

# Adding data elements
   def push(self, NewVal):
      NewNode = Node(NewVal)
      NewNode.next = self.head
      if self.head is not None:
         self.head.prev = NewNode
      self.head = NewNode

# Print the Doubly Linked list in order
   def listprint(self, node):
       print(node.data),
       last = node
       node = node.next

dllist = doubly_linked_list()
dllist.push(12)
dllist.push(8)
dllist.push(62)
dllist.listprint(dllist.head)

"""
completion = generate_text(
    prompt = prompt_template.format(question=question),
    temperature = 0.7
)
print(completion.text)
# When the code is generated you cancheck the code.
```
