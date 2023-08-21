## `environment.yaml` & `requirements.txt`

Having one of these two files are always going to be the key to reproducible work.  But if it's something I create myself for myself,  `environment.yaml` is the better approach.

This is because it includes the Python version, and Conda channels.  A different version may be needed between Linux and Windows, as specific versions in specific channels are for specific operating systems, and the collections on each channel are different.

But working with both is also important, to use other people's code, or make mine useable to others.

## `environment.yaml`

This is the general form for an `environment.yaml` file:

```yaml
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
      # and for wheels
      - http://www.lfd.uci.edu/some_interesting_wheel-win_amd64.whl
```

And usage, from the same directory: 
`conda env create -f environment.yaml`

## Set up a python environment with `requirements.txt`

To replicate a Kaggle kernel for example.
conda create --name kaggle23 python=3.9
conda activate kaggle23
pip install requirements.txt

And just hope for the best.
## Converting `requirements.txt` to `environment.yml`
This can be done by hand, but be warned that a bunch of dependencies may throw up an error if they can't be found on the specified channels.  May need to be added via PIP at the end of the file.

## Create an `environment.yml` from an existing environment

From the directory you want the file:
```bash
conda env export > environment.yml
```
#### For future use with Conda

`conda list -e > requirements.txt`

Can be used to create a `conda` compatible virtual environment with

`conda create --name <env> --file requirements.txt`

If you want a file which you can use to create a `pip` virtual environment (i.e. a `requirements.txt` in the right format) you can install `pip` within the `conda` environment, then use pip to create `requirements.txt`.
#### For future use with Venv and PIP3

```python
conda activate <env>
conda install pip
pip freeze > requirements.txt
```

Then use the resulting `requirements.txt` to create a PIP virtual environment:

```python
python3 -m venv env
source env/bin/activate
pip install -r requirements.txt
```

### Setting up with with Shells Scripts

Since I do this a lot, it's something that would really be worth automating with `.bash` for Linux and also `.ps1` for Windows.

