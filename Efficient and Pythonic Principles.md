Some of this is based on [Writing Efficient Python Code](https://app.datacamp.com/learn/courses/writing-efficient-python-code) from datacamp

## The Zen of Python (By Tim Peters)

To print this in a python terminal just type 

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
- Althouch practicality beats purity
- Errors should never pass silently
- Unless explicitly silenced
- In the face of ambiguity, refuse the temptation to guess
- There should be one-- and preferably only one --obvious way to do it. 
- Although that - way may not be obvious at first unless you're Dutch. 
- Now is better than never. Although never is often better than *right* now. 
- If the implementation is hard to explain, it's a bad idea. 
- If the implementation is easy to explain, it may be a good idea. 
- Namespaces are one honking great idea -- let's do more of those!

*Flat is better than nested* is something I can pobably work on.  Use comprehensions, unpack iterators when I need, broadcast numpy arrays, use numpy over lists.

## Python Builtins

Using these is a good idea when possible as they are highly optimised

Types:   `list`, `tuple`, `set`, `dict`
Functions: `print()`, `len()`, `range()`, `round()`, `enumerate()`, `map()`, `zip()`
Modules: `os`, `sys`, `itertools`, `collections`, `math` etc.

`map` applies a function over an object, and can also be used with a lamda function
e.g:

```python
nums = [1.5, 2.3, 3.4, 4.6, 5.0]
rnd_numbs = map(round, nums)
print(list(rnd_nums))

>>> [1, 2, 3, 4, 5]
```

Map can also be applied to a lamda function.  So now we have a way to apply a function to a list of elements without writing a for loop.

```python
nums = [1, 2, 3, 4, 5]
sqrd_numbs = map(lamda x: x**2, nums)
print(list(sqrd_nums))

>>> [1, 4, 9, 16, 25]
```

## NumPy Arrays
For anything that involves the same a numerical operation over a series of values use NumPy arrays, as the operations are vectorised and will be orders of magnitude faster than doing things by looping through lists.

Also there is less memory overhead, because numpy arrays are homogeneous in thier datatype.

### Array Broadcasting
By default, if an operation is performed on an array, it is performed on every element in the array.  For example:
```python
nums = np.array(range(-2:2))
print(nums_np ** 2)

>>> array([4,,1,0,1,4])
```

This is faster& better than than:
```python
sqrd_nums = [num**2 for num in range(-2:2)]
```

We can apply a boolean operator to an array easily too.
```python
nums = np.array(range(-2:2))
print (nums > 0)

>>> array([False, False, False, True, True])
```

Or apply a function to every element in an array
```python
nums = np.array(range(-2:2))
f = lambda x: x ** 2
sqrd_nums = f(x)
```
