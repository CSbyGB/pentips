# Python programming

- It is really useful for a pentester to know about python and be able to script something. Being able to automate things can definitely help us go quicker in our daily practice.
Of course we do not need to be a developer but it is very helpful to be able to read code.

## shebang

- For python our script need to have this shebang in the first line `#!/bin/python3` this will allow us to launch the script using `./ myscript.py` instead of `python3 myscript.py`

## Strings

### Print a string

```python
#!/bin/python3
print("Hello world!") # double quotes
print('Hello world!') # single quotes
print("""This string runs
multiple lines""") # triple quotes for multiple lines
print("This string is "+"awesome") # concatenation
```

## Math

```python
#!/bin/python3
print(50 + 50) # will add and print the result
print(50 - 50) # will substract and print the result
print(50 + 50) # will multiply and print the result
print(50 / 50) # will divide and print the result
print(50 + 50 - 50 * 50 / 50) # will calculate the equation and print the result
print(50 ** 2) # exponents
print(50 % 6) # modulo
print(50 // 6) # no leftovers
```

## Variables and Methods

```python
#!/bin/python3
mystring = "this is a string stored in a variable called mystring"
print(mystring) # will print the content of the var
print(mystring.upper()) # the upper() method will put the text in all caps
print(mystring.lower()) # the lower() method will put the text in all lower
print(mystring.title()) # the title() method will capitalize the first letter of each word
print(len(mystring)) # the len method will print the number of chars in the var mystring
name = "name" # string
age = 7 # int
size = 2.4 # float
print(int(30.9)) # will cast a float in an int and not round so the it will print 30
age += 1 # will add 1 to the initial ar so the valur will become 8
```

## Functions

```python
#!/bin/python3
def who_am_i():
  name = "name"
  age =7
  print("my name is "+ name + "I am "+ str(age) +" years old")
  
who_am_i() #will call the function 

# function with a parameter
def add_one_hundred(num):
  print(num + 100)

add_one_hundred(100) # will call the function and use the 100 as the parameter

# Function with multiple parameters
def add(x,y):
  print(x + y)

add(7,7) # will call the function so it will add 7+7

def multiply(x,y):
  return x * y

print(multiply(7,7)) # will print 49

def square_root(x):
  print(x** .5)

square_root(64) # will print 8.0

def nl():
  print('\n')

nl() # will save us time because we can just call the function instead of writing the print statement everytime we want a new line
```

## Boolean Expressions

```python
#!/bin/python3

bool1 = True
bool2 = 3*3 == 9
bool3 = False
bool4 = 3*3 != 9
print(bool1,bool2,bool3,bool4) # will print True True False False
print(type(bool1)) # will print <class 'bool'>
```

## Relational and Boolean operators

```python
#!/bin/python3

greater_than = 7 > 5 # True
less_than = 5 < 7 # True
greater_than_equal_to = 7 >= 7 # True
less_than_equal_to = 7 <= 7 # True

test_and = (7 > 5) and (5 < 7) # True
test_and2 = (7 > 5) and (5 > 7) # False
test_or = (7 > 5) or (5 < 7) # True
test_or2 = (7 > 5) or (5 > 7) # True
test_not = not True # False
```

## Conditional Statements

```python
#!/bin/python3

def drink(money):
  if money >= 2:
    return "You got a drink"
  else:
    return "no drink for you"

print(drink(3))
print(drink(1))

def alcohol(age, money):
  if (age >= 21) and (money >= 5):
    return "We're getting a drink"
  elif (age >= 21) and (money < 5):
    return "Come back with more money"
  elif (age < 21) and (money >= 5):
    return "Nice try kid!"
  else:
    return "You're too poor and too young"

print(alcohol(21,5))
print(alcohol(21,4))
print(alcohol(20,4))
```    

## Lists (mutable)

```python
#!/bin/python3

movies=["When Harry met Sally","movie2","movie 3","movi 4"] # this is a list
print(movies[0]) # return first item
print(movies[1:4]) # will print from 2nd item to 3rd
print(movies[1:]) # will print from 2nd item to last item
print(movies[:2]) # will print from begining of the list until the 1st item (so here only the first item)
print(movies[-1]) # will print the very last item of the list
movies.append("another movie") # add an item to the end of the list
movies.pop() # will remove the last item of the list
movies.pop(0) # will remove the first item of the list
print(len(movies) # print length of list
```

