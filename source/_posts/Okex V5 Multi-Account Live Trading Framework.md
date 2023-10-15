---
title: Okex V5 Multi-Account Live Trading Framework
date: 2023-10-15 16:07:00
categories:
  - quantitative trading
tags:
  - Digital currency
  - Stock
  - Quantitative tool
  - Kuangbiao
  - digiccy
  - quant1098.com
  - Okex
  - AIOQuant
description: A month ago according to the big brother's V5 framework, in the live run through the single account, and experimented for a period of time, up to open to 3 py exchange will reject the request, so the integration of multi-account ideas and frameworks, mainly in order to satisfy like me, apply for the Okex API late, can only be used with the V5, and the need for multi-account smoothing the funds curve boss.
cover: https://s2.loli.net/2023/10/15/EuRTH71P2hkAgrF.png
---

## Okex V5 Multi-Account Live Trading Framework

### I. Background: Based on **v3 multi-sub-account framework** and **Muyi big brother v5 framework combined with **modified** Okex V5 multi-account live trading framework**

A month ago according to the big brother's V5 framework, in the live run through the single account, and experimented for a period of time, up to open to 3 py exchange will reject the request, so the integration of multi-account ideas and frameworks, mainly in order to satisfy like me, apply for the Okex API late, can only be used with the V5, and the need for multi-account smoothing the funds curve boss.

This multi-account framework has been running live for two weeks, open and close positions have been verified, currently the first put 2wU live to run 20 sub-strategies, has caught the ETH, LTC trend, earnings are good, some of the strategy 1 week doubled, BTC caught a time but was worn out, the overall feeling is not bad, and I hope that the long-term to bring a good return.

### II. Version special instructions

1, because ccxt does not support okexV5, so the order request are using TradeAPI:

```python
# Initialize TradeAPI
apiKey = api_dict[accountName]['apiKey']
secret = api_dict[accountName]['secret']
password = api_dict[accountName]['password']
tradeAPI = Trade.TradeAPI(apiKey, secret, password, False, '0')

# Recall Example
order_info = tradeAPI.place_order(instId=params['instId'], tdMode=params['tdMode'], side=params['side'],posSide=params['posSide'],ordType=params['ordType'], sz=params['sz'], px=params['px'])['data'][0]
```

2, the original framework for each account did not do funds allocation for the strategy, here I simply according to the number of strategies to do an average allocation, need to be manually rebalance on a regular basis, the trend of opening positions is not high, so okay, and then continue to optimize the automatic rebalance;

```python
e = float(symbol_info.loc[symbol, "account equity"])
e = e / len(symbol_info)

# Do not exceed the maximum leverage of the account
l = min(float(leverage), float(symbol_info.at[symbol, "Maximum Leverage"]))

# Calculate the order size (in sheets)
size = math.floor(e * l * volatility_ratio / (price * coin_value))
```

3, nailed the message currently only joined the open and close positions and account information notification, further optimization is needed.

### Third, the framework composition introduction (only the main code, the overall code will be packaged and uploaded) / public data access

1. in two py files: okex_collect_data.py Functions_data.py okex_collect_data.py

