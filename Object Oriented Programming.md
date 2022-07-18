Discussed here are the three core concepts of OOP with python syntax and terminology:
[[#Encapsulation]], [[#Inheritance]] & [[#Polymorphism]]  Mostly based on examples from The Datacamp course *Object Oriented Programming in Python*

# Encapsulation
An object represents something with State + Behaviour
Cobining these two is *encapsulation*

Eg. Customer class   = 
	- E-mail, phone number (States)
	- Place order, Cancel order (Behaviour)

Instance of a customer class object:
	- olly@olly.com, 555-123456  (States)
	- Ordered a black mountian bike (Behaviour)

In Python, *states* are called *attributes* represented by *variables* , *behaviours* are called *methods* and are represented by *functions*

List all attributes and methods of an object (a) by calling  `dir(a)`
To get help on class information for an object a:  `help(a)`

## Create basic classes

### Empty class
```python
class Customer:
	#put some code here
	pass
```

### Add a method to a class
```python
class Customer:

	def identify(self, name);
		print('I am Customer ' + name)
```

The method is just a typical Python function definition, except for the *self* argument.  The self is a special argument, refering to the data object, being an argument in the function.  Remember the class definition is just a template for objects, not an actual object.  

To call the method above the syntax would be:
```python
cust = Customer() # Create an instance of the Customer class
cust.identify('Laura') # Apply the identify method to the instance

>>> I am Customer Laura
```
So here `cust` has become an instance of the class `Customer`, and this instance is passed to the function `identify`  by the `self` parameter of the function.  As in othis is the equivalent of the function `identify(cust,Laura)` except that when the class definition was written, we don't want to use a specific object that doesn't even exist yet, or may be used any number of times in different instances.

### Add an attrubute to a class
Set an attribute of an object by using an asignment.  So to extend the previous example by creating a name attribute to the customer class:
```python
class Customer:

	def set_name(self, new_name):
		self.name = new_name # Asignment of new_name parameter to .name attribute
```
So at this point we can call the .name parameter with:

```python
cust = Customer() # Create an instance of the Customer class
cust.set_name('Laura') # Apply the identify method to the instance
print('I am customer', cust.name)

>>> I am customer Laura
```
Further we can a method and a parameter assignment, with the self parameter, to set both the paramter, and the method into the class definition, using the parameter from within the class definition.
```python
class Customer:

	def set_name(self, new_name):
		self.name = new_name # Asignment of new_name parameter to .name attribute

	def identify(self, name):
		print('I am customer ' + self.name)

cust = Customer() # Create an instance of the Customer class
cust.set_name('Laura') # Apply the identify method to the instance
print(cust.identify()) #The () are because we are applying a method

>>> I am customer Laura
```

## The `__init__()` constructor

Instead of instantiating an object with `object-name = class-name()` and then adding the attributes one by one using `set_`  methods, like above, it is better practice to use the `__init__` constructor.  With this approach, the object is instantiated, and attribute values passed in as parameters.   

Another advantage is that all the attributes can be declared at the top of the class definition (best practice), making them easy to find (if the class is a very long complex one), and they can be set to default values as parameter values, also helpful.

For example: 
```python
class Customer:
'''Write a docstring here for anyone who wants to use help(Customer) '''

	def__init__(self, name, balance=0)
		self.name = name # Asignment of new_name parameter to .name attribute
		self.balance = balance
		print('New customer', self.name, 'has a balance of', self.balance)


cust = Customer('Luis', 1000) # Create an instance of the Customer class

>>> New customer Luis has a balance of 1000
```

A slightly bigger example below.  Note that the `__init__` is treated like any other function, no need to indent/nest the methods that come next.

```Python
import numpy as np

class Point:
'''A class for menipulating a point on the XY plane'''

	def __init__(self, x=0, y=0):
		self.x = x
		self.y = y

	def reflect(self, axis):
		if axis == 'x':
			self.y = -1 * self.y
		if axis == 'y':
			self.x = -1 * self.x
		else:
			print('Invalid value for axis')

	def distance_to_origin(self):
		return np.sqrt(self.x **2 + self.y **2)

  
pt = Point(x=3.0)
pt.reflect("y")
print((pt.x, pt.y))
pt.y = 4.0
print(pt.distance_to_origin())

>>> (-3.0, 0)
>>> 5.0
```

## Class vs Instance level data
A class level data attribute can be stored in the class definition, and will apply to all instances.

```Python
class MyClass:
	CLASS_ATTRIBUTE_NAME = some_value
```

An instance level attribute needs defining using `self.attribute = some_value` where the value must be passed in as a parameter, or use a default value from the `__init__()` constructor.

The class level attribute can be changed by re-asignment with `ClassName.CLASS_ATTRIBUTE_NAME = some_new_value`

## Class level methods
Regular methods are already shared between every instance.
It is also possible to bind a method to a class, without any instance data.  So something in this form:

```Python
class MyClass:
	@classmethod       # Use a decorator to declare it as a class method
	def my_awsome_method(cls,args...)
		# Do some stuff
		# Do not use any instance attributes

MyClass.my_awesome_method(args)
```

Note that the method is called from `Class.method()` rather than `object.method()`  The main use case for doing this would to be to create an alternative constructor.  For example transform the data some way before the `__init__()` is called.  Here is an example where the data can be added from a file instead of as parameters:

```python
class Employee:
	def __init__(self, name, salary=30000):
		self.name = name
		self.salary = salary

 
	@classmethod   #The decorator is needed, but I don't know why - find out later   
	def from_file(cls, filename):  # Note how now we're using cls instead of self
		with open(filename, 'r') as f:
			name = f.readline()
		return cls(name)
```

When the above is called by `emp = Employee.from_file("some_file.txt")`  what happens is the class method is envoked, then passes the resulting name to the class using the `__init__` constructor.   So now the object `emp` has been instantiated with a name from a file, instead of needing the argument name.


# Inheritance
Class Inheritance is what makes OOP efficient for code-reuse.  A new class inherits the original class functionality, but more can be easily added.  By allowing the same template to be used multiple times across a larger code-base.  Or for an existing class in another module to be used as a starting point for a more customised class.

## Basic Concept
Just add prenthesis around the original class when declaring the new one.

```Python
class MyChildClass(MyParentClass):
	#Do more stuff here, but inherit the original details from parent
```
`Mychild` class is considered an instance of `MyParentClass`, but it can have more features that can be shared amongst other instances of `MyChildClass`.

Here is a simple example

```Python
class Employee:

	MIN_SALARY = 30000
	def __init__(self, name, salary=MIN_SALARY):
		self.name = name
		if salary >= Employee.MIN_SALARY:
			self.salary = salary
		else:
			self.salary = Employee.MIN_SALARY

	def give_raise(self, amount):
		self.salary += amount

class Manager(Employee):
	def display(self):
		print('Manager', self.name)

  
mng = Manager("Debbie Lashko", 86500)
print(mng.name)

>>> mng.display()
```

Or another  example we can initialise the child class with an extra parameter, by calling the `__init__()` consructor from the parent.  In the example below, we add an attribute `interest_rate` to a child of a parent class that didn't have one.
```Python
class SavingsAccount(BankAccount):

	#Constructor specifically for savings account
	def __init__(self, balance, interest_rate):
		BankAccount.__init__(self, balance) #Call the parent __init__() constructor
		self.interest_rate = interest rate  #The extra functionality
```
Instantiate a SavingsAccount object: `acct = SavingsAccount(1000, 0.03)`

## Customising functionality with inheritance
The above examples took a parent class and added new functionality.  It is also possible to use the parent class and modify it's original methods, so that the interface remains the same, but the behaviour is modified for the child instances (without having to re-write the whole original methods)

Suppose in the above example we want to add a withdrawl fee to the existing funtionality withdrawl, for the subclass CheckingAccount.

```Python
class CheckingAccount(BankAccount):
	def __init__(self, balance):
		BankAccount.__init__(self, content)
		self.limit = limit
	def deposit(self, amount):
		self.balance += amount
	def withdrawl(self, amount, fee=0)
		BankAccount.withdraw(self, amount - fee)
```
With the above, the argument syntax is the same to perform the withdrawl method, but the behaviour will include a fee, if the object is an instance of the checking account sub-class.  For example:

```Python
check_acct = CheckingAccount(1000, 25)
bank_acct = BankAccount(1000)

check_acct.withdraw(200)
bank_acct.withdraw(200)
Print(check_acct.amount, bank_account.amount )

>>> 795 800
```

Here is an example modifying the Pandas DataFrame class to allow the use of timestamps:

```Python
import pandas as pd

class LoggedDF(pd.DataFrame):

	def __init__(self, *args, **kwargs):
		pd.DataFrame.__init__(self, *args, **kwargs)
		self.created_at = datetime.today()
	
	def to_csv(self, *args, **kwargs):
		temp = self.copy()
		temp["created_at"] = self.created_at

	pd.DataFrame.to_csv(temp, *args, **kwargs)
```

Notice how in the very last line, the parent method was called and passed an object to it that isn't `self`. When you call parent methods in the class, they should accept _some_ object as the first argument, and that object is _usually_ `self`, but it doesn't have to be.

Also remember `*args` and `**kwargs`  are ways to set up the function for an unknown number of arguments, or keyword arguments.  For this example it makes sense, because there are lots of possible arguments that can be used for a Pandas DF, no point trying to include them all here.

## Customising python comparison operators
By default, two objects withe the same values for thier attributes are not equal, since they are still seperate objects, stored in different memory locations, with a different memory reference id.  However this behaviour can be changed by defining a different equality operator within the class definition.  Many classes use this, for example Pandas DFs, are considered `==` if the values in them are identical.  

For example below we redefine `==` to be true if the attributes `id` and `name` are equal:

```Python
class Customer:
	def __init(self, id, name)

	def __eq__(self, other) # A special constructor, called when == is used
		return (self.id == other.id) and (self.name ==other.name)
```

Using () around an expression returns a boolean.  It is possible to modify the other comparison operators also.  `!=, >=, <=, >, <`    If a child and a parent class both have a customised operator, the child one takes precidence, like other mothods and attribute values.

It is also good practice to check that two objects are the same type when making comparisons.  Such as in the example below:

```Python
class BankAccount:

	def __init__(self, number, balance=0):
		self.number, self.balance = number, balance

	def withdraw(self, amount):
		self.balance -= amount

	def __eq__(self, other):
		return (self.number == other.number) and \
		(type(self) == type(other))

acct = BankAccount(873555333)
pn = Phone(873555333)
print(acct == pn)

>>> False
```

## Customising string and repr information for the object

`__str__()` method is used when we call  `print(object)`, has basic information for the end user.  If nothing is defined, then just a memory address.  Otherwise for an array for example:
```Python
import numpy as np
print(np.array([1,2,3]))

>>> [1 2 3]
```

`__repr__()` method is used when we call `repr(obj)`, and should contain all the structure needed to reproduce the object.  For example
```Python
import numpy as np
repr(np.array([1,2,3]))

>>> array([1, 2, 3])
```

Here is an example where both `__str__` and `__repr__` are defined:

```python
class Employee:
	def __init__(self, name, salary=30000):
	self.name, self.salary = name, salary

	def __str__(self):
		s = "Employee name: {name}\nEmployee salary
			{salary}".format(name=self.name, salary=self.salary)
		return s

	def __repr__(self):
		r = "Employee(\"{}\", {})".format(self.name, self.salary)
		return r

emp1 = Employee("Amar Howard", 30000)

print(emp)
print(repr(emp))

>>> Amar Howard    #Note there is a newline character in the string
>>> 30000
>>> Employee("Amar Howard", 30000)
```

## Exception Handling
It is good practice to handle exceptions with exception errors, `try`, `except` (can be more than one) and `finally` (for the last resort if none of the excepts work, for example close open files) and make the messages and behaviours as granular as possible.  

Except is a class  its self in python python has built-in exception subclasses.  For example:
```Python
def invert_at_index(x, ind):
	try:
		return 1/x[ind]
	except ZeroDivisionError:
		print("Cannot divide by zero!")
	except IndexError:
		print("index out of range!")

a = [5,6,0,7]
print(invert_at_index(a, 1)) # Works okay
print(invert_at_index(a, 2)) # Potential ZeroDivisionError
print(invert_at_index(a, 5)) # Potential IndexError

>>> 0.16666666666666666 
>>> Cannot divide by zero! 
>>> None 
>>> index out of range! 
>>> None
```

If it is necessary to raise an exception yourself (even though the code may run without built-in errors).  In this case use `raise`.  For example:
```Python
def make_list_of_ones(length)
	if length <= =:
		raise ValueError("Invalid length!") #Stops the program and raises error
	return [1]*length
```
If no suitable exception exists a custom exception can be made by inherting and adding to an existing exception class.  For example for the earlier banking Customer class, if we want an exception for a negative balance:

```python
class BalanceError(Exception): pass # Created an empty sub-class of Exception

class Customer:
	def __init__(self, name, balance):
	if balance < 0:
		raise BalanceError("Balance has to be non-negative!")
	else:
		self.name, self.balance = name, balance
```
Here is a less trivial example with two exceptions:

```Python
class SalaryError(ValueError): pass
class BonusError(SalaryError): pass

class Employee:
	MIN_SALARY = 30000
	MAX_BONUS = 5000

	def __init__(self, name, salary = 30000):
		self.name = name

		if salary < Employee.MIN_SALARY:
			raise SalaryError("Salary is too low!")
		self.salary = salary

def give_bonus(self, amount):
	if amount > Employee.MAX_BONUS:
		raise BonusError("The bonus amount is too high!")

	if self.salary + amount < Employee.MIN_SALARY:
		raise SalaryError("The salary after bonus is too low!")

	self.salary +=amount
```

Best to list except blocks in order of specifity, from child -> parent so the most informative error is caught before the more general one.


# Polymorphism
Polymorphishm is the use of a unified interface to unified interface to operate on objects of different classes.  In this way a higher order function that uses a collection of objects, not necessarily from the same sub-classes can operate consistantly on them without needing to know their sub-type.

For example, the *withdraw* method in earlier banking examples should operate in the same way on each account (all that changes is the output, for example some accounts may have a withdrawl fee, others don't, either way they should be designed to operate with the same number of arguments)

### LSP
Inheretance should only be used if it satisfies the Liskov substitution principle:

*Base class hould be interchangeable with any of its subclasses without altering any properties of the program*

This means it should behave the same both **Syntactically** (Function arguments and returned values are compatible), and **Semantically** (The state of the object and program are consistent)
- No strenthed input conditions
- Subclass methods don't weaken output conditions
- No additional exceptions

Violations of the LSP would include
- Parent method requires 1 parameter, child requries 2.  (This could be fixed by adding a default parameter to the parent)
- Subclass strenthens input conditions, for example parameters fall in a narrower range
- The subclass can output a narrower range of outputs than allowed by the parent class.  So if this happened the parent class would throw an exception.
- Subclass changes additional attributes, not changed by the parent
- Subclass includes additional exceptions.  (If more exceptions are needed, they should go into the parent class)

Sticking to this avoids creating likely undetected problems further down the track.

**The golden rule for ineritance**: No LSP - No Inheritance

An example of a breach of LSP the circle-elipse (or rectangle-square) problem.  If a square class is inhereted from a rectangle class, but internally sets the two sides to be equal from just one parameter *w*, then the behaviour has changed.  The rectangle class could not be substituted for the square class, because setting the height *h* would no longer have any effect.  So it is a violation of LSP.

### Managing data access
By default all attribute and methods of any class is public.  They can be accessed from outside the class.  This is not the case in all programming languages.  There are ways to restrict data access.

- Naming conventions (discussed below)
- Use `@property` to customise access
- Overriding `__getattr__()` and `setattr__()` constructors

By convention, to indicate to other developers that a variable or method should not be accessed from outside the class a leading underscore is used  `obj._my_variable`, or `obj._method_name()`.  To make an attribute protectected so it can not be used at all, or inhereted by sub-classes use a double underscore `__my_variable`  Just remember not to use trailing double underscores, as those are reserved for built-in methods like `__init__()`

For example:
```Python
class Car:
    def __init__(self):
        print ("Engine started")
        self.name = "corolla"
        self.__make = "toyota"
        self._model = 1999

car_a = Car()
print(car_a.name)
>>> corolla

print(car_a.make)
>>> AttributeError: 'Car' object has no attribute 'make'
```

The main reason to use `__` is to prevent name clashes.  Somebody might inadvertantly use a variable name in an inhereted class that clashes with something already in the class, and over-write it.  


