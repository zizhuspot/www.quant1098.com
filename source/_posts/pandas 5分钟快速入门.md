---
title: pandas 5-minute quickstart
date: 2023-07-22 16:44:00
categories:
  - Quantitative Trading
tags: 
  - Quantitative trading
  - python
  - Getting Started
  - pandas
  - pandas
  - 5 minutes
description: This article is not suitable for python language has no basis for friends, suitable for language foundation or use a period of time python language but some of the syntax is a bit fuzzy friends, you can use this document as a pandas usage memo, at any time to check some fuzzy or memory is not too solid place
cover: https://s2.loli.net/2023/07/22/LgZKXF3PUYpxjkf.jpg
---

## Importing the pandas library

```python
import pandas as pd  # To import pandas as a third-party library, we usually give pandas an alias called pd
pd.set_option('expand_frame_repr', False)  # No line breaks when there are too many columns
```

## Import data

```python
df = pd.read_csv(
        # This parameter is the path of the data in the computer
        filepath_or_buffer = 'BITFINEX_BTCUSD_20180124_1T.csv',
        # This parameter represents the separator for the data, the default for csv files is a comma. Other common ones are '\t'
        sep=',', # This parameter represents skipping the data file.
        # This parameter means skip the first line of the data file and do not read it in.
        skiprows=1, # nrows.
        # nrows, only read the first n lines of data, if not specified, read all the data.
        nrows=15, # Read only the first n rows of data, if not specified, read all data.
        # Recognizes the data in the specified column as a date. If not specified, time data will be read in as a string. Don't use it at first.
        parse_dates=['candle_begin_time'], # Read all the data.
        # Set the specified column to index. if not specified, index defaults to 0, 1, 2, 3, 4...
        index_col=['candle_begin_time'], # Set the specified columns to index.
        # Read the specified columns, no other data will be read. If not specified, read all columns
        # usecols=['candle_begin_time', 'close'], # Read the data in the specified columns.
         When set to False, no error is reported and the row is skipped. Use this when the data is dirty.
        # error_bad_lines=False.
        # Recognize nulls in data as nulls.
        # na_values='NULL', # identify nulls in the data as nulls.

        # More other parameters, please directly search for "pandas read_csv", to go through them one by one. The more important ones, header, etc.
    )
```

