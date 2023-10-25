---
title: Simple live implementation of the FLASH strategy without any changes to the original framework
date: 2023-10-15 17:07:00
categories:
  - Quantitative Trading
tags:
  - Digital currency
  - framework
  - changes
  - FLASH strategy
  - digiccy
  - quant1098.com
  - Okex
  - AIOQuant
description: 
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

# Simple live implementation of the FLASH strategy without any changes to the original framework

Live implementation of the FLASH strategy (based on a small change in the framework)

First post the signal code:

```python
import os
import pandas as pd
from datetime import datetime


def signal_adapt_bolling_with_flash(df_base, now_pos, avg_price,symbol_config,symbol ):
    holding_times_min=10
    # Local file name, initialize local file
    para=symbol_config[symbol]['para']
    id=para['id']

    file_name=os.path.join(father_path,'data',id+'.csv')
    ini_data(file_name,dic = {'pre_signal': 0, 'stop_lose_price': None,'holding_times':0,'stop_win_times':0,'stop_win_price':0})

    # === Calculation of indicators

    n = para['n']

    #Fixed Stop Loss Parameters
    df=df_base.copy()
    stop_loss_pct=para['stop_loss_pct']

    df['median'] = df['close'].rolling(n).mean()
    # Calculate upper and lower tracks
    df['std'] = df['close'].rolling(n).std(ddof=0)  # ddof代表标准差自由度
    df['zscore'] = abs(df['close'] - df['median']) / df['std']
    df['zsocre_abs'] = df['zscore'].rolling(n).max().shift()

    df['upper'] = df['median'] + df['zsocre_abs'] * df['std']
    df['lower'] = df['median'] - df['zsocre_abs'] * df['std']




    #Initialization signals
    signal=None

    # shorting conditions
    condition1=df.iloc[-1]['close']<df.iloc[-1]['lower']
    condition2=df.iloc[-2]['close']>df.iloc[-2]['lower']
    short_condition =condition1 & condition2

    # long position
    condition1=df.iloc[-1]['close']>df.iloc[-1]['upper']
    condition2=df.iloc[-2]['close']<df.iloc[-2]['upper']
    long_condition =condition1 & condition2

    #level ground conditions
    condition1 = df.iloc[-1]['close'] > df.iloc[-1]['median']
    condition2 = df.iloc[-2]['close'] < df.iloc[-2]['median']
    close_short_condition = condition1 & condition2

    # Pindo condition (math.)
    condition1 = df.iloc[-1]['close'] < df.iloc[-1]['median']
    condition2 = df.iloc[-2]['close'] > df.iloc[-2]['median']
    close_long_condition = condition1 & condition2

    #Check for discrepancies between local files and actual positions
    # Read current parameters from local file
    info_dict = read_data(file_name)

    if now_pos != info_dict['pre_signal']:
        # If the actual position is 0
        if now_pos==0:
            info_dict={'pre_signal': 0, 'stop_lose_price': None,'holding_times':0,'stop_win_times':0,'stop_win_price':0}

        elif now_pos==1:
            info_dict={'pre_signal': 1,
                       'stop_lose_price': (avg_price * (1 - stop_loss_pct / 100)),
                       'holding_times':0,'stop_win_times':0,'stop_win_price':0}

        elif now_pos==-1:
            info_dict = {'pre_signal': -1,
                         'stop_lose_price': (avg_price * (1 + stop_loss_pct / 100)),
                         'holding_times': 0,
                         'stop_win_times': 0, 'stop_win_price': 0}
        save_data(info_dict,file_name)

    # Start ordering logic
    # short
    if short_condition & (now_pos!=-1):
        signal = -1
        stop_lose_price=df.iloc[-1]['close'] * (1 + stop_loss_pct / 100)
        info_dict = {'pre_signal': -1,
                     'stop_lose_price': stop_lose_price,
                     'holding_times': 0, 'stop_win_times': 0, 'stop_win_price': 0}
        save_data(info_dict,file_name)
    # long
    elif long_condition & (now_pos!=1):
        signal = 1
        stop_lose_price = df.iloc[-1]['close'] * (1 - stop_loss_pct / 100)
        info_dict = {'pre_signal': 1,
                     'stop_lose_price': stop_lose_price,
                     'holding_times': 0,
                     'stop_win_times': 0, 'stop_win_price': 0}
        save_data(info_dict,file_name)
    # ===Examine the need for a take profit and stop loss
    #When currently long
    elif now_pos == 1:
        # Determine which ma to use for flash accelerated averages by the number of positions held
        holding_times = info_dict['holding_times']
        ma_temp = max(n - holding_times, holding_times_min)
        # update info_dict的holding_times
        info_dict['holding_times'] = holding_times + 1
        # Calculating the Take Profit SMA
        df_temp=df['close'].rolling(ma_temp,min_periods=1).mean()
        # Extract the latest value of the SMA
        flash_stop_win=df_temp.iloc[-1]
        # midline flat long (i.e. short term)
        if close_long_condition:
            signal = 0
            info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                         'stop_win_price': 0}

        # fixed stop-loss (banking)
        elif df.iloc[-1]['close'] < (avg_price*(1-stop_loss_pct/100)): # fixed stop-loss (banking)
            signal = 0
            info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                         'stop_win_price': 0}

        #flash!!
        #If the price reaches the take profit point
        elif df.iloc[-1]['close'] < flash_stop_win:
            # If the price exceeds the price of the last stop, indicating that there may be room above, the flash acceleration is restarted, the
            if df.iloc[-1]['close'] > info_dict['stop_win_price'] or info_dict['stop_win_times'] == 0:
                info_dict['stop_win_price'] = df.iloc[-1]['close']
                # The number of stops generated +1.
                info_dict['stop_win_times'] = info_dict['stop_win_times'] + 1
                # Flash accelerated averages are zeroed out the number of times a position is held
                info_dict['holding_times'] = 0

            else:
                #exit with a profit
                signal=0
                info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                             'stop_win_price': 0}
        # Updating local files
        save_data(info_dict,file_name)
    # When currently short
    elif now_pos == -1:
        # Determine which ma to use for flash accelerated averages by the number of positions held
        holding_times = info_dict['holding_times']
        ma_temp = max(n - holding_times, holding_times_min)
        info_dict['holding_times'] = holding_times + 1


        df_temp = df['close'].rolling(ma_temp, min_periods=1).mean()
        flash_stop_win = df_temp.iloc[-1]
        if close_short_condition:
            signal = 0
            info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                         'stop_win_price': 0}

        elif df.iloc[-1]['close'] > (avg_price*(1+stop_loss_pct/100)):
            signal = 0
            info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                         'stop_win_price': 0}
        #flash!!
        # If the price reaches the take profit point
        elif df.iloc[-1]['close'] > flash_stop_win:
            # If the price falls below the price of the last stop, indicating that there may be room below, the flash acceleration is restarted, the
            if df.iloc[-1]['close'] < info_dict['stop_win_price'] or info_dict['stop_win_times'] == 0:
                info_dict['stop_win_price'] = df.iloc[-1]['close']
                # Number of stops generated +1
                info_dict['stop_win_times'] = info_dict['stop_win_times'] + 1
                # Flash accelerated averages are zeroed out the number of times a position is held
                info_dict['holding_times'] = 0

            else:
                signal = 0
                info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                             'stop_win_price': 0}
        #Updating local files
        save_data(info_dict,file_name)

    return signal

```

This is a live code for an adaptive boolean plus flash. One difference between me and Interpol is that I changed para to dict and symbol_config to:

```python
'eth-usdt': {'instrument_type': 'SWAP_USDT',
                 'size': 1,
                 'leverage': '1.2',
                 'strategy_name': 'signal_adapt_bolling_with_flash',
                 "para": {'n': 515,'stop_loss_pct': 7}
                 },
```

This looks like this, and then I added an id number to symbol_config's para in the main program, so you guys can put id=para['id']

```
```

This code is changed to look the way you like it.... Feel free to do so. father_path is a path that automatically reads the path to the previous folder in the current folder from:

  

```python
true_path=os.path.abspath(__file__)  # absolute path
father_path=os.path.dirname(os.path.dirname(true_path)) #Current working directory
root_path=os.path.dirname(os.path.dirname(os.path.dirname(os.path.dirname(true_path)))) #OKEX project root directory
```

‍
