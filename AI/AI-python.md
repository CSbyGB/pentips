# AI Python

> Notes from the course of [DeepLearning.AI - AI Python for Beginners](https://www.deeplearning.ai/short-courses/ai-python-for-beginners/)

> Mostly these are examples of practical use but you can have an overview to know how you could adapt them to you context.

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

## Using dictionaries to complete high priority tasks using AI

```python
from helper_functions import print_llm_response, get_llm_response
#task example, large list not ordered by priority. Want to prioritize
list_of_tasks = [
    "Compose a brief email to my boss explaining that I will be late for tomorrow's meeting.",
    "Write a birthday poem for Otto, celebrating his 28th birthday.",
    "Write a 300-word review of the movie 'The Arrival'.",
    "Draft a thank-you note for my neighbor Dapinder who helped water my plants while I was on vacation.",
    "Create an outline for a presentation on the benefits of remote work."
]
#instead of that unorganized large list, divide tasks by priority
high_priority_tasks = [
    "Compose a brief email to my boss explaining that I will be late for tomorrow's meeting.",
    "Create an outline for a presentation on the benefits of remote work."
]

medium_priority_tasks = [
    "Write a birthday poem for Otto, celebrating his 28th birthday.",
    "Draft a thank-you note for my neighbor Dapinder who helped water my plants while I was on vacation."
]

low_priority_tasks = [
    "Write a 300-word review of the movie 'The Arrival'."
]
#create dictionary with all tasks
#dictionaries can contain lists!
prioritized_tasks = {
    "high_priority": high_priority_tasks,
    "medium_priority": medium_priority_tasks,
    "low_priority": low_priority_tasks
}
#complete high priority tasks 
for task in prioritized_tasks["high_priority"]:
    print_llm_response(task)
```

## Customizing recipes with lists, dictionaries and AI

```python
# Update the following dictionary 
# with your own preferences 

from helper_functions import print_llm_response, get_llm_response

my_food_preferences = {
        "dietary_restrictions": ["no spicy food"], #List with dietary restrictions
        "favorite_ingredients": ["carrots", "olives", "tomatoes", "chicken", "fish"], #List with top three favorite ingredients
        "experience_level": "intermediate", #Experience level as a chef
        "maximum_spice_level": 0 #Spice level in a scale from 1 to 10
}

print(my_food_preferences)

prompt = f"""Please suggest a recipe that tries to include 
the following ingredients: 
{my_food_preferences["favorite_ingredients"]}.
The recipe should adhere to the following dietary restrictions:
{my_food_preferences["dietary_restrictions"]}.
The difficulty of the recipe should be: 
{my_food_preferences["experience_level"]}
The maximum spice level on a scale of 10 should be: 
{my_food_preferences["maximum_spice_level"]} 
Provide a detailed description of the recipe.
"""

print_llm_response(prompt)

```

## Helping AI make decisions

```python
from helper_functions import print_llm_response
# Go through the list of tasks if a task from the list takes more than 15 minutes we can do it
task_list = [
    {
        "description": "Compose a brief email to my boss explaining that I will be late for next week's meeting.",
        "time_to_complete": 3
    },
    {
        "description": "Create an outline for a presentation on the benefits of remote work.",
        "time_to_complete": 60
    },
    {
        "description": "Write a 300-word review of the movie 'The Arrival'.",
        "time_to_complete": 30
    },
    {
        "description": "Create a shopping list for tofu and olive stir fry.",
        "time_to_complete": 5
    }
]

for task in task_list:
    if task["time_to_complete"] >= 15:
        task_to_do = task["description"]
        print_llm_response(task_to_do)
```

## Using files in python and AI

```python
from helper_functions import get_llm_response
from IPython.display import display, Markdown

# We open a file with an email in it
f = open("email.txt", "r")
email = f.read()
f.close()

# We ask the AI to extract the bullet points from the email
prompt = f"""Extract bullet points from the following email. 
Include the sender information. 

Email:
{email}"""

print(prompt)
bullet_points = get_llm_response(prompt)
print(bullet_points)

# Print in Markdown format
display(Markdown(bullet_points))

# Here is a prompt to identify all of the countries mentioned 
# in the email
prompt = f"""Make a list of all the countries mentioned in the email

Email:
{email}
"""

countries = get_llm_response(prompt)
print(countries)

# Other example get a list of activities the person mentioned in the email
prompt = f"""Make a list of all the activies that Daniel did and mentioned in the email

Email:
{email}
"""

activities = get_llm_response(prompt)
print(activities)
```

## Loading and using your own data

```python
from helper_functions import upload_txt_file, list_files_in_directory, print_llm_response

# list out the files in the current working directory:
list_files_in_directory()


```