![](https://s2.loli.net/2023/07/22/PBgx5VXL3QSaquh.png)

The use of read_csv import data is very convenient, the data type of the imported data is DataFrame. import data using the read series of functions and read_table, read_excel, read_json, etc., the content of their parameters are similar to each other, you can search for their own view.

## View Data

```python
print(df.shape) # Output how many rows and columns the dataframe has.
print(df.shape[0]) # Take the number of rows, and the corresponding number of columns is df.shape[1].
print(df.columns) # Output the name of each column in order, demonstrating how to traverse with a for statement.
print(df.index) # Outputs the name of each row in order, which can be traversed with a for statement.
print(df.dtypes) # Each column of data has a different type, such as numbers, strings, dates, etc. This method outputs each column's variable type. This method outputs each column variable type
print(df.head(3)) # Look at the first 3 rows of data, default is 5. Very close to natural language
print(df.tail(3)) # Look at the last 3 rows of data, default is 5.
print(df.sample(n=3)) # randomly pick 3 rows, use frac parameter if you want to go to a fixed ratio
print(df.describe()) # Very handy function to get a visual sense of each column of data; will only work for numeric columns
```

## data correction

```python
# 对print出的数据格式进行修正
pd.set_option('expand_frame_repr', False)  # 当列太多时不换行
pd.set_option('max_colwidth', 1)  # 设定每一列的最大宽度，恢复原设置的方法，pd.reset_option('max_colwidth')
pd.set_option("display.max_rows", 100)  # 设定显示最大的行数
pd.set_option('precision', 6)  # 浮点数的精度
print(df.head())

# 更多设置请见http://pandas.pydata.org/pandas-docs/stable/options.html
```

## Selects the specified data

```python
# ===== how to pick specified rows, columns
print(df['open']) # pick based on column name, read data is Series type
print(df[['candle_begin_time', 'close']]) # Select multiple columns at the same time, need two brackets, the data read is DataFrame type

# loc operation: read data by label (name of columns and index)
print(df.loc['2018-01-24 00:01:00']) # select a specified row, the data read is Series type
print(df.loc[['2018-01-24 00:01:00', '2018-01-24 00:04:00']]) # Pick the specified two rows
print(df.loc['2018-01-24 00:01:00': '2018-01-24 00:06:00']) # pick multiple rows in this range, similar to the slice operation in list, the data read is of DataFrame type
print(df.loc[:, 'open':'close']) # Select multiple columns in this range, the data read is of type DataFrame
print(df.loc['2018-01-24 00:01:00': '2018-01-24 00:05:00', 'open':'close']) # Read the specified multiple rows and columns. Before the comma is the range of rows, after the comma is the range of columns. The data to be read is of type DataFrame
print(df.loc[:, :]) # Read all rows, all columns, data read is DataFrame type
print(df.at['2018-01-24 00:01:00', 'open']) # Use at to read one of the specified elements. loc works too, but at is more efficient.

# iloc operation: read data by position
print(df.iloc[0]) # Pick a row by index, read data is Series type.
print(df.iloc[1:3]) # select multiple rows in this range, the data read is of type DataFrame
print(df.iloc[:, 1:3]) # select multiple columns in this range, the data read is of type DataFrame
print(df.iloc[1:3, 1:3]) # read the specified multiple rows and columns, the data read is of type DataFrame
print(df.iloc[:, :]) # Read all rows, all columns, the data read is of type DataFrame
print(df.iat[1, 1]) # Use iat to read a specified element. Using iloc works too, but iat is more efficient.
```

## column operation

```python
# rows and columns add, subtract, multiply and divide
print('January 24, 2018' + df['BST']) # String columns can be directly added to strings to operate on whole columns
print(df['close'] * 100) # Numeric columns can be added or multiplied directly to operate on whole columns.
print(df[['close', 'volume']])
print(df['close'] * df['volume']) # The two columns can be manipulated directly between them. What is the closing price * volume calculated?
# Add a new column
df['BST2'] = 'January 24, 2018' + df['BST']
df['exchange'] = 'bitfinex'
```

## statistical function

```python
# ===== statistics function
print(df['close'].mean()) # Finds the mean of an entire column, returning a number. Null values are automatically excluded.
print(df[['close', 'volume']].mean()) # Find the mean of two columns, return two numbers, Series
print(df[['close', 'volume']])
print(df[['close', 'volume']].mean(axis=1)) # Find the mean of two columns, return DataFrame. axis=0 or 1 to be clear.
# axis=1, on behalf of the operation on the whole several columns. axis=0 (default) on behalf of the operation on several rows. It's normal to get confused in practice, just try it out.

print(df['high'].max()) # Maximum value.
print(df['low'].min()) # min value
print(df['close'].std()) # standard deviation
print(df['close'].count()) # number of non-empty data
print(df['close'].median()) # median
print(df['close'].quantile(0.25)) # 25% quantile
# There are other functions to calculate other metrics, so you can search for them yourself if you encounter them in practice.
```

## Shift-like functions, ways to delete columns

```python
# =====shift class function, way to delete columns
df['next cycle close'] = df['close'].shift(-1) # Read the data of the previous row, if the parameter is set to 3, it is to read the data of the last three rows; if the parameter is set to -1, it is to read the data of the next row;
del df['next cycle close'] # method to delete a column

df['up or down'] = df['close'].diff(-1) # find the value obtained by subtracting the data in this row from the data in the previous row
print(df[['close', 'up/down']])
df.drop(['close', 'close']], axis=1, inplace=True) # another way to delete a column, inplace parameter means whether to replace the original df or not

df['up/down'] = df['close'].pct_change(1) # similar to diff, but the ratio of the two numbers is the equivalent of the up/down ratio

# =====cum(cumulative)-like function
df['volume_cum'] = df['volume'].cumsum() # The cumulative value of the column
print(df[['volume', 'volume_cum']])
print((df['volume'] + 1.0).cumsrod()) # Cumulative value of the column, here the calculation is the money curve, assuming an initial 1$
```

## Other column functions

```python
# ===== other column functions
df['close_rank'] = df['close'].rank(ascending=True, pct=False) # Output the rank. ascending parameter represents whether it's sequential or inverse. pct parameter represents whether the output is ranked or a percentage of ranked
print(df[['close', 'close_rank']])
del df['close_rank']]
print(df['close'].value_counts()) # Count. Counts the number of times each element in the column appears. The data returned is Series
```

## Data filtering 

```python
# ===== Filtering operation to filter out relevant data based on specified conditions.
print(df['symbol'] == 'AIDBTC') # Determine if the pair code is equal to BTCUSD
print(df[df['symbol'] == 'BTCUSD']) # Output that will be judged as True: pick the rows where the pair code is equal to BTCUSD
print(df[df['symbol'] == 'BTCUSD'].index) # output the index of the row judged True
print(df[df['symbol'].isin(['BTCUSD', 'LTCUSD', 'ETHUSD'])]) # pick rows with code equal to 'BTCUSD' or 'LTCUSD ' or 'ETHUSD'
print(df[df['close'] < 10.0]) # Pick rows with a close less than 10
print(df[(df['close'] < 10.0) & (df['symbol'] == 'AIDUSD')]) # Two conditions, or if it is |
```

## Missing value handling

```python
# ===== Missing value handling: there are missing values in the raw data, how to handle them?
# Create missing values
index = df[df['candle_begin_time'].isin(['2018-01-24 00:00:00', '2018-01-24 12:00:00'])].index
df.loc[index, '12 hours'] = df['candle_begin_time']

# Drop the missing values
print(df.dropna(how='any')) # Remove rows with null values. how='any' means, as long as there is a null value in the row, it will be removed, can be changed to all.
print(df.dropna(subset=['12 hours', 'close'], how='all')) # The subset parameter specifies that null values are judged in a particular column.
# all means all are null before the row is deleted; any deletes the row as long as one is null.

# Fill in missing values
print(df.fillna(value=0)) # Directly assign missing values to fixed values
# df['12 hours'].fillna(value=df['close'], inplace=True) # Directly assign missing values to other columns of data
print(df)
print(df.fillna(method='ffill')) # Find the nearest non-null value upwards and fill the missing position with that value, called forward fill, very useful
print(df.fillna(method='bfill')) # look down to the nearest non-null value and fill the true position with that value, known as backward fill

# Find the missing value
print(df.notnull()) # Determine if it is null, reverse function isnull()
print(df[df['12 hours'].notnull()]) # Output the line with '12 hours' listed as null
```

## sorting function

```python
print(df.sort_values(by=['candle_begin_time'], ascending=0)) # The by parameter specifies what to sort by, and the ascending parameter specifies whether it's sequential or inverse, 1 sequential, 0 inverse
print(df.sort_values(by=['symbol', 'candle_begin_time'], ascending=[0, 0])) # sort by multiple columns
```

## Data consolidation

```python
# ===== two df up and down merge operations, append operations
df1 = df.iloc[0:10][['candle_begin_time', 'symbol', 'close', 'volume']]
print(df1)
df2 = df.iloc[5:15][['candle_begin_time', 'symbol', 'close', 'volume']]
print(df2)
print(df1.append(df2)) # append operation to splice df1 and df2 up and down. Watch the index after the append. index can be repeated
df3 = df1.append(df2, ignore_index=True) # ignore_index parameter, user re-determines index.
print(df3)    
```

## Data de-duplication

```python
# ===== de-duplicating data
# There are duplicate rows in df3, how do we get rid of the duplicates?
df3.drop_duplicates(
subset=['candle_begin_time', 'symbol'], # The subset parameter is used to specify the class of data based on which to determine if it is duplicated. If not specified, all columns will be used to determine duplicates.
keep='first', # When removing duplicates, do we keep the top row or the bottom row? first keeps the top row, last keeps the bottom row, False keeps none of the rows.
inplace=True)
print(df3)
```

## Other Important Functions

```python
# ===== other common and important functions
df.reset_index(inplace=True, drop=False) # reset index
print(df.rename(columns={'close': 'close', 'open': 'open'})) # The rename function changes the name of the variable. The name to be changed is passed to the columns parameter using dict
print(df.empty) # Determine if a df is empty.
print(pd.DataFrame().empty) # pd.DataFrame() creates an empty DataFrame.
print(pd.DataFrame().empty) # pd.DataFrame() creates an empty DataFrame, output here is empty
```

## string processing (computing)

```python
# ===== string processing
print(df['symbol'])
print('BTCUSD'[:3])
print(df['symbol'].str[:3])
print(df['symbol'].str.upper()) # After adding str you can use common string functions to manipulate the whole columns
print(df['symbol'].str.lower())
print(df['symbol'].str.len()) # Calculate the length of the string, length
df['symbol'].str.strip() # strip operation, remove spaces on both sides of the string
print(df['symbol'])
print(df['symbol'].str.contains('AID')) # Determine whether a string contains certain characters or not
print(df['symbol'].str.replace('AID', 'AVT')) # do replacement, replace sz with sh
# # For more string functions see: http://pandas.pydata.org/pandas-docs/stable/text.html#method-summary
```

## time handling

```python
# ===== time processing
print(df['candle_begin_time'])
print(df.at[0, 'candle_begin_time'])
print(type(df.at[0, 'candle_begin_time']))
df.rename(/)
df['candle_begin_time'] = pd.to_datetime(df['candle_begin_time']) # change transaction date from string to time variable
print(df.at[0, 'candle_begin_time'])
print(type(df.at[0, 'candle_begin_time']))

print(pd.to_datetime('January 11, 1999')) # pd.to_datetime function: transforms strings into time variables
print(df['candle_begin_time'])
print(df['candle_begin_time'].dt.year) # Output the year for this date. The corresponding month is the month, day is the day, and hour, minute, second
print(df['candle_begin_time'].dt.week) # What week of the year is this day
print(df['candle_begin_time'].dt.dayofyear) # What day of the year is this?
print(df['candle_begin_time'].dt.dayofweek) # This day is the first day of the week, with 0 being Monday
print(df['candle_begin_time'].dt.dayofweek) # Same function as above, but more commonly used
print(df['candle_begin_time'].dt.weekday_name) # Same function as above, returns the day of the week in English, used for reports.
print(df['candle_begin_time'].dt.days_in_month) # how many days in the month this day is in
print(df['candle_begin_time'].dt.is_month_start) # Is this day the beginning of the month and is there an is_month_end?
print(df['candle_begin_time'] + pd.Timedelta(hours=1)) # add a day, Timedelta is used to represent time difference data, [weeks, days, hours, minutes, seconds, milliseconds, microseconds, nanoseconds microseconds, nanoseconds]
print((df['candle_begin_time'] + pd.Timedelta(days=1)) - df['candle_begin_time']) # add a day then subtract today's date
```

## Rolling, expanding operations

```python
# =====rolling, expanding operations
# Calculate the mean of the column 'close'
print(df['close'])
# How do I get the average of the last 3 days of close for each day? i.e. how to calculate the common moving average?
# Use the rolling function
df['close_3-day_mean'] = df['close'].rolling(3).mean()
print(df[['close', 'close_3_day_mean']])
rolling(n) means to take the last n rows of data, only calculate the n rows of data. Can be followed by various types of calculation functions, such as max, min, std, etc.
print(df['close'].rolling(5).max())
print(df['close'].rolling(3).min())
print(df['close'].rolling(3).std())

# rolling can calculate the average of the last 3 days for each day, what should I do if I want to calculate the average of each day from the beginning to the present?
# Use expanding operation
df['close_to_date_mean'] = df['close'].expanding().mean()
print(df[['close', 'close_to_date_mean']])

# expanding means taking the data from the beginning to the present. This can be followed by all sorts of math functions
print(df['close'].expanding().max())
print(df['close'].expanding().min())
print(df['close'].expanding().std())

# rolling and expanding are simply tailor-made methods for the quantitative domain and are often used.
```

## exports

```python
# ===== output to local file
print(df)
df.to_csv('output.csv', index=False)
```

## The Complete List of Functions

Where can I see all the functions?

http://pandas.pydata.org/pandas-docs/stable/api.html 



