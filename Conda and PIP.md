Conda and PIP have some important differences that should be understood.

## VSCode on Windows

To make Conda commands through the shell built into VSCode on Windows you need to launch the editor through the anaconda terminal!!  

## Differences between Conda and PIP

[One explanation](https://pythonspeed.com/articles/conda-vs-pip/)
[An explanation from anaconda.com](https://www.anaconda.com/blog/understanding-conda-and-pip)

A summary
|   |pip   |conda   | 
|---|---|---|
| Installs Python | No  | As Package  |  
| 3rd-party shared libraries | Inside a wheel   | As Package  |  
| Executables and tools| As Package  | As Package  |  
| Environment Management | No, requires 3rd party systems like venv | Yes |
| Code Repository   |  PyPI  (150,000 + packages) |  Anaconda (Default, most stable, 1500 packages) + others, conda-forge being the largest and most up to date |
| Dependency checking  |  Later packages can break earlier ones |  Yes |
|Can require compilers |  no  | yes  |
|Manages   |   binaries   |   wheels or source code  | 
| Package Types   |   Python only   |   Any (rust, C etc.) |

For general programming, using pip, into a `venv` is probably fine.  But for scientific computing and data science where reproducibility is the key, Conda has some significant advantages.

- The binaries (executable files that are already compiled and ready to run) have been compiled consistently from a centralised repository.
- Consistency between operating systems, there is no dependence on a Python version, or software coming from the operating system its self.
- The dependency checking.  This is a double-edged sword, it makes it harder to find a workable combination of packages, but once found there should be no unexpected behaviors from broken dependency chains.

I've found this quite painful in the past.  But I could avoid all this by building myself a little collection of .yml files, keeping them on my SSD, and setting up my Conda environments from them every time.  This would make life much easier!

In the future, when working on a project, it would be good to produce both a requirements.txt, and an environment.yml file, so anybody can use the code.  But for myself, stick to the environment.yml approach.

## Converting requirements.txt to environment.yml
This can be done by hand, but be warned that a bunch of dependencies may throw up an error if they can't be found on the specified channels.  May need to be added via PIP at the end of the file.

## Create an environment.yml from an existing environment

From the directory you want the file:
```bash
conda env export > environment.yml
```


## Using Conda together with PIP

[Using them together](https://www.anaconda.com/blog/using-pip-in-a-conda-environment)
It is generally asking for trouble to use Conda and PIP together.  If necessary at all, preferably do the PIP packages last, to reduce the chance of conflicts.

If a package is only available on pip, it can still be included in the environment.yml file as such.  This makes the dependency PIP its self, and the packages are dependencies of PIP.

```xml
name: myenv
channels:
  - conda-forge
dependencies:
  - python=3.9
  - numpy
  - pandas
  - geopandas
  - pip:
      # Packages that are only on PyPi
      - torch
      - opencv-python
```

`pip install my_package` Directly into a Conda environment also works, but with the same issue as above.  Try to get out of the habit of working without a recorded package list.

### To create the new python environment

From a shell terminal in the directory containing `environment.yml`
```shell
conda env create -f environment.yml
```
That's it!

## Switching between windows and Linux

It's possible that the package specified in the `environment.yml` was set up on an operating system that included packages not available to the other one.  Go to [anaconda.org](https://anaconda.org) and check the right-hand column, where possible OS for each package is listed.  To be really thorough, it would be best to check the environment.yml works on both OS.  Or at a bare minimum, make a note of which OS I used to create it, so the problem is understood later!

This highlights the advantage/disadvantage of PIP.  Since this is compiled from source for the operating system in use.  So more likely to work, but not guarenteed to be quite the same behaviour.