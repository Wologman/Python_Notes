## The Multiprocessing Module

This is a simple and convenient way to work with multiple cores.  Just be careful on windows to protect the function by a `if __name__ == "__main__":` block, so that it doesn't go propagating its self in the spawned script.

This is how to process a simple list example, using the maximum physical cores, for some custom function `process_item`:
```python
import multiprocessing as mp 

num_processes = mp.cpu_count() 
if __name__ == "__main__":
	with mp.Pool(processes=num_processes) as pool: 
		results = pool.map(process_item, my_iterable)
```
The above was using a context manager to do the equivalent of 
```python
pool = multiprocessing.Pool(processes=num_processes) 
results = pool.map(process_item, my_iterable)
pool.close() 
pool.join()
```
These two blocks are equivalent.  In either case the results is a list structure that gets automatically put together in the order of the original list

## Delayed Module
Make some notes here about the delayed module for multi core processing

## Hyperthreading
Discuss threading vs physical cores

## Windows Vs Linux (Spawn vs Fork)
When Python does multiprocessing on Linux, the memory state of the code is inherited by the child process, this is a fork.

When multiprocessing is done in Python on Windows, instead a whole new script is run to create the state.  This would create a problem, as in running the script again, the spawn is made again.  So 2 processes becomes 4, 8, 16 etc.

This is prevented by placing the spawning block at least, inside 

`if __name__ == "__main__":`

But actually the more versatile way to do this is to wrap the main part of the whole script in this, and call it.  This works well for larger projects too, where I might want to run the script as a module in some other script.

```python
import multiprocessing as mp

def some_fuction():

def some other function():
...
########____________Main Script_____________########

def main(some_argument):
	with mp.Pool(processes=num_processes) as pool: 
		results = pool.map(some_function, my_iterable)
		some_function()
	
	
	some_other_function()
return

if __name__ == "__main__"
	main(some_argument_i_want_by_default)
```
With the above function, I can put in a new argument into main() when calling from outside this script, but when simply running the script, it will run with the `some_argument_i_want_by_default`.  Also it protects against the previous problem from spawning in Windows.
## `cuda()` & GPU programming
Make some notes about the under the hood processes of cuda, & GPUs, NVIDIA toolkit, and memory management, & data transfer bottlenecks with GPU programming