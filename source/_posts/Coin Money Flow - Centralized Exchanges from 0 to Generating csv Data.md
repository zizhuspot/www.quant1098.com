---
title: Coin Money Flow - Centralized Exchanges from 0 to Generating csv Data
date: 2023-10-25 12:07:00
categories:
  - quantitative Trading
tags:
  - Centralized
  - Exchanges
  - Quantitative tool
  - Generating
  - digiccy
  - quant1098.com
  - Okex
  - csv Data
description: Explain how to crawl the data down and save it as a csv
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

Explain how to crawl the data down and save it as a csv

## Methods

Find the web page, open F12, check the network monitoring content, find the corresponding xhr file, determine the request link and method, simulate the request and then process the data

## Step one

Get the non-small rankings of centralized exchanges and some information about them

![](https://github.com/zqwuming/blogimage/blob/main/img/Mq3IH54kS62t4kfcDQQ3fwqGxaTcVItTStue6yNo.png?raw=true)

![](https://s2.loli.net/2023/10/25/3HDhG1QgEu5zUIb.png)

Here I'll know the request URL and request method based on the method just described (since this is viewable without logging in, I'd say no API or anything like that)

Request URL: https://dncapi.aigopocket.com/api/v2/exchange/web-exchange?token=&page=1&pagesize=100&sort_type=exrank&asc=1&isinnovation=1&type=all&area=&webp=1

Request method: GET

#### Code

Now that we know the request URL and request method, let's write the code.

```python
def fetch_data():
    """
    This function is responsible for fetching data from a specific URL for an exchange.

    The logic is as follows:
    1. the target URL is defined.
    2. sends an HTTP GET request to that URL using the requests library.
    3. Check the returned HTTP status code.
        - If the status code is 200 (i.e., the request was successful), the returned JSON data is parsed and returned.
        - If the status code is not 200 (i.e., the request failed), print the status code and return None.
    """

    url = "https://dncapi.aigopocket.com/api/v2/exchange/web-exchange?token=&page=1&pagesize=100&sort_type=exrank&asc=1&isinnovation=1&type=all&area=&webp=1"  # 目标URL
    response = requests.get(url)  # Send an HTTP GET request to the target URL

    if response.status_code == 200:  # Check if the HTTP status code is 200 (request successful)
        data = response.json()  # Parsing the returned JSON data
        return data  # Returns parsed data
    else:
        print("Request failed with status code:", response.status_code)  # Printing failed HTTP status codes
        return None  # Returns None if the request fails

data = fetch_data()
print(data)

```

Returned content (I usually do not target to view the data, so that I use word to show the content of the returned DATA)

![404069](https://github.com/zqwuming/blogimage/blob/main/img/rydx5fM5Zn9YAEDyd7Q5zTdM6dVOleDOLZCy3Tbe.png?raw=true)

We can see that under data contains the data we need (information about the exchange)

So let's extract him and do some processing on him (it's all relatively simple, put it all together)

```python
import pandas as pd


def fetch_data():
    """
    This function is responsible for fetching the data of an exchange from a specific URL.


    The logic is as follows:
    1. defines the target URL.
    2. sends an HTTP GET request to that URL using the requests library.
    3. Check the returned HTTP status code.
        - If the status code is 200 (i.e., the request was successful), the returned JSON data is parsed and returned.
        - If the status code is not 200 (i.e., the request failed), print the status code and return None.
    """


    url = "https://dncapi.aigopocket.com/api/v2/exchange/web-exchange?token=&page=1&pagesize=100&sort_type=exrank&asc=1&isinnovation=1& type=all&area=&webp=1" # destination URL
    response = requests.get(url) # send HTTP GET request to target URL


    if response.status_code == 200: # Check if the HTTP status code is 200 (the request was successful)
        data = response.json() # parse the returned JSON data
        return data # Return the parsed data
    data = response.json() # Parses the returned JSON data.
        print("Request failed, status code: ", response.status_code) # Print the HTTP status code of the failed request.
        return None # Returns None if the request failed

def transform_to_df(data).
    """
    This function is responsible for transforming the input dictionary into a pandas DataFrame.


    The logic is as follows:
    1. check if the key 'data' is present in the input dictionary. 2.
    2. if there is, use the value corresponding to this key to create a pandas DataFrame and return this DataFrame.
    3. If not, print an error message and return None.
    """


    if 'data' in data: # Check if the dictionary contains the key 'data'
        df = pd.DataFrame(data['data']) # Create a DataFrame using the value corresponding to the 'data' key.
        return df # return the created DataFrame
    else.
        print("Key 'data' not found") # Print an error message if the key 'data' is not in the dictionary.
        return None # Return None to indicate that the conversion failed.


def rename_columns(df): # If there is no 'data' key in the dictionary, print an error message.
    columns_mapping = {
        
        
        'logo': 'Logo',
        'rank': 'ranked',
        'pairnum': 'number of pairs of transactions', 
        'volumn': 'number of transactions
        'volumn': 'volume', 'volumn_btc'
        'volumn_cny': 'Volume_CNY',
        'change_volumn': 'volume_change',
        'assets_usd': 'assets_usd',
        'risk_level': 'Risk level'
    }
    df.rename(columns=columns_mapping, inplace=True)




def sort_by_assets_usd(df).
    df['assets_usd'] = pd.to_numeric(df['assets_usd'], errors='coerce') # convert assets_usd columns to numeric, set to NaN if unable to do so
    df.sort_values(by='Asset_Dollar', ascending=False, inplace=True) # Sort by Asset_Dollar column in descending order.
    df.reset_index(drop=True, inplace=True) # reset indexes




def save_to_csv(df).
    print("Successfully got exchange name from non-small")
    # Optionally save or not
    # df.to_csv('exchange_data.csv', index=False, encoding="GBK")
    # print("Data has been saved as exchange_data.csv")




# Fetch and process the new data source
data = fetch_data()
if data is not None.
    df = transform_to_df(data)
    if df is not None: df = transform_to_df(data)
        rename_columns(df) # rename the columns of the DataFrame
        sort_by_assets_usd(df) # Sort the DataFrame by asset values
        save_to_csv(df) # Save the DataFrame as a CSV file.



```

The final rendering:

![157902](https://github.com/zqwuming/blogimage/blob/main/img/ToLRiNTXpL2bbWZpmD4JJEanQ3mybiO4Gd6ZsHUj.png?raw=true)

### Note

The code I've given is not saved as a csv, because I'm passing the df directly into the requested function later on, and there's no point in saving it.

### Step 2

Now that we know the name of the exchange we want to request, we are ready to start the request.

First, we'll use the same method to find out the inflow and outflow data of the exchange.

[web url ： Arkham (arkhamintelligence.com)](https://platform.arkhamintelligence.com/)

Search for binance, click on it and open F12, or go in, click F12 and refresh the page.

![632226](https://github.com/zqwuming/blogimage/blob/main/img/40VLYHJf3jwTn0462PVB6ieYS1xQtz7MAPMV8dze.png?raw=true)

![804938](https://github.com/zqwuming/blogimage/blob/main/img/VQgyoKKQxdmWsynwWFso7LX0lPnYkO94akBWgeew.png?raw=true)

Well, at this point we know the request URL and the request method, start building the code

Request URL: https://api.arkhamintelligence.com/flow/entity/binance
Request method: GET

### code:

```python
def fetch_and_save_binance_data(df).
    """
    This function is used to fetch and save data from Binance exchange.

    Logical process:
    1. filter out exchanges with assets greater than $50 million.
    2. Normalize the names of these exchanges.
    3. For each filtered exchange, launch an API request to get the data. 4.
    4. parse the data returned by the API and save it as a CSV file. 5.
    5. Perform subsequent processing of the CSV file.

    Parameters:
    df: DataFrame containing data about the exchanges and their assets

    Return Value:
    None
    """
    # Step 1: Filter out exchanges with assets greater than 50 million dollars
    high_value_entities = df[df['assets_$'] > 5e7]

    # Step 2: Convert the names of these high value entities to lowercase and into a list
    high_value_names = [name.lower() for name in high_value_entities['name'].values.tolist()]

    # Create a dictionary of replacement names for some specific exchanges
    name_replacements = {'coinbase pro': 'coinbase', 'mexc global': 'mexc', 'gate.io': 'gate-io'}
    high_value_names = [name_replacements.get(name, name) for name in high_value_names]

    # Step 3: Make an API request to get the data
    for name in high_value_names.
        time.sleep(0.5) # Pause for 0.5 seconds to avoid issuing the request too quickly
        print(f "Fetching: {name} trade flow data")
        url = f "https://api.arkhamintelligence.com/flow/entity/{name}"
        response = requests.get(url)

        dfs = {} # Use to store data from different networks
        count_no_time = 0 # Used to keep track of the number of networks without a time field

        # Step 4: Parse the data returned by the API and save it as CSV
        if response.status_code == 200: # Parsing the data returned by the API and saving it as CSV.
            print(f "Request successful for {name}!")
            data = response.json()
            total_networks = len(data)

            
                dfs[network] = pd.DataFrame(records)

                # Check for the existence of a time field
                if 'time' in dfs[network].columns.
                    # Time field handling
                    dfs[network]['time'] = pd.to_datetime(dfs[network]['time'])
                    dfs[network]['time'] = dfs[network]['time'] + DateOffset(hours=12)
                    dfs[network]['time'] = dfs[network]['time'].dt.tz_localize(None)

                    # Create the folder where the data will be stored
                    folder_path = f "E:/Blockchain data acquisition/data/{name}fund flow history data/"
                    if not os.path.exists(folder_path):: os.makedirs(fs.folder_path)
                        os.makedirs(folder_path)

                    # Save as CSV
                    csv_path = os.path.join(folder_path, f"{network}.csv")
                    dfs[network].to_csv(csv_path, index=False, encoding="GBK")

                    # Step 5: Perform subsequent processing on the CSV file
                    rename_csv_columns(csv_path)
                    calculate_and_update_net_inflow(folder_path, network)
                else.
                    count_no_time += 1

            if count_no_time == total_networks.
                print(f "No {name} exchange data in AR")
        print(f "No {name} exchange data in AR")
            print(f "Request failed for {name}, status code: {response.status_code}")

# Define a function to rename a column in a CSV file
def rename_csv_columns(csv_path).
    # Use Pandas to read the CSV file into a DataFrame object
    df = pd.read_csv(csv_path)

    # Rename the columns in the DataFrame using the rename method.
    # Parameter inplace=True implies modification of the original DataFrame
    df.rename(columns={
        'time': 'time', # rename 'time' to 'time'
        'inflow': 'inflow', # rename 'inflow' to 'inflow'
        'outflow': 'outflow', # rename 'outflow' to 'outflow'
        'cumulativeInflow': 'cumulativeInflow', # rename 'cumulativeInflow' to 'cumulativeInflow'
        'cumulativeOutflow': 'cumulativeOutflow' # rename 'cumulativeOutflow' to 'cumulativeOutflow'
    }, inplace=True)

    # Save the updated DataFrame back to a CSV file using Pandas, with the file encoding set to GBK
    df.to_csv(csv_path, index=False, encoding="GBK")

# Define a function to calculate and update the net inflow
def calculate_and_update_net_inflow(folder_path, network).
    # Construct the full path to the CSV file
    csv_path = os.path.join(folder_path, f"{network}.csv")

    # Use Pandas to read the CSV file into a DataFrame object with the file encoding set to GBK
    df = pd.read_csv(csv_path, encoding="GBK")

    # Check if 'inflow' and 'outflow' columns exist in the DataFrame
    if 'inflows' in df.columns and 'outflows' in df.columns: # Calculate net inflows and add them to df.columns.
        # Calculate the net inflows and store the results in a new column 'flows'
        df['flows'] = df['inflows'] - df['outflows']

        # Use Pandas to save the updated DataFrame back to a CSV file with the file encoding set to GBK
        df.to_csv(csv_path, index=False, encoding="GBK")

```

Here's what the code does: traverses the request for URLs based on the name of the exchange we just read and saves them as a csv

### rendering :

![296777](https://github.com/zqwuming/blogimage/blob/main/img/rvfs8gYpHaAdZd8pCvIx7b1WAUjXPJYvUNFl018x.png?raw=true)

### Note

This data is updated every day at 12 noon (it should be, it wasn't updated at 8 am anyway, I looked at it at 2 pm and found it was updated)

# Summary

So far, we already know the method of obtaining and how to process into csv, then we can do what we want according to this. 

I've added detailed comments to the code, so you can read the code carefully (it's not that hard to write).

The way to crawl the data is through F12 to find the corresponding data request URL and the way, and according to the return data for data processing, and ultimately saved as csv

I uploaded the file, is able to run successfully, you just need to change the path to your own path can be used!

```python
import os
import json
import time
import requests
import datetime
import matplotlib
import pandas as pd
from pandas.tseries.offsets import DateOffset


def fetch_data():
    """
    This function is responsible for fetching data from a specific URL for an exchange.

    The logic is as follows:
    1. the target URL is defined.
    2. sends an HTTP GET request to that URL using the requests library.
    3. Check the returned HTTP status code.
        - If the status code is 200 (i.e., the request was successful), the returned JSON data is parsed and returned.
        - If the status code is not 200 (i.e., the request failed), print the status code and return None.
    """

    url = "https://dncapi.aigopocket.com/api/v2/exchange/web-exchange?token=&page=1&pagesize=100&sort_type=exrank&asc=1&isinnovation=1&type=all&area=&webp=1"  # 目标URL
    response = requests.get(url)  # Send an HTTP GET request to the target URL

    if response.status_code == 200:  # Check if the HTTP status code is 200 (request successful)
        data = response.json()  # Parsing the returned JSON data
        return data  # Returns parsed data
    else:
        print("Request failed with status code：", response.status_code)  # Printing failed HTTP status codes
        return None  # Returns None if the request fails


def transform_to_df(data):

    if 'data' in data: 
        df = pd.DataFrame(data['data'])  
        return df  
    else:
        print("未找到'data'键")  
        return None  

def rename_columns(df):
    columns_mapping = {
        'id': 'identifier',
        'name': 'name',
        'logo': 'Logo',
        'rank': 'ranked',
        'pairnum': 'number of pairs of transactions', 'volumn': 'number of transactions
        'volumn': 'volume', 'volumn_btc'
        
        'volumn_cny': 'Volume_CNY',
        
        'change_volumn': 'volume_change',
        
        
        'assets_usd': 'assets_usd', 'assets_usd'.
        
        'risk_level': 'Risk level'
    }
    df.rename(columns=columns_mapping, inplace=True)


def sort_by_assets_usd(df).
    df['assets_usd'] = pd.to_numeric(df['assets_usd'], errors='coerce') # convert assets_usd columns to numeric, set to NaN if unable to do so
    df.sort_values(by='Asset_Dollar', ascending=False, inplace=True) # Sort by Asset_Dollar column in descending order.
    df.reset_index(drop=True, inplace=True) # reset indexes


def save_to_csv(df).
    print("Successfully got exchange name from non-small")
    # Optionally save or not
    # df.to_csv('exchange_data.csv', index=False, encoding="GBK")
    # print("Data has been saved as exchange_data.csv")


def fetch_and_save_binance_data(df).
    """
    This function is used to fetch and save Binance exchange data.

    Logical process:
    1. filter out exchanges with assets greater than $50 million.
    2. Normalize the names of these exchanges.
    3. For each filtered exchange, launch an API request to get the data. 4.
    4. parse the data returned by the API and save it as a CSV file. 5.
    5. Perform subsequent processing of the CSV file.

    Parameters:
    df: DataFrame containing data about the exchanges and their assets

    Return Value:
    None
    """
    # Step 1: Filter out exchanges with assets greater than 50 million dollars
    high_value_entities = df[df['assets_$'] > 5e7]

    # Step 2: Convert the names of these high value entities to lowercase and into a list
    high_value_names = [name.lower() for name in high_value_entities['name'].values.tolist()]

    # Create a dictionary of replacement names for some specific exchanges
    name_replacements = {'coinbase pro': 'coinbase', 'mexc global': 'mexc', 'gate.io': 'gate-io'}
    high_value_names = [name_replacements.get(name, name) for name in high_value_names]

    # Step 3: Make an API request to get the data
    for name in high_value_names.
        time.sleep(0.5) # Pause for 0.5 seconds to avoid issuing the request too quickly
        print(f "Fetching: {name} trade flow data")
        url = f "https://api.arkhamintelligence.com/flow/entity/{name}"
        response = requests.get(url)

        dfs = {} # Use to store data from different networks
        count_no_time = 0 # Used to keep track of the number of networks without a time field

        # Step 4: Parse the data returned by the API and save it as CSV
        if response.status_code == 200: # Parsing the data returned by the API and saving it as CSV.
            print(f "Request successful for {name}!")
            data = response.json()
            total_networks = len(data)

            
                dfs[network] = pd.DataFrame(records)

                # Check for the existence of a time field
                if 'time' in dfs[network].columns.
                    # Time field handling
                    dfs[network]['time'] = pd.to_datetime(dfs[network]['time'])
                    dfs[network]['time'] = dfs[network]['time'] + DateOffset(hours=12)
                    dfs[network]['time'] = dfs[network]['time'].dt.tz_localize(None)

                    # Create the folder where the data will be stored
                    folder_path = f "E:/Blockchain data acquisition/data/{name}fund flow history data/"
                    if not os.path.exists(folder_path):: os.makedirs(fs.folder_path)
                        os.makedirs(folder_path)

                    # Save as CSV
                    csv_path = os.path.join(folder_path, f"{network}.csv")
                    dfs[network].to_csv(csv_path, index=False, encoding="GBK")

                    # Step 5: Perform subsequent processing on the CSV file
                    rename_csv_columns(csv_path)
                    calculate_and_update_net_inflow(folder_path, network)
                else.
                    count_no_time += 1

            if count_no_time == total_networks.
                print(f "No {name} exchange data in AR")
        print(f "No {name} exchange data in AR")
            print(f "Request failed for {name}, status code: {response.status_code}")


# Define a function to rename a column in a CSV file
def rename_csv_columns(csv_path).
    # Use Pandas to read the CSV file into a DataFrame object
    df = pd.read_csv(csv_path)

    # Rename the columns in the DataFrame using the rename method.
    # Parameter inplace=True implies modification of the original DataFrame
    df.rename(columns={
        'time': 'time', # rename 'time' to 'time'
        'inflow': 'inflow', # rename 'inflow' to 'inflow'
        'outflow': 'outflow', # rename 'outflow' to 'outflow'
        'cumulativeInflow': 'cumulativeInflow', # rename 'cumulativeInflow' to 'cumulativeInflow'
        'cumulativeOutflow': 'cumulativeOutflow' # rename 'cumulativeOutflow' to 'cumulativeOutflow'
    }, inplace=True)

    # Save the updated DataFrame back to a CSV file using Pandas, with the file encoding set to GBK
    df.to_csv(csv_path, index=False, encoding="GBK")


# Define a function to calculate and update the net inflow
def calculate_and_update_net_inflow(folder_path, network).
    # Construct the full path to the CSV file
    csv_path = os.path.join(folder_path, f"{network}.csv")

    # Use Pandas to read the CSV file into a DataFrame object with the file encoding set to GBK
    df = pd.read_csv(csv_path, encoding="GBK")

    # Check if 'inflow' and 'outflow' columns exist in the DataFrame
    if 'inflows' in df.columns and 'outflows' in df.columns: # Calculate net inflows and add them to df.columns.
        # Calculate the net inflows and store the results in a new column 'flows'
        df['flows'] = df['inflows'] - df['outflows']

        # Use Pandas to save the updated DataFrame back to a CSV file with the file encoding set to GBK
        df.to_csv(csv_path, index=False, encoding="GBK")


# Fetch and process the new data source
data = fetch_data()
if data is not None: df = transform_to_df
    df = transform_to_df(data)
    if df is not None: df = transform_to_df(data)
        rename_columns(df) # rename the columns of the DataFrame
        sort_by_assets_usd(df) # Sort the DataFrame by asset values
        save_to_csv(df) # Save the DataFrame as a CSV file
        fetch_and_save_binance_data(df) # fetch and save Binance data

```
