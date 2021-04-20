```python
"""
Dataset contains min and max weather temperatures for certain days. 
See desirable output in figure.
"""
import datetime

import pandas as pd

# Tips:
# use datetime.datetime
# use pivot_table in the end
```

![to obtain](weather_final.png)


```python
df = pd.read_csv("weather-raw.csv")
df.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>year</th>
      <th>month</th>
      <th>element</th>
      <th>d1</th>
      <th>d2</th>
      <th>d3</th>
      <th>d4</th>
      <th>d5</th>
      <th>d6</th>
      <th>d7</th>
      <th>d8</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MX17004</td>
      <td>2010</td>
      <td>1</td>
      <td>tmax</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>MX17004</td>
      <td>2010</td>
      <td>1</td>
      <td>tmin</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.id.unique()
```




    array(['MX17004', 'MX17004 '], dtype=object)




```python
df['id'] = df['id'].str.strip()
```


```python
df = df.melt(id_vars=['id', 'year', 'month', 'element'])
df.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>year</th>
      <th>month</th>
      <th>element</th>
      <th>variable</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MX17004</td>
      <td>2010</td>
      <td>1</td>
      <td>tmax</td>
      <td>d1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>MX17004</td>
      <td>2010</td>
      <td>1</td>
      <td>tmin</td>
      <td>d1</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['date'] = df['year'].astype('str') + '-' + df['month'].astype(
    'str') + '-' + df['variable'].str.replace('d', '')
```


```python
df['date'] = pd.to_datetime(df['date'])
```


```python
df = pd.pivot_table(df,
                    values=['value'],
                    columns=['element'],
                    index=['id', 'date'])
```


```python
df = df.droplevel(level=0, axis=1)
```


```python
df['tmin'] = df['tmin'].ffill().dropna()
```


```python
df['tmax'] = df['tmax'].bfill()
```


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>element</th>
      <th>tmax</th>
      <th>tmin</th>
    </tr>
    <tr>
      <th>id</th>
      <th>date</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="3" valign="top">MX17004</th>
      <th>2010-02-02</th>
      <td>27.3</td>
      <td>14.4</td>
    </tr>
    <tr>
      <th>2010-02-03</th>
      <td>24.1</td>
      <td>14.4</td>
    </tr>
    <tr>
      <th>2010-03-05</th>
      <td>32.1</td>
      <td>14.2</td>
    </tr>
  </tbody>
</table>
</div>


