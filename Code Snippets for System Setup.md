To check where the python interpreter is located

```python
import sys
print(sys.executable)
```

To get a list of the python paths

```Python
import sys  
import pandas as pd  
paths = sys.path  
df = pd.DataFrame({'paths':paths})  
df.to_csv('./qgis_sys_paths.csv', index=False)
```

Or just to print them:

```Python
import sys  
print(sys.path)
```