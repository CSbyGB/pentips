# Python programming

- It is really useful for a pentester to know about python and be able to script something. Being able to automate things can definitely help us go quicker in our daily practice.
Of course we do not need to be a developer but it is very helpful to be able to read code.
> notes from my practice, TCM Academy and DeepLearning.AI

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
### Using f-Strings

f-Strings is useful to mix strings with computations or data

```python
# Will print The temperature 75F in degrees celsius is 23.88888888888889C
print(f"The temperature 75F in degrees celsius is {(75 - 32) * 5 / 9}C")
# It works also with multiligne strings
print(f"""
    Most countries use the metric system for recipe measurement, 
    but American bakers use a different system. For example, they use 
    fluid ounces to measure liquids instead of milliliters (ml).
    
    So you need to convert recipe units to your local measuring system!
    
    For example, 8 fluid ounces of milk is {8 * 29.5735} ml.
    And 100ml of water is {100 / 29.5735} fluid ounces.
""")
# This will print
''' 
 Most countries use the metric system for recipe measurement, 
    but American bakers use a different system. For example, they use 
    fluid ounces to measure liquids instead of milliliters (ml).
    
    So you need to convert recipe units to your local measuring system!
    
    For example, 8 fluid ounces of milk is 236.588 ml.
    And 100ml of water is 3.381405650328842 fluid ounces.
'''

# It also works on variables
my_name = "gabrielle"
print(f"Hello {my_name}!")
# this will print Hello gabrielle!
# And we can mix var and computation as follow
fav_num = 8
print(f"Your favorite number plus 10 is {fav_num+10}")
# this will print Your favorite number plus 10 is 18
```

## type()

In Python, you can check the type of any data that you are using. To check the data type, you can use the type() function.

```python
# str
type("mystring")
# int
type(100)
# float
type(2.99)
```

## Math

The order of operations in Python is the same as in arithmetic. First, you compute parentheses, then exponents, then you multiply and divide (from left to right), and finally, you add and subtract (from left to right).

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
# convert from from Fahrenheit to Celsius
print((75 - 32) * 5 / 9)
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

Declare a list

```python
friends_list = ["Tommy", "Isabel", "Daniel"]
print(friends_list)
# will print ['Tommy', 'Isabel', 'Daniel']
len(friends_list)
# will print 3
print(friends_list[1]) 
# Output: Isabel
# add single element to list
friends_list.append("Otto")
#using remove
friends_list.remove("Tommy")
```

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

## Sys

```python
import sys
import time

# get info on system
def get_info():
	print(sys.version)
	print(sys.executable)
	print(sys.platform)

# we can use sys for user input
	# Here it is going to wait for user input until user types exit
def user_input():
	for line in sys.stdin:
		if line.strip() == "exit":
			break
		sys.stdout.write(">> {}".format(line))

# writes on the same line
def oneline():
	for i in range(1,5):
		sys.stdout.write(str(i))
		sys.stdout.flush()

# writes on different lines
def multiplelines():
	for i in range(1,5):
		print(i)

# makes a progress bar
def progress_bar():
	for i in range(0, 51):
		time.sleep(0.1)
		sys.stdout.write("{} [{}{}]\r".format(i, '#'*i, "."*(50-i)))
		sys.stdout.flush()
	sys.stdout.write("\n")

# will print all args when the script is launched
def print_args():
	print(sys.argv)

	if len(sys.argv) != 3:
		print("[X] To run {} enter a username and password".format(sys.argv[0]))
	username = sys.argv[1]
	password = sys.argv[2]
	print("{}:{}".format(username, password))


# sys allows to access the path where python will search for modules
def get_modulespath():
	print(sys.path)

def get_all_modules():
	print(sys.modules)


get_info()
user_input()
oneline()
progress_bar()
print_args()
get_modulespath()
get_all_modules()

# Exit with an exit code mentionning everything was ok
sys.exit(0)

# Note: to check exit code in terminal we can use echo $?
```

## Reading and writing files

```python
# file has to exist here we put a file in the same dir
f = open("top-10.txt")
print(f)

f = open("top-10.txt", 'rt')
print(f)
print("-----")
print("read")
print(f.read())
print("-----")
f = open("top-10.txt", 'rt')
print("readlines")
print(f.readlines())
# will not print content another time
print(f.readlines())
# will print content again if we ask for it with seek
f.seek(0)
print(f.readlines())
# iterate over eachline and strip off chars
f.seek(0)
for line in f:
	print(line.strip())

f.close()

# write to a file
f = open("test.txt", "w")
f.write("test line!")
f.close()
# read it to see if it exists:
f = open("test.txt")
print(f.read())
# append an existing file
f = open("test.txt", "a")
f.write("test line! again")
# check results
f = open("test.txt")
print(f.read())

# get info on the file
print(f.name)
print(f.closed)
print(f.mode)

# for larger file we can use the file object
with open('rockyou.txt', encoding='latin-1') as f:
	for line in f:
		pass
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


## Virtual Environments

We might sometimes need to use a version for a script and another version for another
Create and isolate python virtual environments not dependant
With virtualenv we can have multiple version of a package installed and usable in a system at the same time
`pip install virtualenv`
We can then create a folder and launch our virtual env in it:
```bash
mkdir virtual demo
cd virtual-demo
python3 -m venv env
```

We then have to activate it:
```bash
source env/bin/activate
```

We can see that our env is launched  
![image](https://user-images.githubusercontent.com/96747355/180493387-7b360d2d-2247-4711-8c71-7a354e47d56a.png)  

By default our virtual env does not contains our previously installed python packages or any modules
We can check which virtualenv is being called with which python3:  
![image](https://user-images.githubusercontent.com/96747355/180493488-88f6d22e-0cf9-476c-bce2-4c45576f1ebe.png)  

On our host (outside of our virtualenv) the same command gives this:  
![image](https://user-images.githubusercontent.com/96747355/180493663-e5662665-fffb-4095-8222-d5fd88649db9.png)  

And in our virtual environment we can install anything we need
We can have multiple virtual environments running at the same time.
One we finished with the virtualenv we can type deactivate

## Resources

{% embed url="https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course" %} Practical Ethical Hacking - TCM Security {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/python-101-for-hackers" %} Python 101 for Hackers - TCM Security {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/python-201-for-hackers" %} Python 201 for Hackers - TCM Security {% endembed %}
{% embed url="https://checkio.org/" %} Checkio programming challenges to learn Python {% endembed %}
