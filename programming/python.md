# Python programming

- It is really useful for a pentester to know about python and be able to script something. Being able to automate things can definitely help us go quicker in our daily practice.


## shebang

- For python our script need to have this shebang in the first line `#!/bin/python3`

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


