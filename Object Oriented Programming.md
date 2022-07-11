### The basic concept
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

#### Create a basic class

##### Empty class
```python
class Customer:
	#put some code here
	pass
```

##### Add a method to a class
```python
class Customer:

	def identify(self, name)
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

##### Add an attrubute to a class
Set an attribute of an object by using an asignment.  So to extend the previous example by creating a name attribute to the customer class:
```python
class Customer:

	def set_name(self, new_name)
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

	def set_name(self, new_name)
		self.name = new_name # Asignment of new_name parameter to .name attribute

	def identify(self, name)
		print('I am customer ' + self.name)

cust = Customer() # Create an instance of the Customer class
cust.set_name('Laura') # Apply the identify method to the instance
print(cust.identify()) #The () are because we are applying a method

>>> I am customer Laura
```

##### The `__init__` constructor