## Tuples (immutable)

- It is immutable meaning we can not use pop or append on them once it is declared it can not be modified.

```python
#!/bin/python3

grades=(""a,"b","c","d","e","f")
print(grades[1]) # will print b
```

## Looping

### For loops

- Start to finish of an iterate

```python
#!/bin/python3

vegetables=["cucumber", "spinach", "cabbage"]
for x in vegetables:
  print(x)
```

### While loops

- Execute as long as true

```python
#!/bin/python3

i=1

while 1<10:
  print(i)
  i++
```

## Importing modules

```python
#!/bin/python3
import sys # systems functions and parameters
from datetime import datetime as dt # import with alias + plus using from makes it to import only part of the module

print(sys.version)
print(dt.now())
```

## Advanced strings

```python
#!/bin/python3

my_name="myname"
print(my_name[0])
print(my_name[-1])

sentence = "This is a sentence"
print(sentence.split()) # will split using space as a delimiter so it will take each word separately
sentence_split = sentence.split()
sentence_join = ' '.join(sentence_split)
quote = "he said 'give me all your money'" # we can use single quotes in double quotes to keep the double quote from being escaped (would also work the other way around)
quote = "he said \"give me all your money\"" # we can also escape characters with \
too_much_space = "               hello                  "
print(too_much_space.strip())
print("A" in "Apple") # will print True
print("A" in "apple") # will print False
letter = "A"
word = "Apple"
print(letter.lower() in word.lower()) # improved
movie = "movie"
print("my favorite movie is {}.".format(movie))
```

## Dictionaries

- Key value pairs

```python
#!/bin/python3

drinks = {"Drink1": 7, "Drink2": 10, "Drink3": 8} # drink is the key, price is the value
print(drinks)

employees = { "Finance": ["Bob", "Linda", "Tina"], "IT": ["Gene", "Louise", "Nicole"], "HR": ["Jimmy", "Marty"]
print(employees)

employees['Legal'] = ["Mr. Frond"] # Add new key:value pair
print(employees)

employees.update({"Sales": ["Adie", "Ollie"]}) # Add new key:value pair
print(employees)

drinks["Drink1"] = 8
print(drinks)
print(drinks.get("drink1"))
```

## Sockets

- We use socket to connect to an open port and an IP addess
```python
#!/bin/python3
import socket

HOST='127.0.0.1'
PORT='7777'

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) # AF_INET is IPV4 SOCK_STREAM is the port
s.connect((HOST,PORT))
```

- To test our script we can launch netcat
- `nc -nlvp 7777`

### Building a port scanner

- We can use socket to build a port scanner

```python
#!/bin/python3
import sys
import socket
from datetime import datetime

# usage: python3 scanner.py <IP-ADR>

# Define our target
if len(sys.argv) == 2:
  target = socket.gethostbyname(sys.argv[1]) # Translate host name to IPV4
else:
  print("Invalid amount of arguments")
  print("Syntax python3 scanner.py <IP-ADR>")

# Add a banner
print("-" * 50)
print("Scanning target "+target)
print("Time started: "+str(datetime.now()))
print("-" * 50)

try:
  for port in range(1,65535):
  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  socket.setdefaultitmeout(1)
  result = s.connect_ex((target,port)) # returns an error indicator if connection successful will return 0 otherwise will return 1
  if result == 0:
    print("Port {} is open".format(port))
  s.close()

# Make a clean exit if there is a keyboard interruption like ctrl+c
except KeyboardInterrupt:
  print("\nExiting program. ")
  sys.exit()

except socket.gaierror:
  print("Hostname could not be resolved.")
  sys.exit()
  
except socket.error:
  print("Could not connect to server.")
  sys.exit()
```
- This script is just and exercise (done in the PEH TCM course) so it definitely could be improved:
  - Handle other type of errors
  - ...

## Resources

{% embed url="https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course" %} Practical Ethical Hacking - TCM Security {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/python-101-for-hackers" %} Python 101 for Hackers - TCM Security {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/python-201-for-hackers" %} Python 201 for Hackers - TCM Security {% endembed %}
{% embed url="https://checkio.org/" %} Checkio programming challenges to learn Python {% endembed %}
