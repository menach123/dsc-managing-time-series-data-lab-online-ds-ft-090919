
# Managing Time Series Data - Lab

## Introduction

In the previous lecture, you learned that time series data are everywhere and understanding time series data is an important skill for data scientists!

In this lab, you'll practice your previously learned techniques to import/load, clean and manipulate time series data.

The lab will cover how to perform time series analysis while working with large datasets. The dataset can be memory intensive so your computer will need at least 2GB of memory to perform some of the calculations.


## Objectives

You will be able to:

* Load time series data using Pandas and perform time series indexing
* Perform index based slicing to create subsets of a time series
* Change the granularity of a time series 
* Perform basic data cleaning operations on time series data

## Let's get started!

We will start the lab by loading the required libraries 

* `pandas` for data wrangling and manipulations  
* `matplotlib` for visualizing time series data 
* `statsmodels` primarily for bundled datasets 


```python
# Load required libraries
import pandas as pd
import matplotlib.pyplot as plt
import statsmodels as sm

```

## Loading time series data
The `StatsModels` library comes bundled with built-in datasets for experimentation and practice. A detailed description of these datasets can be found [here](http://www.statsmodels.org/dev/datasets/index.html). Using `StatsModels`, the time series datasets can be loaded straight into memory. 

In this lab, we'll use the **"Atmospheric CO2 from Continuous Air Samples at Mauna Loa Observatory, Hawaii, U.S.A."**, containing CO2 samples from March 1958 to December 2001. Further details on this dataset are available [here](http://www.statsmodels.org/dev/datasets/generated/co2.html).

We can bring in this data using the `load()` method, which will allow us to read this data into a pandas dataframe by using `dataset["data"]` and setting the index to the time series data.  


```python
# Load the "co2" dataset from sm.datasets
data_set = sm.datasets.co2.load()

# load in the data_set into pandas data_frame
CO2 = pd.DataFrame(data=data_set["data"])
CO2.rename(columns={"index": "date"}, inplace=True)

# set index to date column

CO2.set_index('date', inplace=True)
CO2.index = pd.to_datetime(CO2.index.astype(str))
CO2.head()
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
      <th>co2</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1958-03-29</th>
      <td>316.1</td>
    </tr>
    <tr>
      <th>1958-04-05</th>
      <td>317.3</td>
    </tr>
    <tr>
      <th>1958-04-12</th>
      <td>317.6</td>
    </tr>
    <tr>
      <th>1958-04-19</th>
      <td>317.5</td>
    </tr>
    <tr>
      <th>1958-04-26</th>
      <td>316.4</td>
    </tr>
  </tbody>
</table>
</div>



Let's check the type of CO2 and also first 15 entries of CO2 dataframe as our first exploratory step.


```python
# Print the datatype of CO2 and check first 15 values
type(CO2)

# datatype of CO2 is <class 'pandas.core.frame.DataFrame'>

CO2[:15]
#               co2
# 1958-03-29  316.1
# 1958-04-05  317.3
# 1958-04-12  317.6
# 1958-04-19  317.5
# 1958-04-26  316.4
# 1958-05-03  316.9
# 1958-05-10    NaN
# 1958-05-17  317.5
# 1958-05-24  317.9
# 1958-05-31    NaN
# 1958-06-07    NaN
# 1958-06-14    NaN
# 1958-06-21    NaN
# 1958-06-28    NaN
# 1958-07-05  315.8
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
      <th>co2</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1958-03-29</th>
      <td>316.1</td>
    </tr>
    <tr>
      <th>1958-04-05</th>
      <td>317.3</td>
    </tr>
    <tr>
      <th>1958-04-12</th>
      <td>317.6</td>
    </tr>
    <tr>
      <th>1958-04-19</th>
      <td>317.5</td>
    </tr>
    <tr>
      <th>1958-04-26</th>
      <td>316.4</td>
    </tr>
    <tr>
      <th>1958-05-03</th>
      <td>316.9</td>
    </tr>
    <tr>
      <th>1958-05-10</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1958-05-17</th>
      <td>317.5</td>
    </tr>
    <tr>
      <th>1958-05-24</th>
      <td>317.9</td>
    </tr>
    <tr>
      <th>1958-05-31</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1958-06-07</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1958-06-14</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1958-06-21</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1958-06-28</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1958-07-05</th>
      <td>315.8</td>
    </tr>
  </tbody>
</table>
</div>



With all the required packages imported and the CO2 dataset as a Dataframe ready to go, we can move on to indexing our data.

## Data Indexing

You may have noticed that by default, the dates have been set as the index of our pandas DataFrame. While working with time series data in Python, it's important to always ensure that dates are used as index values and are set as a `timestamp` object. Timestamp is the pandas equivalent of python’s `Datetime` and is interchangeable with it in most cases. It's the type used for the entries that make up a `DatetimeIndex`, and other time series oriented data structures in pandas. Further details can be found [here](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Timestamp.html).

We can confirm these assumption in python by checking index values of a pandas dataframe with `DataFrame.index`. 


```python
# Confirm that date values are used for indexing purpose in the CO2 dataset 
CO2.index
# DatetimeIndex(['1958-03-29', '1958-04-05', '1958-04-12', '1958-04-19',
#                '1958-04-26', '1958-05-03', '1958-05-10', '1958-05-17',
#                '1958-05-24', '1958-05-31',
#                ...
#                '2001-10-27', '2001-11-03', '2001-11-10', '2001-11-17',
#                '2001-11-24', '2001-12-01', '2001-12-08', '2001-12-15',
#                '2001-12-22', '2001-12-29'],
#               dtype='datetime64[ns]', length=2284, freq='W-SAT')
```




    DatetimeIndex(['1958-03-29', '1958-04-05', '1958-04-12', '1958-04-19',
                   '1958-04-26', '1958-05-03', '1958-05-10', '1958-05-17',
                   '1958-05-24', '1958-05-31',
                   ...
                   '2001-10-27', '2001-11-03', '2001-11-10', '2001-11-17',
                   '2001-11-24', '2001-12-01', '2001-12-08', '2001-12-15',
                   '2001-12-22', '2001-12-29'],
                  dtype='datetime64[ns]', name='date', length=2284, freq=None)



The output above shows that our dataset clearly fulfills the indexing requirements. Look at the last line:


### **dtype='datetime64[ns]', length=2284, freq='W-SAT'**


* `dtype=datetime[ns]` field confirms that the index is made of timestamp objects.
* `length=2284` shows the total number of entries in our time series data.
* `freq='W-SAT'` tells us that we have 2,284 weekly (W) date stamps starting on Saturdays (SAT).

## Resampling

Remember that depending on the nature of analytical question, the resolution of timestamps can also be changed to other frequencies. For this data set we can resample to monthly CO2 consumption values. This can be obtained by using the `resample() function`. Let's

* Group the time-series into buckets representing 1 month using `resample()` function.
* Apply a `mean()`function on each group (i.e. get monthly average).
* Combine the result as one row per monthly group.


```python
# Group the timeseries into monthly buckets
# Take the mean of each group 
# get the first 10 elements of resulting timeseries

CO2_monthly= CO2['co2'].resample('MS')
CO2_monthly_mean = CO2_monthly.mean()

CO2_monthly_mean.head(10)

# 1958-03-01    316.100000
# 1958-04-01    317.200000
# 1958-05-01    317.433333
# 1958-06-01           NaN
# 1958-07-01    315.625000
# 1958-08-01    314.950000
# 1958-09-01    313.500000
# 1958-10-01           NaN
# 1958-11-01    313.425000
# 1958-12-01    314.700000
# Freq: MS, Name: co2, dtype: float64
```




    date
    1958-03-01    316.100000
    1958-04-01    317.200000
    1958-05-01    317.433333
    1958-06-01           NaN
    1958-07-01    315.625000
    1958-08-01    314.950000
    1958-09-01    313.500000
    1958-10-01           NaN
    1958-11-01    313.425000
    1958-12-01    314.700000
    Freq: MS, Name: co2, dtype: float64



Looking at the index values, we can see that our time series now carries aggregated data on monthly terms, shown as `Freq: MS`. 

### Time-series Index Slicing for Data Selection

Slice our dataset to only retrieve data points that come after the year 1990.


```python
# Slice the timeseries to contain data after year 1990. 
CO2_monthly_mean['1990':]
# 1990-01-01    353.650
# 1990-02-01    354.650
#                ...   
# 2001-11-01    369.375
# 2001-12-01    371.020
# Freq: MS, Name: co2, Length: 144, dtype: float64
```




    date
    1990-01-01    353.650
    1990-02-01    354.650
    1990-03-01    355.480
    1990-04-01    356.175
    1990-05-01    357.075
    1990-06-01    356.080
    1990-07-01    354.675
    1990-08-01    352.900
    1990-09-01    350.940
    1990-10-01    351.225
    1990-11-01    352.700
    1990-12-01    354.140
    1991-01-01    354.675
    1991-02-01    355.650
    1991-03-01    357.200
    1991-04-01    358.600
    1991-05-01    359.250
    1991-06-01    358.180
    1991-07-01    356.050
    1991-08-01    353.860
    1991-09-01    352.125
    1991-10-01    352.250
    1991-11-01    353.740
    1991-12-01    355.025
    1992-01-01    355.900
    1992-02-01    356.680
    1992-03-01    357.900
    1992-04-01    359.075
    1992-05-01    359.540
    1992-06-01    359.125
                   ...   
    1999-07-01    369.000
    1999-08-01    366.700
    1999-09-01    364.675
    1999-10-01    365.140
    1999-11-01    366.650
    1999-12-01    367.900
    2000-01-01    369.020
    2000-02-01    369.375
    2000-03-01    370.400
    2000-04-01    371.540
    2000-05-01    371.650
    2000-06-01    371.625
    2000-07-01    369.940
    2000-08-01    367.950
    2000-09-01    366.540
    2000-10-01    366.725
    2000-11-01    368.125
    2000-12-01    369.440
    2001-01-01    370.175
    2001-02-01    371.325
    2001-03-01    372.060
    2001-04-01    372.775
    2001-05-01    373.800
    2001-06-01    373.060
    2001-07-01    371.300
    2001-08-01    369.425
    2001-09-01    367.880
    2001-10-01    368.050
    2001-11-01    369.375
    2001-12-01    371.020
    Freq: MS, Name: co2, Length: 144, dtype: float64



Slice the time series for a given time interval. Let's try to retrieve data starting from Jan 1990 to Jan 1991.


```python
# Retrieve the data between 1st Jan 1990 to 1st Jan 1991
CO2_monthly_mean['1990-01-01':'1991-01-01']
# 1990-01-01    353.650
# 1990-02-01    354.650
# 1990-03-01    355.480
# 1990-04-01    356.175
# 1990-05-01    357.075
# 1990-06-01    356.080
# 1990-07-01    354.675
# 1990-08-01    352.900
# 1990-09-01    350.940
# 1990-10-01    351.225
# 1990-11-01    352.700
# 1990-12-01    354.140
# 1991-01-01    354.675
# Freq: MS, Name: co2, dtype: float64
```




    date
    1990-01-01    353.650
    1990-02-01    354.650
    1990-03-01    355.480
    1990-04-01    356.175
    1990-05-01    357.075
    1990-06-01    356.080
    1990-07-01    354.675
    1990-08-01    352.900
    1990-09-01    350.940
    1990-10-01    351.225
    1990-11-01    352.700
    1990-12-01    354.140
    1991-01-01    354.675
    Freq: MS, Name: co2, dtype: float64



## Missing Values

Check if there are missing values in the data set.


```python
# Get the total number of missing values in the time series
CO2_monthly_mean.isna().sum()
# 5
```




    5



Remember that missing values can be filled in a multitude of ways. Look for the next valid entry in the time series and fills the gaps with this value. Next, check if your attempt was successful by checking for missing values again.


```python
# perform backward filling of missing values
CO2_final = CO2_monthly_mean.fillna(CO2_monthly_mean.ffill())
CO2_final.isnull().sum()
# 0
```




    0



Great! Now your time series are ready for visualization and further analysis.

## Summary

In this introductory lab, you learned how to create a time series object in Python using Pandas. You learned how to check timestamp values as the data index and you learned about basic data handling techniques for getting time-series data ready for further analysis.
