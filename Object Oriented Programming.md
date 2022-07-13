Discussed here are the three core concepts of OOP with python syntax and terminology:
[[#Encapsulation]], [[#Inheritance]] & [[#Polymorphism]]

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
Class Inheritance is what makes OOP efficient for code-reuse.  A new class inherets the original class functionality, but more can be easily added.  By allowing the same template to be used multiple times across a larger code-base.  Or for an existing class in another module to be used as a starting point for a more customised class.

## Basic Concept
Just add prenthesis around the original class when declaring the new one.

```Python
class MyChildClass(MyParentClass):
	#Do more stuff here, but inheret the original details from parent
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

Or another  example we can initialise the child class with an extra parameter, by calling the `__init__()` consructor from the parent.  In the example below, we add an attribute 'interes_rate' to a child of a parent class that didn't have one.
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
With the above, the argument syntax is the same  to perform the withdrawl method, but the behaviour will include a fee, if we the object is an instance of the checking account sub-class.  For example:

```Python
check_acct = CheckingAccount(1000, 25)
bank_acct = BankAccount(1000)

check_acct.withdraw(200)
bank_acct.withdraw(200)
Print(check_acct.amount, bank_account.amount )

>>> 795 800
```


# Polymorphism
Not up to this yet