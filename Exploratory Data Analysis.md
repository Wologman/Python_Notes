Summarising some useful snippets from the datacamp course '*how to win a Kaggle competition'*.  I could add to this some learnings from Ryan Holbrooks's Feature Engineering course, and datacamp's *Exploratory Data Analysis in Python*.

## Useful Stats with `describe()` & `valuecounts()`

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

### Boolean series
We may with so examine data that meets a specific condition, or set of conditions.  For this, we can create a boolean series.  The data series together with a condition statement is all that is needed. 

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

To use this series for filtering, we use the boolean series as an index for the target variable series.  Or invert it using the tilda (not).
```python
pre_term_weight = birth_weight[preterm]
full_term_weight = birthweight[~preterm]
```

### `dropna()`
Pandas DataFrames and also Series have the method `df.dropna()` This is handy when ever the need comes up to use a series without missing values.  For example, a Seaborne Violin plot. Usage like this, making a subset of a DataFrame in this case:
```python
my_cleaner_subset = df.dropna(subset=[col1, col2])
```

## Searching for data dependencies
Test hypothesis about suitable features & feature engineering transformations.  To draw a simple scatter plot with a nice style.
```python
import matplotlib.pyplot as plt

plt.style.use('ggplot')
plt.scatter(x=train['fare_amount'], y=train['distance_km'], alpha=0.5)
plt.axis([x_min, x_max, y_min, y_max]) # optional
plt.xlabel('Fare amount')
plt.ylabel('Distance, km')
plt.title('Fare amount based on the distance')
```
Or the built-in plot function, fewer options, but all we may need here
```python
plt.plot(x=train['fare_amount'], y=train['distance_km'], 'o') # o=circles
plt.axis([x_min, x_max, y_min, y_max])
plt.xlabel('Fare amount')
plt.ylabel('Distance, km')
plt.title('Fare amount based on the distance')
```
With large datasets, the points may be on top of each other, which could obscure the density characteristic.  Try to improve this by adjusting the alpha (transparency) parameter, playing with the marker (use `+` instead of an `o` for example), adjusting zoom level (axis values) & `markersize`.

Some data may be artificial discretized due to rounding issues.  For example, inches converted to cm, so points sit on top of each other when in real life they wouldn't.  Try countering this by adding a little noise (jittering).
```python
height = some_dataset['height_in_cm_from_inches']
height_jitter = height + np.random.normal(0,2, size=len(some_dataset))
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

### Probability Mass Function
Sometimes a histogram won't be granular enough as we have chosen arbitrary bins, which may hide smaller peaks, and we want to plot the value as a probability mass function.

For a single value counts for example ages in a survey 'gss'
```python
age = gss['age']
pmf = age.value_counts().sort_index() / len(age)
pmf.bar(label='Age')
plt.xlabel('Age')
plt.ylabel('PMF')
plt.show()
```

### Cumulative Distribution Function
Better cover this too for completeness.  One answer for continuous, one for discrete.


### Probability Density Function
Write this up too. 

### KDE
Write this up too

### Comparing PMF, CDF & KDE 
CDF produces a smoother distribution as it integrates out random variations that occur along the independent variable axis. It may be more effective at comparing two quite similar but noisy distributions.

- In general, use CDFs for exploration to compare to look for differences in similar but noisy distributions
- Use PMFs if there are only a few unique values
- Use KDE if there are a lot of values, or a continuous independent variable

### Box plots and Violin plots

Say we have data that has been significantly binned.  For example, looking at age vs weight, where ages are binned into 5 year groupings.  But we're interested in the changes over the whole range, thus between the groupings.  Seaborne gives us a handy method to create box plots, or do a KDE plot, over each bin.  

In either case, if the extreme values are so far from the median that small differences in median and IQR are hard to pick, it may be better to visualize the plot on a log axis by setting `plt.yscale('log').

#### Violin Plots
A KDE is run over each bin.  The width represents the density, whilst the extreme values are shown by the tip of the lines.  
```python
data = df.dropna(subset=['age','weight'])
sns.violinplot(x='age', y='weight', data=data, inner=None)
plt.xlabel('Weight in kg')
plt.ylabel('Age in years')
plt.show()
```

#### Box Plots
Whilst they look a bit less exotic than violin plots, they do show the interquartile range and median well, so in one graph depict the spread of data, and the trend between bins quite well.  The usage is similar to violin plots with Seaborne.
```python
data = df.dropna(subset=['age','weight'])
sns.boxplot(x='age', y='weight', data=data, whis=10)
plt.xlabel('Weight in kg')
plt.ylabel('Age in years')
plt.show()
```

### Correlation
Be careful referring to correlation.  If we mean Pearson's correlation coefficient r, on its own for a linear relationship this represents a combination of how noisy is the dependent variable, and how steep is the slope of the line.  Or another way, if we know one variable, how accurately can we predict the other.  With perfect linear relationships (of any slope) described by -1 or 1.

However, a low value of r, does not mean no correlation.  It may mean a non-linear correlation, or that the data has a low fitted slope compared to the spread due to noise.

To get r from a selection of columns in a DataFrame, there is a handy built-in method `corr()`
```python
subset = df[my_column_list]
print(subset.corr())
```
Produces a matrix of values from each column compared to other columns.  With 1's down the diagonal representing a column compared to its self.

### Simple linear regression
With SciPy stats module get a simple regression relationship like this:
```python
from scipy.states impot linregress
result = linregress(xs, ys)

>>>LinregressResult(slope=0.1234,
>>>				intercept = 123
>>>				rvalue = 0.75
>>>				pvalue=1.84
>>>				stderr=0.0023)
```
But on its own this tells us very little without looking at a scatter plot and fitting a slope.  We can plot a linear regression directly from the above inputs, since we know the slope and the x values.  Here is an example, of a regression line on top of a scatter plot.

```python
xs = [some_array]
ys = [some_other_array]
plt.plot(xs, ys, 'o', alpha=0.2)
fx = np.array([xs.min(), xs.max()])
fy = res.intercept + res.slope * fx

plt.plot(fx, fy, '-', alpha=0.7)
plt.xlabel('Income code')
plt.ylabel('Vegetable servings per day')
plt.ylim([0, 6])
plt.show()
```


