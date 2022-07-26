# Lamda Functions

# Decorators
[Start with this](https://www.geeksforgeeks.org/decorators-in-python/)
[Or this one](https://realpython.com/primer-on-python-decorators/)

Decorators are used to modify the behaviours of functions, including class methods.  Best to see them as a convenient shortcut to a way to wrap a modifying function around another function.

## Understand functions as objects 
Functions are *first class objects*, meaning that they can be stored as any other object (in a list for example), or they can be passed into another function as an argument, or returned from another function as a result.

When a function is being passed as an object, there are no `()`, because it is not actually being evalueted, it is just a reverence to the function object.  The `()` (with arguments if they are needed) implies the evaluation of the function. 

An example of passing a function to another function is below.  `say_hello`  and `be_awesome` are passed to a third function `greet_bob`, which takes those functions as an argument, and does something with them.
```Python
def say_hello(name):
    return f"Hello {name}"

def be_awesome(name):
    return f"Yo {name}, together we are the awesomest!"

def greet_bob(greeter_func):
    return greeter_func("Bob")

>>> greet_bob(say_hello)
>>> 'Hello Bob'

>>> greet_bob(be_awesome)
>>> 'Yo Bob, together we are the awesomest'
```

A local function inside another function can not be accessed unless it is returned as a result from that function.  Here is an example of this:

```Python
def parent(num):
    def first_child():
        return "Hi, I am Emma"

    def second_child():
        return "Call me Liam"

    if num == 1:
        return first_child
    else:
        return second_child

first = parent(1)
second = parent(2)

first 
>>> <function parent.<locals>.first_child at 0x7f599f1e2e18>

second
>>> <function parent.<locals>.second_child at 0x7f599dad5268>
```
The output above is saying that first & second are local functions within a parent. But since they are assigned from the output they are now accessable.

```Python
first()
'Hi, I am Emma'

>>> second()
'Call me Liam'
```

## Simple Decorator with out using @ syntax
To put it most simply.  **Decorators wrap a function, modifying its behavior.** Consider this example below, using familiar syntax discussed above. 
```python
def my_decorator(in_func):
    def wrapper():
        print("Something is happening before the function is called.")
        in_func()
        print("Something is happening after the function is called.")
    return wrapper

def say_whee():
    print("Whee!")

say_whee = my_decorator(say_whee)
# This like modifying any object, eg x = x + 5,  x now references something different.
```
  The decoration line  `say_whee = my_decorator(say_whee)` turns say_whee (without `()`) into a reference to a function `mydecorator(say_whee)`. So when say_whee() is called for evaluation (with `())`,  instead of simply printing "Whee" as it would from the `say_whee()` function definition, we get a function modified by the `my_decorator` function which returns the `wrapper` function as an object.  

```python
>>> say_whee()
>>> Something is happening before the function is called.
>>> Whee!
>>> Something is happening after the function is called.
```

## Simple decorator using `@` (pie syntax)
This does exactly the same as the previous example `@` is a shorthand way to replace the second half.  It also makes it more obvious that the intent is to modify the `say_whee` using a wrapper function.
```python
def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

@my_decorator
def say_whee():
    print("Whee!")
    ````
This is the behaviour like before:

```python
>>> say_whee()
>>> Something is happening before the function is called.
>>> Whee!
>>> Something is happening after the function is ca
```
## Decoration Use Cases
### Multi-use decorators

### Special decorators within class definitions



# Comprehensions
## List comprehensions
[Based on this explanation](https://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/)
List comprehensions are just a super convenient way to create one list as a subset of another, with an optional transformation as well.  

```
new_things = [] 
for ITEM in old_things: 
	if condition_based_on(ITEM): 
		new_things.append(some__transform(ITEM))
```

The above becomes:
```python
new_things = [some_transform(ITEM) for ITEM in old_things if condition_based_on(ITEM)]
```

It's that easy!  But gets a bit more tricky for a nested loop.  Here is an example:
```python
#The long way:
flattened = []
for row in matrix:
    for n in row:
        flattened.append(n)

#List comprehension:
flattened = [n for row in matrix for n in row]
```
Just be careful to get the order of the two `for` statements right, or the loops will be swapped.

## Dictionary and set comprehensions
The syntax is much the same for dictionaries and sets, or any other iterable.  

This example produces a set.  ```first_letters = {w[0] for w in words}``` Note the curly brackets, doesn't make it a dictionary, `{}` when empty is reserved specifically for dictionsries, but `{1, 2, 3}` is a set.

Here is a handy way to swap the keys and values for a dictionary (just be careful to only use this if the values are unique!)

```Python
#long way
flipped = {}
for key, value in original.items():
    flipped[value] = key

# dict comprehension
flipped = {value: key for key, value in original.items()}
```

# Relative/absolute paths to modules

# Set variables with conditional statements
Just a bit more compact than the obvious multi-line ways.

#### One-line if-else
```Python
true_expression if conditional else false_expression
#for example:
y = 4 if x <10 else y = 5

# another example:
print('N' if lat < 0 else 'S')

>>> S 
```

#### Use Boolean values in a one-line conditional statement
This is very nice when possible, use the equivalense of true=1, and false=0.  For example:
```Python
def print_coords(lat, long):
    ns = 'NS'[lat < 0]  #True (1) defaults to "N" and False(0) defaults to "S"
    we = 'EW'[long < 0] # True (1) defaults to "E" and False(0) defaults to "W
    print(f'{abs(lat):.2f}°{ns}, {abs(long):.2f}°{we}')
 
lat, long = 57.682605, 11.983421
 
print_coords(lat, long)
>>> 57.68°N, 11.98°E
```

### Return a boolean from an expression
```Python
lat = 60
long = 30

Answer = (lat > long)
print(Answer)

>>> True
```

