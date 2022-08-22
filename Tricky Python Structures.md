# Contents
[[#Lamda Functions]]
[[#Decorators]]
[[#Comprehensions]]
[[#Relative absolute paths to modules]]
[[#Set variables with conditional statements]]

# Lamda Functions

# Decorators
[Start with this](https://www.geeksforgeeks.org/decorators-in-python/)
[Or this one](https://realpython.com/primer-on-python-decorators/)

Decorators are a convenient syntax to a way to wrap a modifying function around another function.  The same thing can be achieved with more functions, but this is a cleaner, more re-usable and more convenient approach.

## Understand functions as objects 
Functions are *first class objects*, meaning that they can be stored as any other object (in a list for example), or they can be passed into another function as an argument, or returned from another function as a result.

When a function is being passed as an object, there are no `()`, because it is not actually being evaluated, it is just a reverence to the function object.  The `()` (with arguments if they are needed) implies the evaluation of the function. 

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
>>> first()
>>> 'Hi, I am Emma'

>>> second()
>>> 'Call me Liam'
```

## Simple Decorator without using `@` syntax
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
# This line like modifying any object, eg x = x + 5,  x now references something different.
```

  The decoration line  `say_whee = my_decorator(say_whee)` turns say_whee (without `()`) into a reference to a function `mydecorator(say_whee)`. So when say_whee() is called for evaluation (with `())`,  instead of simply printing `'Whee'` as it would from the `say_whee()` function definition, we get a function modified by the `my_decorator` function which returns the `wrapper` function as an object.  
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

This is the same behaviour as before:

```python
>>> say_whee()
>>> Something is happening before the function is called.
>>> Whee!
>>> Something is happening after the function is called
```

### Introspection
If the python introspection tools (Like `__name__` or `help()`, or just the object reference, eg `say_whee`) are run on a decorated function, what is returned (correctly) is the decorating function, not the original one, which may not be very helpful.  For example, in the above definitions `say_whee.__name__` would return `my_decorator`.  

To get around this the decorator its self can use the decorator from the functools library `@functools.wraps` and the introspection will then be directed to the wrapped function instead.  Note that `@functools.wraps(func)` preceeds the wrapper function, just like any other decorator.
```python
import functools

def do_twice(func):
    @functools.wraps(func)
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice
```

Now we get a more informative result

```python
>>> say_whee.__name__
>>>'say_whee'
```

## More complex decorator structures
### Multi-use decorators
Decorators don't need to be uniquely written for a particular function.  It might be attractive to store a collection of commonly used decorators in their own module, and call them for various purposes.  Here is a simple example, where the decorators are stored in the module `decorators.py`

```python
def do_twice(func):
    def wrapper_do_twice():
        func()
        func()
    return wrapper_do_twice
```

Then to call this from some other script (with the PYTHONPATH variable suitably set)

```python
from decorators import do_twice

@do_twice
def say_whee()
	print('Whee!')

>>> say_whee()
>>> Whee!
>>> Whee!
```

### Decorating functions with arguments
Funtions with arguments face the problem that the decorator would also need to be defined for those arguments.  But this makes the decorators less versatile.

The solution is to use `*args` and `**kwargs` as placeholders in the decorator definition for an unknown number of aguments and keyword arguments (potentially none).
```python
def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        func(*args, **kwargs)
    return wrapper_do_twice

@do_twice
def greet(name):
    print(f"Hello {name}")

>>> say_whee()  # from before
>>> Whee!
>>> Whee!
>>>
>>> greet('World')
>>> Hello World
>>> Hello World
```

### Decorators with arguments themselves
First try to understand the finer points about inner functions in Python. 

#### Closure & Inner (Nested) Functions
[Based on this tutorial](https://realpython.com/inner-functions-what-are-they-good-for/)
In Python, inner functions have direct access to the enclosing function, *even after the enclosing function has returned the inner function* (this would be a *closure*).  The outer function creates a namespace for the inner function. 

Here is an example calculating the factorial of a number, but doing some validaton before passing the value through to the inner function for evaluation.

```python
def factorial(number):
	# Validate input
	if not isinstance(number, int):
			raise TypeError("Sorry. 'number' must be an integer.")
	if number < 0:
		raise ValueError("Sorry. 'number' must be zero or positive.")

	# Calculate the factorial of number
	def inner_factorial(number):
		if number <= 1:
			return 1
		return number * inner_factorial(number - 1)
	return inner_factorial(number)

print(factorial(4))

>>> 24
```

There is something else subtle going on in this particular example.  The inner function is actually returning its self for evaluation, creating a loop until the variable `number` = 1.  Nice.

Uses for inner functions include:
- Provide encapsulation and hide functions from external access  -- A class definition does this, but so does an inner function inside a normal function.
- Write helper inner functions
- Create closures and decorators

#### Helper Inner Functions Vs Private Helper Functions
Using an inner helper function rather than a top-level function has the potential advantage of providing encapsulation.  However if need be the inner function could be extracted as a top-level function by using a single leading underscore.  `_my_inner_function()` so it will be private to that module or class.

#### Retaining state with inner functions by creating closures
A closure is the encapsulation of an inner function along with variables accessed from the surrounding function.  The process is simple enough:  

1.  Create an inner function.
2.  Reference variables from the enclosing function.
3.  Return the inner function.

The returned function now has the variables it accessed as a permanent state.  If it is necessary to change those variables, it will need to be performed with a getter and setter methods, but I'm not going to worry about that now.

#### Using nested inner functions to pass decorator arguments
Suppose we want to make the decorator its self behave in a changable way by using arguments.  For example, extend the above `do_twice()` decorator to any `@repeat(n_times)`.  We can do this by wrapping a second function around the decorated function to handle the arguments and create a closure.    -- elaborate on this.

```python
def repeat(num_times):
    def decorator_repeat(func):
        @functools.wraps(func)
        def wrapper_repeat(*args, **kwargs):
            for _ in range(num_times):
                value = func(*args, **kwargs)
            return value
        return wrapper_repeat
    return decorator_repeat
```

### Returning values from decorated functions
Just remember that the wrapper function by default does not return anything.  So if the function being decorated is intended to return a result, this will have to be built into the decorator return statement too.

Using the do-twice example from before, but fixing up this to return a result.
```python
def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice

@do_twice
def return_greeting(name):
    print("Creating greeting")
    return f"Kia Ora {name}"

def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice

>>> return_greeting("Zoe")
>>> Creating greeting
>>> Creating greeting
>>> 'Kia Ora Zoe'
```
Notice how above the returned result is only printed once, while the static method `print("Creating greeting")` was performed twice.

### Nested decorators
Decorators can be nested simpy by stacking them one after another.  The outer-msot decorator goes first.  Just be careful with the above points about returning results, and comping with arguments.

### Decorating methods within class definitions
`@classmethod()`, `@staticmethod()`,  and `@property()`, are built-in decorators commonly used within class definitions.  So they are wrapping a regular instance methods and variables in a function & structure that limits some of their uses.  This is discussed further  in [[Object Oriented Programming]]

`@classmethod()` returns a class method for a given function.

`@staticmethod()` returns a static method for a given function.

`@property` returns the internal attribute storing the data.  

It is also possible to decorate the methods within a class just like decorating any other function as discussed in earlier sections.

### Decorating the whole class
This is also a thing to be aware of, but I'm not likely to use.  Here is an example where a decorator is used to ensure a class is a singleton (a class where there is only one instance)

```python
import functools

def singleton(cls):
    """Make a class a Singleton class (only one instance)"""
    @functools.wraps(cls)
    def wrapper_singleton(*args, **kwargs):
        if not wrapper_singleton.instance:
            wrapper_singleton.instance = cls(*args, **kwargs)
        return wrapper_singleton.instance
    wrapper_singleton.instance = None
    return wrapper_singleton

@singleton
class TheOne:
    pass

first_one = TheOne()
another_one = TheOne()
```

Now the class `TheOne` is a singleton.  As demonstrated below:

``` python
>>> id(first_one)
140094218762280

>>> id(another_one)
140094218762280

>>> first_one is another_one
True
```

## Some useful decorator examples
### Timer decorators
An example of a handy decorator measuring the  runtime of a function.  Note that it's also using the functools.wraps(func) decorator so it doesn't mess with the Python introspection functionality, and placeholders for any number of arguments.

```Python
import time

def timer(func):
    """Print the runtime of the decorated function"""
    @functools.wraps(func)
    def wrapper_timer(*args, **kwargs):
        start_time = time.perf_counter()    # 1
        value = func(*args, **kwargs)
        end_time = time.perf_counter()      # 2
        run_time = end_time - start_time    # 3
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
        return value
    return wrapper_timer

@timer
def waste_some_time(num_times):
    for _ in range(num_times):
        sum([i**2 for i in range(10000)])


>>> waste_some_time(999)
>>>Finished 'waste_some_time' in 0.3260 secs
```

### Using a decorator to help debugging
The following `@debug` decorator will print the arguments a function is called with as well as its return value every time the function is called:

```python
import functools

def debug(func):
    """Print the function signature and return value"""
    @functools.wraps(func)
    def wrapper_debug(*args, **kwargs):
        args_repr = [repr(a) for a in args]                      # 1
        kwargs_repr = [f"{k}={v!r}" for k, v in kwargs.items()]  # 2
        signature = ", ".join(args_repr + kwargs_repr)           # 3
        print(f"Calling {func.__name__}({signature})")
        value = func(*args, **kwargs)
        print(f"{func.__name__!r} returned {value!r}")           # 4
        return value
    return wrapper_debug
```

Breaking down the above with some explanation:
1. Creates a list of the positional arugments using the repr() function.  
2. Create a list of the keyword arguments
3. Join the above together.
4. Print the return value

With a simple function you write yourself, there might be easier ways to debug, but this could also be applied to some imported function you don't really know much about.

### Monitor the state of a function
Say we wish to monitor some state of a function, that wouldn't normally be returned, but we don't want to modify the function its self.  This is an excellent reason to use a decorator.

Here is a simple example where we store a state, in this case the number of calls of a function, in the wrapper.

```Python
import functools

def count_calls(func):
    @functools.wraps(func)
    def wrapper_count_calls(*args, **kwargs):
        wrapper_count_calls.num_calls += 1
        print(f"Call {wrapper_count_calls.num_calls} of {func.__name__!r}")
        return func(*args, **kwargs)
    wrapper_count_calls.num_calls = 0
    return wrapper_count_calls

@count_calls
def say_whee():
    print("Whee!")

>>> say_whee()
>>> Call 1 of 'say_whee'
>>> Whee!

>>> say_whee()
>>> Call 2 of 'say_whee'
>>> Whee!
```

#### Monitor state using a class as a decorator
Since we are trying to monitor a state, the OOP way to do this is to use a class.  Classes can be decorators too, but they need to be made callable using the `.__call__()` constructor, which takes the place of the previously used wrapper function.  We also need an `__init__()`  constructor, to initialise the instance and the states to be monitored.

Here is the previous example rewritten as a class:

```python
import functools

class CountCalls:
    def __init__(self, func):
        functools.update_wrapper(self, func)
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        print(f"Call {self.num_calls} of {self.func.__name__!r}")
        return self.func(*args, **kwargs)
```

The behaviour is exactly the same, by decorating functions with `@CountCalls`.  Note that now we use `functools.update_wrapper(self, func)` where previously we would have used `@functools.wraps`, to take care of introspection.

# Comprehensions
Comprehensions are a handy way to conditionally select some elements from a Python iterable.

## List comprehensions
[Based on this explanation](https://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/)
List comprehensions create one list as a subset of another, with an optional transformation as well.  
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

## One-line if-else
```Python
true_expression if conditional else false_expression
#for example:
y = 4 if x <10 else y = 5

# another example:
lat, long = -45, 73
print('N' if lat < 0 else 'S')

>>> S 
```

## Boolean values in a one-line conditional statement
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

## Return a boolean from an expression
```Python
lat = 60
long = 30

Answer = (lat > long)
print(Answer)

>>> True
```

