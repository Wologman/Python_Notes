# Lamda Functions

# Decorators
[Start with this](https://www.geeksforgeeks.org/decorators-in-python/)
[Or this one](https://realpython.com/primer-on-python-decorators/)

Decorators are used to modify the behaviours of functions, including class methods.  Best to see them as a convenient shortcut to a way to wrap a modifying function around another function.

## Understand functions as objects 
Functions are *first class objects*, meaning that they can be stored as any other object (in a list for example), or they can be passed into another function as an argument, or returned from another function as a result.

When a function is being passed as an object, there are no `()`, because it is not actually being evalueted, it is just a reverence to the function object.  the `()` (with arguments if they are needed) implies the evaluation of the function. 

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




## Functions passed to other functions
## Decorators
## Special decorators within class definitions




# List Comprehensions

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

