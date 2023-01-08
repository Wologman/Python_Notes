summarising some useful snippets from the datacamp course 'how to win a kaggle competition'.  I could add to this some learnings from Ryan Holbrooks's feature engineering course.

## Useful Snippets

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

Also 

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
