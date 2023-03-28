https://realpython.com/python-pathlib/

To get the parent directory of where the python file is sitting

```python
from pathlib import Path
project_directory = Path.cwd().parent
```

Here is a handy way to use pathlib's build in recursive glob method, to get a list of files of a given type from a directory.

```python
file_names = [f.name for f in Path(folder_pth).rglob('*.ogg')]
```

## Useful `Path` Object Methods

```python
.parent  # the whole path object to the parent
.parent.name  # only the parent folder name
.stem
.name
.suffix
```

## Filepath setup

You can't mix a string constructor like `+` with filepath constructors `/`, but you can add a string to a filepath object.  So an elegent way to build a filepath is to use f strings.

For example say we're opening a file from one path, stripping the file type and adding a new ending.

```python 
fp = Path('/home/olly/Documents/Data Science/My_Project/Data/ClassD/hello.ogg')
data_fldr = Path('/home/olly/Documents/Data Science/Some_Project/Data')
out_path = data_fldr / f'{str(fp.parent.name)}/{str(fp.stem)}.wav'
```


## `OS` Methods

```python
os.makedirs(path,  exist_ok=True)
os.listdir(some_parent_directory) # but better to use path_object.glob()
```