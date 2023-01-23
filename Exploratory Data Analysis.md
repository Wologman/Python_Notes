Summarising some useful snippets from the datacamp course '*how to win a Kaggle competition'*.  I could add to this some learnings from Ryan Holbrooks's feature engineering course, and datacamp's *Exploratory Data Analysis in Python*.

## Useful Stats with `.describe()` & `.valuecounts()`

With a pandas dataframe, get a summary of a variables stats

```Python
MyDataFrame.MyColumn.describe()

>>> count 20000.000 
>>> mean 11.303 
>>> std 9.542 
>>> min -3.000 
>>> 25% 6.000 
>>> 50% 8.500 
>>> 75% 12.500 
>>> max 180.000
```

`valuecounts()` is an easy way to get some indication of the frequency of different values, if the dataset has a modest number of discrete values

```python
MyDataFrame.MyColumn.value_counts()
```

## Searching for data dependencies

Test hypothesis about suitable features & feature engineering transformations.  To draw a simple scatter plot with a nice style.

```python
import matplotlib.pyplot as plt

plt.style.use('ggplot')
plt.scatter(x=train['fare_amount'], y=train['distance_km'], alpha=0.5)
plt.xlabel('Fare amount')
plt.ylabel('Distance, km')
plt.title('Fare amount based on the distance')
```

Group some continuous time series data by hour, and make a line plot.

```Python
# Create hour feature
train['pickup_datetime'] = pd.to_datetime(train.pickup_datetime)
train['hour'] = train.pickup_datetime.dt.hour

# Find median fare_amount for each hour
hour_price = train.groupby('hour', as_index=False)['fare_amount'].median()

# Plot the line plot
plt.plot(hour_price['hour'], hour_price['fare_amount'], marker='o')
plt.xlabel('Hour of the day')
plt.ylabel('Median fare amount')
plt.title('Fare amount based on day time')
plt.xticks(range(24))
plt.show()
```


## datacamp EDA Course notes

### `.replace()`
To replace unwanted values in-place use `.replace()` For example say a column of baby weights 'pounds' was encoded to use 98 & 99 lbs for missing values, but you want to get data about the mean, these values could be replaced by `nan`
```python
pounds = pounds.replace([98, 99], np.nan)
```

### Histograms
To look at the distribution of a variable visually, in this case birth_weight, whilst dropping any `NaN` values:

```python
import matplotlib.pyplot as plt
plt.hist(birth_weight.dropna(), bins=30)
plt.xlabel('Age at conception')
plt.ylabel('Number of pregnancies')
plt.show()
```

### Boolean series
We may with so examine data that meets a specific condition, or set of conditions.  For this we can create a boolean series.  The data series together with a condition statement is all that is needed. 

```python
preterm = nsfg['prglngth'] < 37   #premature bables from a dataset of birth data
preterm.head()0 

>>> False 
>>> True 
>>> True 
>>> True 
>>> False
>>> Name: prglngth, dtype: bool
```
Since python treats Boolean values as 1s and 0s for arithmetic operations, we can generate stats directly.  For the above `preterm.sum()` => 3742.  The number of premature babies in the dataset.  `preterm.mean()` => 0.39987 The proportion of pregnancies defined as premature.

To use this series for filtering, we use the boolean series as an index for the target variable series.  Or invert it using the Tilda (not).
```python
pre_term_weight = birth_weight[preterm]
full_term_weight = birthweight[~preterm]
```



