# AI Python

> Notes from the course of [DeepLearning.AI - AI Python for Beginners](https://www.deeplearning.ai/short-courses/ai-python-for-beginners/)

- Check out my page on python if you need a refresher on python [here](../programming/python.md)

## Create an environment to use AI in Python script

![Coming soon](../.res/coming-soon.png)

## Building LLM prompts with variables

```python
from helper_functions import print_llm_response
print_llm_response("What is the capital of France?")
# Will print "The capital of France is Paris."
number_of_lines = 8
prompt = f"generate a poem with {number_of_lines} lines"
print_llm_response(prompt)
print(prompt)
# Will print a poem with 8 lines
# Would also work this way
number_of_lines = 8
prompt = f"generate a poem with {number_of_lines} lines"
response = get_llm_response(prompt)
print(response)
```

## Iteratively updating AI prompts using lists

```python
from helper_functions import print_llm_response, get_llm_response

# Translate the flavors in ice_cream_flavors to Spanish
ice_cream_flavors = ["Vanilla", "Strawberry"]

for flavor in ice_cream_flavors:
    prompt = f"""For the ice cream flavor listed below,
    Translate each flavor in spanish
    
    Flavor: {flavor}
    
    """
    ### --------------- ###
    print_llm_response(prompt)
# This prints
'''
____________________________________________________________________________________________________
Vanilla in Spanish is "Vainilla."
____________________________________________________________________________________________________


____________________________________________________________________________________________________
Strawberry in Spanish is "fresa."
____________________________________________________________________________________________________
'''
```