Some of this is based on [Writing Efficient Python Code](https://app.datacamp.com/learn/courses/writing-efficient-python-code) from datacamp
## The Zen of Python (By Tim Peters)

To print this in a Python terminal just type 

```python
import this
```
- Beautiful is better than ugly
- Explicit is better than implicit
- Simple is better than complex
- Complex is better than complicated
- Flat is better than nested
- Sparse is better than dense
- Readability counts
- Special cases aren't special enough to break the rules
- Although practicality beats purity
- Errors should never pass silently
- Unless explicitly silenced
- In the face of ambiguity, refuse the temptation to guess
- There should be one-- and preferably only one --obvious way to do it. 
- Although that - way may not be obvious at first unless you're Dutch. 
- Now is better than never. Although never is often better than *right* now. 
- If the implementation is hard to explain, it's a bad idea. 
- If the implementation is easy to explain, it may be a good idea. 
- Namespaces are one honking great idea -- let's do more of those!

*Flat is better than nested* is something I can probably work on.  Use comprehensions, unpack iterators when I need, broadcast NumPy arrays, use NumPy over lists.

## Python Built-ins

Using these is a good idea when possible as they are highly optimised

Types:   `list`, `tuple`, `set`, `dict`
Functions: `print()`, `len()`, `range()`, `round()`, `enumerate()`, `map()`, `zip()`
Modules: `os`, `sys`, `itertools`, `collections`, `math`

`map` applies a function over an object, and can also be used with a lamda function
for example:
```python
nums = [1.5, 2.3, 3.4, 4.6, 5.0]
rnd_numbs = map(round, nums)
print(list(rnd_nums))

>>> [1, 2, 3, 4, 5]
```
Note that we have passed a reference to the function, not an instance of the function.  Map can also be applied to a lamda function.  So now we have a way to apply a function to a list of elements without writing a for loop.
```python
nums = [1, 2, 3, 4, 5]
sqrd_numbs = map(lamda x: x**2, nums)
print(list(sqrd_nums))

>>> [1, 4, 9, 16, 25]
```

## NumPy Arrays
For anything that involves the same a numerical operation over a series of values use NumPy arrays, as the operations are vectorised and will be orders of magnitude faster than doing things by looping through or working with lists.

Also there is less memory overhead, because numpy arrays are homogeneous in thier datatype.

### Array Broadcasting
By default, if an operation is performed on an array, it is performed on every element in the array.  For example:
```python
nums = np.array([-2, 1, 0, 1, 2])  #[*range(-2:2)]
print(nums_np ** 2)

>>> array([4,,1,0,1,4])
```

This is faster & better than than:
```python
sqrd_nums = [num**2 for num in range(-2:2)]
```

We can apply a boolean operator to an array easily too.
```python
nums = np.array([-2, 1, 0, 1, 2])
print (nums > 0)

>>> array([False, False, False, True, True])
```

Or apply a function to every element in an array
```python
nums = np.array([-2, 1, 0, 1, 2])
f = lambda x: x ** 2
sqrd_nums = f(x)
print(sqrd_nums)

>>> array([4,,1,0,1,4])
```

### Masking
To efficiently apply operations based on conditions use a mask.  Here is an example from BirdCLEF, setting everything to zero if the no-call was > 0.2 and > the other predictions:

```python
arr_view = np.concatenate((arr[:,:nocall_idx], arr[:,nocall_idx+1:]), axis=1)
row_max = np.amax(arr_view, axis=1)   # An array with shape (120,1)
mask = (row_max < arr[:,nocall_idx]) & (arr[:,nocall_idx] > 0.2)
arr[mask] = 0  #This is the line setting all to zero, if they aren't masked
```

## Performance Benchmarking
For quick comparisons of time and memory the iPython shell has some built-in tools of its own.

### %timeit

```python
%timeit rand_nums = np.random.rand(1000)

>>> 8.61 μs ± 69 ns per loop (mean ± std. dev of 7 runs, 100000 loops each)
```
The number of runs and loops can be specified with -r and -n flags.  For example 
```python
%timeit -r2 -n10 rand_nums = np.random.rand(1000)

>>> 8.61 μs ± 69 ns per loop (mean ± std. dev of 2 runs, 10 loops each)
```
For a code block for a for loop use  \%\% 
```python
%%timeit
nums = []
for x in range(10):
	nums.append(x)

>>> 8.61 μs ± 69 ns per loop (mean ± std. dev of 7 runs, 100000 loops each)
```
To save out the time result as a variable, use `-o` flag: 
```python
times = %timeit -0  my_list = np.random.rand(1000)
```

### line_profiler
This is an external Python package, that will provide good stats on a function in an IPython shell.  Needs to be installed into the Python environment `pip install line_profile`  Then loaded using magic commands.
```python
%load_ext line_profiler
lprun -f myfunction(my_arguments)
```
Gives a line by line breakdown for each line within the function, and the percentage spent on each line.  So super useful to find bottlenecks.

### memory_profiler
Same deal & syntax as with `line_profiler`.  Use pip or conda to install `pip install memory_profiler`   The only new catch is that it only works on imported functions from modules saved as an external .py file.    Provides a line by line breakdown of memory resource increments within the function.

### Other Evaluation Methods
There is nothing wrong with using other perf, time etc and making my own timing functions or decorators. 

## Working with lists
- Use list comprehensions to transform elements in a list, generally faster than for loops
- If necessary, use map, to perform any function on list elements or an iterator
- Unpack iterators into lists when necessary rather than use a loop
```python
LOWER = 34
UPPER = 789
my_fancy_list = [*range(lower:upper)]
```
- Use the collections & itertools built in modules, they are well optimised
- Convert lists to the built-in set datatype

### The `collections` module

A python builtin with handy & fast methods, and some special datatypes.
- `namedtuple`  a sub-class of tuple with named fields (could use a bit like a dict)
- `deque` list-like container with faster appends and pops
- `Counter()` A fast method that produces a dictionary of counts from hashable objects
- `OrderedDict`  A dict that retains the order of entries

Here is an example of using counter.
```python
# Each Pokémon's type (720 total)
poke_types = ['Grass', 'Dark', 'Fire', 'Fire', ...]
from collections import Counter
type_counts = Counter(poke_types)
print(type_counts)

>>> Counter({'Water':105, 'Normal':92, 'Bug': 65,...})
```

### The `itertools` module
Has a `count` method to work with `map` or `zip` when needing a sequence of numbers.  For example you could produce a list of sequenced pairs of tuples 
```python
my_list = ['New Zealand`,`Australia`,` `Canada`, `USA`, `England']
numbered_tuples = {*zip(count(start=1,step=1), my_list)`)}
```
Another super handy method of collections is `combinations()`  Use this to do combinatorics on lists.
```python
from itertools import combinations

poke_types = ['Bug', 'Fire', 'Ghost', 'Grass', 'Water'] 
print(combinations(poke_types, 2))

[('Bug', 'Fire'), ('Bug', 'Ghost', 'Bug, Grass')...
('Fire', 'Ghost'), ('Fire, Grass'), ...]

```

### Common patterns with sets:
Membership testing using `set` datatype is much faster than the same thing in a tuple or a list:
```python
in_list = 5 in {1, 2, 5 , 7, 12}
print(in_list)
>>>  True   
```
Getting unique items is also faster, sets can only have one of each element, lists can have any number.
```python
big_list = ['Grass', 'Psychic', 'Dark', 'Bug', ...etc]
unique_items = set(big_list)
```

The other common methods for lists are `.union()`, `.symmetric_difference()`, `intersection()` & `difference()`.  All very handy.