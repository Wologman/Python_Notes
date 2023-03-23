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