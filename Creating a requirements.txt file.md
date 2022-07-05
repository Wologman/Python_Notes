Suppose a project is completed and it's time to replecate it elsewhere, or clone to GitHub:

### To use in future with Conda

`conda list -e > requirements.txt`

Can be used to create a `conda` compatible virtual environment with

`conda create --name <env> --file requirements.txt`


If you want a file which you can use to create a `pip` virtual environment (i.e. a `requirements.txt` in the right format) you can install `pip` within the `conda` environment, then use pip to create `requirements.txt`.


### To use in future with Venv and  PIP3

```python
conda activate <env>
conda install pip
pip freeze > requirements.txt
```

Then use the resulting `requirements.txt` to create a `pip` virtual environment:

```python
python3 -m venv env
source env/bin/activate
pip install -r requirements.txt
```