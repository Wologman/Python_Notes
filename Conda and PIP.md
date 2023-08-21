Conda and PIP have some important differences that should be understood.
[Conda Cheat Sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)
## Differences between Conda and PIP

[One explanation](https://pythonspeed.com/articles/conda-vs-pip/)
[An explanation from anaconda.com](https://www.anaconda.com/blog/understanding-conda-and-pip)

A summary:

|    |pip   |conda   | 
|---|---|---|
| Installs Python | No  | As Package  |  
| 3rd-party shared libraries | Inside a wheel   | As Package  |  
| Executables and tools| As Package  | As Package  |  
| Environment Management | No, requires 3rd party systems like venv | Yes |
| Code Repository   |  PyPI  (150,000 + packages) |  Anaconda (Default, most stable, 1500 packages) + others, conda-forge being the largest and most up to date |
| Dependency checking  |  Later packages can break earlier ones |  Yes |
|Can require compilers |  No  | Yes  |
|Manages   |   Binaries   |   Wheels or source code  | 
| Package Types   |   Python only   |   Any (rust, C etc.) |

For general programming, using PIP, into a `venv` is probably fine.  But for scientific computing and data science where reproducibility is the key, Conda has some significant advantages.

- The binaries (executable files that are already compiled and ready to run) have been compiled consistently from a centralised repository.
- Consistency between operating systems, there is no dependence on a Python version, or software coming from the operating system its self.
- The dependency checking.  This is a double-edged sword, it makes it harder to find a workable combination of packages, but once found there should be no unexpected behaviors from broken dependency chains.

I've found this quite painful in the past.  But I could avoid all this by building myself a little collection of `.yaml files`, keeping them on my SSD, and setting up my Conda environments from them every time.  This would make life much easier!  Or go one step further and write `.bash` & `.ps1` scripts to set them up all in one go on any new operating system.

In the future, when working on a project, it would be good to produce both a `requirements.txt`, and an `environment.yaml` file, so anybody can use the code.  But for myself, stick to the `environment.yaml`.

## Conda basics
```bash
conda update conda
conda create --name my_test_environment python=3.10
conda activate my_test_environment
conda install some_package
conda list
conda deactivate my_test_environment
conda env list
conda env remove --name my_test_environment
```

Upgrade the solver to libmamba

```bash
conda update -n base conda
conda install -n base conda-libmamba-solver
conda config --set solver libmamba
```


## Using Conda together with PIP

[Using them together](https://www.anaconda.com/blog/using-pip-in-a-conda-environment)
It is generally asking for trouble to use Conda and PIP together.  If necessary at all, preferably do the PIP packages last, to reduce the chance of conflicts.

If a package is only available on PIP, it can still be included in the `environment.yaml` file as such.  This makes the dependency PIP its self, and the packages are dependencies of PIP.

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
conda env create -f environment.yaml
```
That's it!

## Switching between windows and Linux

It's possible that the package specified in the `environment.yaml` was set up on an operating system that included packages not available to the other one.  Go to [anaconda.org](https://anaconda.org) and check the right-hand column, where possible OS for each package is listed.  To be really thorough, it would be best to check the `environment.yaml` works on both OS.  Or at a bare minimum, make a note of which OS I used to create it, so the problem is understood later!

This highlights the advantage/disadvantage of PIP.  Since this is compiled from source for the operating system in use.  So more likely to work, but not guarantied to be quite the same behaviour.



