Summarising some useful snippets from the datacamp course *Exploratory Data Analysis in Python*.  I could add to this some learning from Ryan Holbrooks's Feature Engineering course, and datacamp's  '*how to win a Kaggle competition'*
## Useful Stats with `describe()` & `value_counts()`

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

To actually subset by some condition based on value counts (in this case rareness):
```python
mask = df['class_label'].map(df['class_label'].value_counts()) < THRESHOLD
rare_df = df[mask]
rare_df.class_label.value_counts()
```

### `groupby()`
If I have discrete bins or datapoints, with lots of values just want to see the trend for the mean of each, `groupby` could be used.  It produces a `groupby` object that has useful methods much like pandas series, including `mean()`. For example:
```python
grouped = df.groupby('age')
mean_something_by_age = grouped['something'].mean()
```
### `dropna()`
Pandas DataFrames and also Series have the method `df.dropna()` This is handy when ever the need comes up to use a series without missing values.  For example, a Seaborne Violin plot. Usage like this, making a subset of a DataFrame in this case:
```python
my_cleaner_subset = df.dropna(subset=[col1, col2])
```

### `replace()`
To replace unwanted values in-place use `.replace()` For example say a column of baby weights 'pounds' was encoded to use 98 & 99 lbs for missing values, but you want to get data about the mean, these values could be replaced by `nan`
```python
pounds = pounds.replace([98, 99], np.nan)
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
from scipy.stats import linregress
result = linregress(xs, ys)

>>>LinregressResult(slope=0.1234 intercept = 123 rvalue = 0.75 pvalue=1.84 stderr=0.0023)
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

### Multiple linear regression
We could do this with scikit-learn, but the syntax below is using statsmodels.  Where the data shows non-linear dependencies, we could also create nonlinear features at the DataFrame level, and fifth those using a multiple regression.  For example,
`df[age_2] = df['age']**2` would create a feature based on the square of the 'age' values.

```python
import statsmodels.formula.api as smf

# Add a new column with educ squared
gss['age2'] = gss['age']**2
gss['educ2'] = gss['educ']**2

# Run a regression model with educ, educ2, age, and age2
model = smf.ols('realinc ~ educ + educ2 + age + age2 ', data=gss)
results = model.fit()

# Print the estimated parameters
print(results.params)

>>>Intercept -23241.884 educ -528.309 educ2 159.967 age 1696.717 age2 -17.197 dtype: float64
```
To add more features, to the model just add more columns with the + operator

The slope coefficient values tell us some idea of how influential each feature is, but we don't get a sense of how well, if at all this model fits the data.  For that, and to visualise what is going on, try holding some values constant and plotting some predictions, comparing against relevant data points.

```python
# Run a regression model with educ, educ2, age, and age2
results = smf.ols('realinc ~ educ + educ2 + age + age2', data=gss).fit()

# Make the DataFrame
df = pd.DataFrame()
df['educ'] = np.linspace(0,20)
df['age'] = 30
df['educ2'] = df['educ']**2
df['age2'] = df['age']**2

# Generate and plot the predictions
pred = results.predict(df)
print(pred.head())

>>> 0 12182.345 
>>> 1 11993.359 
>>> 2 11857.672 
>>> 3 11775.286 
>>> 4 11746.199 
>>> dtype: float64
>>
# Plot mean income in each age group
plt.clf()
grouped = gss.groupby('educ')
mean_income_by_educ = grouped['realinc'].mean()
plt.plot(mean_income_by_educ, 'o', alpha = 0.5)

# Plot the predictions
pred = results.predict(df)
plt.plot(df['educ'], pred, label='Age 30')
plt.xlabel('Education (years)')
plt.ylabel('Income (1986 $)')
plt.legend()
plt.show()
```
In the above example, the predicted parabola like fit matches well to all the data samples of age = 30.

If we want to control for a categorical variable, for example in the above sex, Male is encoded to 1 in the DataFrame, female is encoded to 2.  By adding a `C(sex)` term into the model, we get the model controlled for sex, with a term showing the difference for T.2.
```python
results = smf.ols('realinc ~ educ + educ2 + age + age2 + C(sex)', data=gss).fit()
# And later something like 
>>> Intercept -23241.884 
>>> C(sex)[T.2] -4156.114
>>> educ -528.309 
>>> educ2 159.967 
>>> age 1696.717 
>>> age2 -17.197 
>>> dtype: float64
```
The above indicates a difference of $4,156 controlling for the sex variable.

### Logistic Regression
Build a model with categorical variables, or a mix of categorical and numerical variables, we could use the logistic regression class in statsmodels, (or from Scikit-Learn).  The syntax is much the same, but note that we may need to change the encoding of categorical binaries to 0 & 1.

This example is predicting a categorical 'grass'  (likelihood of supporting a marijuana law) based on some numerical and one categorical (encoded to 0 & 1 already):
```python
# Recode grass
gss['grass'].replace(2, 0, inplace=True)
# Run logistic regression
results = smf.logit('grass ~ age + age2 + educ + educ2 + C(sex)', data=gss).fit()
results.params

>>> Intercept -1.685e+00 
>>> C(sex)[T.2] -3.846e-01 
>>> age -3.476e-02 
>>> age2 1.917e-04 
>>> educ 2.219e-01 
>>> educ2 -4.163e-03 
>>> dtype: float64
```

Again, it is useful to hold some variables constant, and plot the predictions to visualise the model.