```python
"""
0607 OKEx Contract Timing Strategy [Multi-Account] Live Trading Framework, Version 1.0
"""
import ccxt
from time import sleep
import os
import pandas as pd
from datetime import datetime
import glob
from config import *
from Functions_data import *

pd.set_option('display.max_rows', 1000)
pd.set_option('expand_frame_repr', False) # Do not reprise when there are too many columns.
# Set up column alignment for command line output
pd.set_option('display.unicode.ambiguous_as_wide', True)
pd.set_option('display.unicode.east_asian_width', True)

"""
Program ideas:
1. the overall idea is still the same as the original single-account program, both through the while True to let the program run in accordance with a fixed time interval.

2. use to get the data cycle, the smallest cycle, as each cycle of the time interval.
For example, if you want to get data for ['5m', '15m', '30m', '1h', '2h'] cycles, then use 5m as the time interval for each cycle.

3. Collect only the data of the smallest cycle in each cycle, and synthesize the other data using the resample of the smallest cycle data. Speed up the operation
For example to get ['5m', '15m', '30m', '1h', '2h'] cycles of data in total, but will only request 5min data from okex, and the rest of the data is synthesized using 5min data.
"""

# ===== reads configuration info from config
# = time period for which data needs to be grabbed
# In config.py, please put the smallest time period at the top. Ensure that time_interval_list is sorted from smallest to smallest and min_time_interval is the minimum time period
# time_interval_list = []
# for account_name in symbol_config_dict.keys()::
# time_interval = symbol_config_dict[account_name]['time_interval']
# if time_interval not in time_interval_list: # time_interval_list.
# time_interval_list.append(time_interval)
time_interval_list = ['30m', '1h']
# time_interval_list = ['5m', '15m']
min_time_interval = time_interval_list[0] # time_interval_list = ['5m', '15m']
candle_num = 50 # Get the number of K-lines at a time. It is important to make sure that the number of candle_num is large enough to ensure that min_time_interval can resample the maximum number of time intervals
print('Time period to be captured:', time_interval_list)
print('Time period to be captured:', time_interval_list)
print('The minimum time period is:', min_time_interval) # Other time periods must be integer multiples of the minimum time period.

# = coins to be captured
symbol_config = {}
for account_name in symbol_config_dict.keys()::
    for symbol in symbol_config_dict[account_name]['symbol_config'].
        symbol_config[symbol] = {
            'instrument_id': symbol_config_dict[account_name]['symbol_config'][symbol]['instrument_id']
        }
print('Currency to be captured:', symbol_config)
# symbol_config is more suitable for lists, making symbol_config a dict is just for compatibility with the previous program

# ===== other configuration information
# =exchange
OKEX_CONFIG = {
    # Don't need the api, give it and you'll be limited by the limit instead.
    'timeout': 1000, # timeout time is shorter
    'rateLimit': 10, # 'hostname': HOSTname
    # 'hostname': HOST_NAME, # Enable when fq is not available.
    'enableRateLimit': False
}
exchange = ccxt.okex(OKEX_CONFIG)

# = set the maximum number of K-lines to be collected, okex_v3 can't exceed 1440
max_len = 1000
```

```python
def main().
    # === Get historical data for the currency to be traded. Single account program: data to symbol_candle_data, multi-account program: data to local csv file.
    # Iterate over coins to get historical data
    for time_interval in time_interval_list.
        for symbol in symbol_config.keys().
            print('Grabbing historical data:', symbol_config[symbol]['instrument_id'], time_interval)
            # Fetch historical data for the currency, will remove the latest row of data
            df = fetch_okex_symbol_history_candle_data(symbol_config[symbol]['instrument_id'], time_interval,
                                                       max_len, max_try_amount=5)
            # Store the data locally
            df.to_csv(os.path.join(data_save_dir, '%s_%s.csv' % (symbol, time_interval)), index=False)
            time.sleep(medium_sleep_time) # short sleep

    # === enter each loop, note: each loop is min_time_interval
    while True.

        # == Get the strategy execution time and sleep until that time
        # Get the run_time
        run_time = next_run_time(min_time_interval, ahead_seconds=1)
        # Calculate the data period to be saved for this cycle. Calculating before sleep is to save time later on
        need_save_list = cal_need_save_time_interval_this_run_time(run_time, time_interval_list)
        # sleep
        time.sleep(max(0, (run_time - datetime.datetime.now()).seconds))
        while True: # when close to the target time
            if datetime.datetime.now() > run_time.
                break

        # = Get the most recent data for all currencies
        # Get data serially, as opposed to a single-account program, except that it removes the symbol_info
        recent_candle_data = single_threading_get_data_for_muti(exchange, symbol_config, min_time_interval, run_time, candle_num)
        for symbol in symbol_config.keys():: print('\n', run_time_interval, run_time, candle_num)
            print('\n', recent_candle_data[symbol].tail(2))

        # runtime 16:15

        # = convert the data cycle and store the data
        # Data to be stored
        agg_dict = {
            'open': 'first',
            'high': 'max', 'low'.
            
            
            
        }
        # Iterate over all the cycles that need to be converted
        for time_interval in need_save_list.
            # Iterate over all currencies to be converted
            for symbol in symbol_config.keys(): # Iterate over all coins to be converted.
                print('Starting conversion data cycle and store:', time_interval, symbol, )
                df = recent_candle_data[symbol]

                if time_interval ! = min_time_interval: # data period to be converted: not equal to min_time_interval
                    if time_interval.endswith('m'): # The data period to be converted: not equal to the min_time_interval.
                        rule = time_interval.replace('m', 'T')
                    else: rule = time_interval.replace('m', 'T')
                        rule = time_interval
                    # Conversion cycle
                    df = recent_candle_data[symbol].resample(rule=rule, on='candle_begin_time_GMT8').agg(agg_dict)
                    # Save the last row of data, keeping index
                    df.iloc[-1:, :].to_csv(os.path.join(data_save_dir, '%s_%s.csv' % (symbol, time_interval)), mode='a',
                                           header=None)
                else: # data period without conversion: equal to minimum time period
                    # Save data without index
                    df.iloc[-1:, :].to_csv(os.path.join(data_save_dir, '%s_%s.csv' % (symbol, time_interval)), mode='a',
                                           index=False, header=None)

                # Output data_ready
                t = datetime.datetime.now()
                pd.DataFrame(columns=[t]).to_csv(os.path.join(data_save_dir, 'data_ready_%s_%s_%s' % (
                symbol, time_interval, str(run_time).replace(':', '-'))), index=False)
                print('Time to finish storing data:', t)

        # = Retrieve historical data at regular intervals: every odd hour. This is done for 2 purposes:
        # 1. to keep the historical data from getting too long over time, which would slow down the reading of the data.
        # 2. In case the data is not correct during the calculation, it can be corrected from the server in time.
        if run_time.hour % 2 == 1 and run_time.minute == 0:: print('sleep 3 minutes')
            print('After 3min sleep, start re-capturing historical data...')
            time.sleep(3 * 60)
            # Iterate through to get the currency history
            for time_interval in time_interval_list.
                for symbol in symbol_config.keys().
                    print('Grabbing historical data:', symbol_config[symbol]['instrument_id'], time_interval)
                    # Fetch historical data for the currency, will remove the latest row of data
                    df = fetch_okex_symbol_history_candle_data(symbol_config[symbol]['instrument_id'],
                                                               time_interval, max_len=max_len)
                    # Store the data locally
                    df.to_csv(os.path.join(data_save_dir, '%s_%s.csv' % (symbol, time_interval)), index=False)
                    time.sleep(medium_sleep_time) # short sleep

        # = every once in a while, clear out the previous data_ready files: every Tuesday at 9:00, delete all data_ready files
        if run_time.weekday() == 2 and run_time.hour == 9 and run_time.minute == 0:.
            print('Start deleting data_ready files after 1min of sleep...')
            time.sleep(60)
            file_list = glob.glob(data_save_dir + '/*') # python's own libraries, or the paths to all the files in a certain folder
            file_list = list(filter(lambda x: 'data_ready_' in x, file_list))
            for file in file_list.
                os.remove(file)

        # = end of this loop
        print('\n', '-' * 20, 'End of this loop, %f seconds to next loop' % long_sleep_time, '-' * 20, '\n\n')
        time.sleep(long_sleep_time)
```

