[pandas cheat sheet](https://pandas.pydata.org/Pandas_Cheat_Sheet.pdf)

For truly massive datasets Pandas might not be the thing, consider trying more performance focussed alternatives like Polars, Dask or Vaex, or working directly with NumPy arrays.  But for everything else, Pandas is awesome.

Pandas is built on top of NumPy so wherever possible do operations with whole columns rather than iterating down them, so the operations can be vectorised.

If working with an individual column or row, it is good practice to assign it to a variable.
```python
my_column = df['my_col_header']  
```
`df.my_col_header` could also work since the columns are attributes of the DataFrame class, but not in all situations, for example the col header has a space in it.  So I'm going to stick with this syntax for consistency.

## Really Common operations 
To subset based on a Boolean series.  Remove all rows based on some field equaling some value:
`df = df[df['column_name'] == some_value]`

## Efficient memory and speed considerations
### Choice of datatypes
It's good practice to specify the datatype when loading, and avoid using mixed datatypes in a particular field.

### For big dataframes, it's time to stop using csv for storing everything!
CSV stores all data as text.  It's slow to load and unload, and wastes memory in most cases as there is no efficiency gain from choosing optimal Pandas datatypes.  

**Pickle**
Pickle is Python specific, for storage of any python object, including dataframes. 
```python
import pickle
with open('path/to/df.pkl', 'wb') as f:
    pickle.dump(df, f)
```
and to re-load later
```python
with open('path/to/df.pkl', 'rb') as f:
    df = pickle.load(f)
```

**Parquet**
Pandas has native support for parquet, so long as the pyarrow library is also installed (`conda install pyarrow`) , so it's super easy.
To save:   `df.to_parquet('file_name.parquet')`
To load:   `df = pd.read_parquet('file_name.parquet')`

## Iterating by row
Generally not the point of Pandas, but if it's really necessary then try to do it efficiently with `iterrows` or `itertuples`.

#### `df.interrows()`
This is more efficient than using an index with `iloc()`, and returns a row object that can be referenced by the column name.
```python
for i,row in baseball_df.iterrows():
	wins = row['W']
	games_played = row['G']
```

#### `df.intertuples()`
Faster than `df.interrows()` and it returns a special pandas datatype in the form of a named tuple.  The values can no be accessed as attribute values with attribute syntax.

```python
for row_tupple in baseball_df.itertuples():
	wins = row_tuple.W
	games_played = row_tuple.G
```

## Column arithmetic
Simple arithmetic between specified columns can be accomplished easily by directly using `+-/*` to add a new column in-place.
```python
df[new_column] = df['Col_A'] + df['Col_B']
```

## Builtin Pandas operators
Pandas comes with some convenient built in operators for element-wise operations.  Don't expect them to be the fastest solution as they will involve Pandas overhead, but they are readable and better than doing loops.

`add()`, `sub()`, `mul()`, `div()`, `floordiv()`, `mod()`, `pow()` Can operate column or row wise by setting the axis argument.

- These are quite flexible, and have the syntax `operator(value, axis, level, fill_value)`
- Only the value is required, if all else empty this value will apply to the whole DataFrame.  
- The value can be a single or multiple element like thing.
- By default `axis='columns', level=None, fill_value=None`

`.sum(axis=None)` Returns the sum of all the values on a requested axis.  If only wanting to do this with some columns or rows, then slice the DataFrame first then apply.

## Operating on the underlying NumPy arrays
by using the `df.values` attribute a NumPy array of the specified portion of the DataFrame can be obtained, then all the awesome efficiencies of doing vectorised operations and functions on NumPy arrays can be accessed.  This will generally be the fastest method possible.  

```python
baseball_df['RS'].values - baseball_df['RA'].values
baseball_df['RD'] = run_diffs_np
```

### df.apply()
`df.apply()` is analogous to the Python built-in `map()`. It can be used to apply functions, or lambda functions, on each row.  Expect this to be slower working directly with the NumPy arrays but more flexible.  For example I could do some fancy string operations.

```python
def calc_run_diff(runs_scored, runs_allowed):
	run_diff = runs_scored - runs_allowed
return run_diff

run_diffs_apply = baseball_df.apply(
lambda row: calc_run_diff(row['RS'], row['RA']), axis=1)
baseball_df['RD'] = run_diffs_apply
```
