## Hyperthreading vs Multiprocessing
#### Multiprocessing
A process is an independent program, with it's own memory space, file handlers and system resources.  Processes are isolated from each other and run asynchronously without communicating with other processes.  Multi-processing is ideal for computationally intensive CPU sequences that can be run independently, but in some cases the overhead of setting them up will outweigh the performance boost.
#### Hyperthreading (Multi-threading)
A thread is a single sequence of instructions within a process that can be scheduled by the operating system.  Threads share the same memory space and other resources of the process, allowing for efficient coordination between threads.  Threads can be run in parallel, and be scheduled by the system across cores as resources are available.

Hyperthreading is likely to be useful for I/O bound tasks where there is CPU processing to be performed, but a substantial amount of time is spent waiting for slower process, like reading and cropping some images from from a HDD for example.  
## Windows Vs Linux (Spawn vs fork)
When Python does multiprocessing on Linux, the memory state of the code is inherited by the child process, this is a fork.

When multiprocessing is done with built-in Python `multiprocessing` module on Windows, using a whole new instance script is run to create the state.  This could create a problem, as in running the script again, the spawn is made again.  So 2 processes becomes 4, 8, 16 etc.

This is prevented by placing the spawning block at least, inside a protecting block:
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
With the above function, I can put in a new argument into `main()` when calling from outside this script, but when simply running the script directly , it will run with the `some_argument_i_want_by_default`.  Also it protects against the previous problem from spawning in Windows.

Just be careful with passing variables to the function, since main only gets run once, if the function relies on variables external variables for its arguments it won't find those in the child spawns.  Variables needed by the function need to be created outside the protection block so they get calculated in the spawned script.
## `cuda()` & GPU programming
TODO - Make some notes about the under the hood processes of Cuda, GPUs, the NVIDIA toolkit, memory management, & data transfer bottlenecks with GPU programming.

## `multiprocessing` Module

This is the built-in module for doing multi core processing.  Just be careful on windows to protect the function by a `if __name__ == "__main__":` block, so that it doesn't go propagating its self in the spawned script.

This is how to process a simple list example, using the maximum physical cores, for some custom function `process_item`:
```python
import multiprocessing as mp 
some_value = 10
def process_item(some_parameter=some_value, some_item):
	
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
These two blocks are equivalent.  In either case the results is a list structure that gets automatically put together in the order of the original list.  To pass parameters that don't need iteration they can be set up as named arguments in the function definition
## `concurrent` Module
Using `concurent.futures.TreadPoolExtractor` is the built-in way to do multi-threading, and doesn't cause windows to spawn a new instance of the whole script.

Here is a basic example, using a context manager, with a progress bar included manually updating each iteration, from an example where I was building a set.  Could just as easily have been building a list or a dictionary:
```Python
import tqdm as tqdm
import concurrent.futures
subdirs = [entry for entry in root_dir.iterdir() if entry.is_dir()]
overall_progress = tqdm(total=len(subdirs), desc="Overall Progress")
filenames = Set() 
processed_directories = 0
with concurrent.futures.ThreadPoolExecutor(max_workers=8) as executor:
        for result in executor.map(search_for_jpgs, subdirs):
            filenames.update(result)
            processed_dirs += 1
            overall_progress.update(1)
overall_progress.close()
print(f'{processed_dirs} directories were processed.')
```

In this example `subdirs` was an iterable (a list of subdirectories) and the function would take one of its items as an input.

```Python
def search_for_jpgs(folder):
	return {path for path in folder.rglob('*.[jJ][pP][gG]')}
```
## `delayed` & `Parallel` Modules from `Joblib`
The delayed module provides some slightly cleaner ways to do parallel processing, especially for the case of multiprocessing on Windows.  `Parallel` is built on `multiprocessing` but has some potential advantage in memory use, as it is combined with a generator function with `delayed`.  

Parallel runs multi-processing by default, but to run as muti-threaded specify `prefer='threads'`  Any concerns over protecting from spawning are abstracted away.  So the example below should work with the same syntax on Linux & Windows.

To run multiprocessing with delayed:
```python
from joblib import Parallel, delayed

some_number = 10
def my_process(item_to_process, keword_arg=some_number):
	return keyword_arg + item_to_process

results = Parallel(n_jobs=-1)(delayed(my_process)(item) for item in items)
```
What's happening here is that `delayed(my_process)(item)` is acting as a generator function. 

`Results` here is a list, with each item in the order of the original iterable.  For other output data structures it is probably easiest to flatten or extract from the list afterwards.  For example below would produce a dictionary, by turning the list of tuples that the function returns directly into a dictionary with the `dict()` method. 
```python
some_number = 10
def my_process(item_to_process, keword_arg=some_number):
	Do some stuff
	return key, value

results = dict(Parallel(n_jobs=-1)(delayed(my_process)(item) for item in items))
```
Or  a list of lists could be flattened with a comprehension. 
`flat_list = [item for sublist in nested_list for item in sublist]`

In these examples -1 is a shorthand way to say use all the processors available.  To change from parallel processing to hyperthreading, add`backend='threading'` to the `Parallel` argument.  There is also a verbose argument, or `tdqm()` could be used on items

Note the interesting syntax here.  This is *method chaining*, which doesn't come up so much in Python, but seems common for parallel processing libraries.  The left most function returns a function, that takes the next set of arguments, and so on.