~~~python
if __name__ == '__main__':
    while True:
        try:
            main()
        except Exception as e:
            send_dingding_msg('系统出错，10s之后重新运行，出错原因：' + str(e))
            print(e)
            sleep(long_sleep_time)
```
~~~

Multi-account strategy live program

Contains:

4 py files: okexV5Trading_muti.py Functions_muti.py Signals_muti.py config.py.

1 set of TradeAPI tools, provided by Moyi Big Brother

```python
## okexV5Trading_muti.py
"""
0607 OKEx Contract Timing Strategy [Multi-Account] Live Trading Framework, Version 1.0
"""
import ccxt
import os
import sys
from time import sleep
import pandas as pd
from datetime import datetime
from config import *
from Functions_muti import *

pd.set_option('display.max_rows', 1000)
pd.set_option('expand_frame_repr', False) # do not repr when there are too many columns
# Set up column alignment for command line output
pd.set_option('display.unicode.ambiguous_as_wide', True)
pd.set_option('display.unicode.east_asian_width', True)

"""
Program Ideas:

The overall idea still hasn't changed much from the original single account program.

Except that the original single account program gets the latest data from the okex server, and now the multi-account program gets it locally.
"""

# ccxt version is 1.27.28 at the time of testing. if it is not this version, it may report an error, it is very unlikely. print(ccxt.__version__) to see the ccxt version.


# === Read the subaccount related parameters required for the program to run
if len(sys.argv) > 1.
    account_name = sys.argv[1]
else.
    print('Account_name parameter not specified, program exit')
    exit()
print('Subaccount id:', account_name)

# Initialize the subaccount name
# === Configure run-related parameters
# == Read configuration information from config.
exchange = ccxt.okex(OKEX_CONFIG_dict[account_name])
symbol_config = symbol_config_dict[account_name]['symbol_config']
print('Transaction information:', symbol_config)
# = time interval for execution
time_interval = symbol_config_dict[account_name]['time_interval']
print('Time interval:', time_interval)

# = Whether the program fetches data, places orders, etc. in parallel. Parallel is faster, serial is slower. (But on windows, because of the code, may instead serial will be faster, especially when not many coins are traded, please test yourself)
if_multi_thread = False # If False, then serial. Serial is recommended.

