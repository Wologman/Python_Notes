[PEP8 Guidelines tutorial](https://realpython.com/python-pep8/)
[PEP8 Python.org](https://peps.python.org/pep-0008/)

Functions, methods, and variables
```python
lower_case_with_underscores_or_single_word
```

Class
```python
Model, MyClass, UseCamelCase
```

Constant
```python
UPPERCASE, UPPERCASE_WORDS_WITH_UNDERSCORE
```

Module
```python
lowercaseword.py
```

Package
```python
short.py lower.py case.py, word.py
```

### Blank Lines
Suround top level classes and functions by two blank lines
method 

Function definitions inside classes by a single blank line

Use blank lines inside functions sparingly, just to help understand logic inside the function

```python
class MyFirstClass:
    pass


def top_level_function():
	def inner_method_1():
		return None

	def inner_method_2():
		return None
	return None
```