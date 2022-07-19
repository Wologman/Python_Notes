# Lamda Functions

# Decorators
[Start with this](https://www.geeksforgeeks.org/decorators-in-python/)
[Or this one](https://realpython.com/primer-on-python-decorators/)

Decorators are used to modify the behaviours of functions or classes.




# List Comprehensions

# Good practice for relative/absolute paths to modules

# Pythonic ways to do set variables with conditional statements
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

##### Use Boolean values in a one-line conditional statement
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