# = pinned
# Multiple pinning bots can be created in a pinning group.
# It is recommended to create a separate pinning bot which is specialized in sending pinning messages. Be sure to put the error reporting bot in id and secret in the default parameters of function.send_dingding_msg.
# When we run a multi-strategy, we run multiple python programs, and it is recommended that different programs use different pinned bots to send relevant messages. Start each program with the id and secret of that robot
robot_id = ''
secret = ''
robot_id_secret = [robot_id, secret]


def main():
    # === Get position data on first run
    # Initialize symbol_info at the start of each loop.
    symbol_info_columns = ['account_equity', 'position_direction', 'position_volume', 'position_yield', 'position_profit', 'average_price_of_position', 'current_price', 'max_leverage']
    symbol_info = pd.DataFrame(index=symbol_config.keys(), columns=symbol_info_columns) # Convert to dataframe

    # Update account information symbol_info
    symbol_info = update_symbol_info(symbol_info, symbol_config)
    print('\nsymbol_info:\n', symbol_info, '\n')
    # === Enter each loop
    while True.
        print('Subaccount id:', account_name, 'Time period:', time_interval)

        # = Get the strategy execution time and sleep until that time
        run_time = sleep_until_run_time(time_interval)
        # run_time = pd.to_datetime('2022-04-29 15:00:00')
        # = get the most recent data for all coins (can be changed to parallel as needed)
        symbol_candle_data = {}
        for symbol in symbol_config.keys():
            p = os.path.join(data_save_dir,
                             'data_ready_%s_%s_%s' % (symbol, time_interval, str(run_time).replace(':', '-')))
            print('Getting data address:', p)
            while True: if os.path.exists
                if os.path.exists(p):: 'Get data address:', p) while True: if os.path.exists(p).
                    print('Data already exists:', datetime.datetime.now())
                    break
                if datetime.datetime.now() > run_time + timedelta(minutes=1)::
                    print('Time exceeded 1 minute, abort reading from file, return empty data')
                    break
            symbol_candle_data[symbol] = pd.read_csv(os.path.join(data_save_dir, '%s_%s.csv' % (symbol, time_interval)))
            symbol_info.loc[symbol, 'signal price'] = symbol_candle_data[symbol].iloc[-1]['close'] # the latest price of the species
            print(symbol_candle_data[symbol].tail(5))

        # = calculate trading signals for each currency
        symbol_signal = calculate_signal(symbol_info, symbol_config, symbol_candle_data)
        print('\nsymbol_info:\n', symbol_info)
        print('Trade plan for this cycle:', symbol_signal)

        # = place order
        symbol_order = pd.DataFrame()
        print('symbol_sig:', symbol_signal)
        if symbol_signal.
            # if if_multi_thread: # Parallelize
            # symbol_order = multi_threading_place_order(exchange, symbol_info, symbol_config, symbol_signal) # multi_threading_place_order
            # else.
            symbol_order = single_threading_place_order(symbol_info, symbol_config, symbol_signal) # single_threading_place_order
            print('Order placed: \n', symbol_order)

            # Update order information to see if it's completely filled
            time.sleep(short_sleep_time) # take a break before updating order information
            symbol_order = update_order_info(symbol_config, symbol_order)
            print('Updating order record: ', '\n', symbol_order)

        # Re-update the account information symbol_info
        time.sleep(long_sleep_time) # take a break and update again
        symbol_info = pd.DataFrame(index=symbol_config.keys(), columns=symbol_info_columns)
        symbol_info = update_symbol_info(symbol_info, symbol_config)
        print('\nsymbol_info:\n', symbol_info, '\n')

        # Send pinning
        dingding_report_every_loop(symbol_info, symbol_signal, symbol_order, run_time, [' 2092b36b38f3130232290163da3c4dcc6af9bfb939b023a0865eaa46d6423626', ''])

        # End of this loop
        print('\n', '-' * 20, 'End of this loop, %f seconds to next loop' % long_sleep_time, '-' * 20, '\n\n')
        time.sleep(long_sleep_time)


if __name__ == '__main__'.
    if __name__ == '__main__': while True.
        try: main()
            __name__ == '__main__': while True: try: main()
        except Exception as e.
            send_dingding_msg('System error, re-run after 10s, reason for error:' + str(e))
            print(e.with_traceback())
            sleep(long_sleep_time)

```

### Reflections on subsequent optimization routes:

1. Record order information
2. Real-time K-line visualization monitoring
3. Fault-tolerant logging, error reporting
4. Sub-account management

Other:

CoinSafe is going to run BNB with a single account first, while others are waiting for the multi-account version to be released.

Started to study the neutral framework, backtesting a bit eat machine, still considering how to deal with.

I hope the big guys will give more advice and exchange more!

=====================