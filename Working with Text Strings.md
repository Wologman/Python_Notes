
## f-strings
```python
print(f'{old_length - len(df)} rows removed, leaving {len(df)}')
```

## `.format()` method
```python
txt1 = "My name is {fname}, I'm {age}".format(fname = "John", age = 36)
```

## % operator
For anything beyond Python 3.6 this is not recommended, just putting here for completion.

```python
name = 'Wong Fei Hung'
print('Ni Hao %s') % name

>>> Ni Hao Wong Fei Hung
```

## `.split()` method
Super useful.  `string.split(delimiter)` returns a list from the string, with elements seperated by the delimiter.