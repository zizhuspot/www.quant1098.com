---
title: Circle Coin Event Strategy Manual Live Code
date: 2023-10-27 14:07:00
categories:
  - Quantitative Trading
tags:
  - Event Strategy
  - Live Code
  - Quantitative tool
  - Kuangbiao
  - digiccy
  - quant1098.com
  - Binance
  - AIOQuant
description: According to the strategy K-line cycle, run a program to get data at regular intervals.
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

Previously participated in the Circle B event-based research group, backtesting data feel quite good, want to try the real disk.

According to past experience, carefully go through the framework, the output of real disk is achievable.

Combined with the part of building a database in the basic course, to form a set of manual live output framework.

If there is any error or optimization, please advise me.

## Brief description

The idea of opening a real position is as follows:

![693430](https://s2.loli.net/2023/10/27/rA1FdRXmBvj492P.png)

- First the framework is split into two parts:

  - Update the data (0_DataUpdate.py)
  - Calculating and outputting the live market (1_StrategyLive.py)

![521711](https://s2.loli.net/2023/10/27/lTEHfnUhNr9wMxP.png)

1. The final operation can then become:

   1. according to the strategy K line cycle, timed to run the program to obtain data. (* For example, a strategy is a 24H K cycle, then you can start running the whole set of code every night after 0:00, to realize the manual order *)
   2. After obtaining the data to run the strategy output of the real part of the market
   3. manual order.

![739877](https://s2.loli.net/2023/10/27/HbPIJs38ZUqlLWn.png)

## Data Update

For the data update part, I'm going to rely on the undecided FOR cycle and put in all the coins in the binance contract, which is about 140 coins or so.

However, considering the fact that the coins in the contract are updated and downgraded, it is still necessary to update them every few days.

```python

import pandas as pd
import ccxt
import time
import os
import datetime
from config import *
from datetime import timedelta
pd.set_option('expand_frame_repr', False)  # 当列太多时不换行

def save_spot_candle_data_from_exchange(exchange, symbol, time_interval, start_time, path):
    limit = None
    if exchange.id == 'huobipro':
        limit = 2000

    df_list = []
    start_time_since = exchange.parse8601(start_time)
    end_time = pd.to_datetime(start_time) + datetime.timedelta(days=1)

    while True:
        df = exchange.fetch_ohlcv(symbol=symbol, timeframe=time_interval, since=start_time_since, limit=limit)
        df = pd.DataFrame(df, dtype=float)  
        df_list.append(df)
        t = pd.to_datetime(df.iloc[-1][0], unit='ms')
        start_time_since = exchange.parse8601(str(t))
        if t >= end_time or df.shape[0] <= 1:
            break
        time.sleep(1)
    df = pd.concat(df_list, ignore_index=True)
    df.rename(columns={0: 'MTS', 1: 'open', 2: 'high',
                       3: 'low', 4: 'close', 5: 'volume'}, inplace=True) 
    df['candle_begin_time'] = pd.to_datetime(df['MTS'], unit='ms')  
    df['candle_begin_time'] = df['candle_begin_time']
    df = df[['candle_begin_time', 'open', 'high', 'low', 'close', 'volume']]  序

    df = df[df['candle_begin_time'].dt.date == pd.to_datetime(start_time).date()]
    df.drop_duplicates(subset=['candle_begin_time'], keep='last', inplace=True)
    df.sort_values('candle_begin_time', inplace=True)
    df.reset_index(drop=True, inplace=True)

    path = os.path.join(path, exchange.id)
    if os.path.exists(path) is False:
        os.mkdir(path)
    path = os.path.join(path, 'spot')
    if os.path.exists(path) is False:
        os.mkdir(path)
    path = os.path.join(path, str(pd.to_datetime(start_time).date()))
    if os.path.exists(path) is False:
        os.mkdir(path)
    file_name = '_'.join([symbol.replace('/', '-'), time_interval]) + '.csv'
    path = os.path.join(path, file_name)
    df.to_csv(path, index=False)

today=datetime.date.today()
oneday=datetime.timedelta(days=1)
yesterday=today-oneday

begin_date = yesterday 
end_date = today 

date_list = []
date = pd.to_datetime(begin_date)
while date <= pd.to_datetime(end_date):
    date_list.append(str(date))
    date += datetime.timedelta(days=1)
start_time = '2021-01-01 00:00:00'
path = root_path + '/0_数据更新/coin_database'

error_list = []
for start_time in date_list:

    for exchange in [ccxt.binance()]:
        market = exchange.load_markets()
        market = pd.DataFrame(market).T
        symbol_list = list(market['symbol'])
        mysymbol = ['1INCH/USDT','AAVE/USDT',
                    'ADA/USDT','ALGO/USDT','ALICE/USDT','ALPHA/USDT',
                    'ANKR/USDT','ANT/USDT','APE/USDT','ARPA/USDT',
                    'API3/USDT','ATA/USDT','ATOM/USDT',
                    'AUDIO/USDT','AVAX/USDT','AXS/USDT','BAKE/USDT',
                    'BAL/USDT','BAND/USDT','BAT/USDT','BCH/USDT',
                    'BEL/USDT','BLZ/USDT','BNB/USDT','BNX/USDT',
                    'BTC/USDT','BTS/USDT','C98/USDT',
                    'CELO/USDT','CELR/USDT','CHR/USDT','CHZ/USDT',
                    'COMP/USDT','COTI/USDT','CRV/USDT','CTK/USDT',
                    'CTSI/USDT','CVC/USDT','DAR/USDT','DASH/USDT',
                    'DENT/USDT','DGB/USDT','DOGE/USDT',
                    'DOT/USDT','DUSK/USDT','DYDX/USDT','EGLD/USDT',
                    'ENJ/USDT','ENS/USDT','EOS/USDT','ETC/USDT',
                    'ETH/USDT','FIL/USDT','FLM/USDT','FLOW/USDT',
                    'FTM/USDT','FTT/USDT','GALA/USDT','GAL/USDT',
                    'GMT/USDT','GRT/USDT','GTC/USDT','HBAR/USDT',
                    'HNT/USDT','HOT/USDT','ICP/USDT','ICX/USDT',
                    'IMX/USDT','IOST/USDT','IOTA/USDT','IOTX/USDT',
                    'JASMY/USDT','KAVA/USDT','KLAY/USDT','KNC/USDT',
                    'KSM/USDT','LINA/USDT','LINK/USDT','LIT/USDT',
                    'LPT/USDT','LRC/USDT','LTC/USDT','MANA/USDT',
                    'MASK/USDT','MATIC/USDT','MKR/USDT','MTL/USDT',
                    'NEAR/USDT','NEO/USDT','NKN/USDT','OCEAN/USDT',
                    'OGN/USDT','OMG/USDT','ONE/USDT','ONT/USDT',
                    'PEOPLE/USDT','QTUM/USDT','RAY/USDT','REEF/USDT',
                    'REN/USDT','RLC/USDT','ROSE/USDT','RSR/USDT',
                    'RUNE/USDT','RVN/USDT','SAND/USDT',
                    'SFP/USDT','SKL/USDT','SNX/USDT','SOL/USDT',
                    'SRM/USDT','STMX/USDT','STORJ/USDT','SUSHI/USDT',
                    'SXP/USDT','THETA/USDT','TLM/USDT','TOMO/USDT',
                    'TRB/USDT','TRX/USDT','UNFI/USDT','UNI/USDT',
                    'VET/USDT','WAVES/USDT','WOO/USDT','XEM/USDT',
                    'XLM/USDT','XMR/USDT','XRP/USDT','XTZ/USDT',
                    'YFI/USDT','ZEC/USDT','ZEN/USDT','ZIL/USDT',
                    'ZRX/USDT'
                    ]
        for symbol in mysymbol:
            for time_interval in ['1h']:
                print(exchange.id, symbol, time_interval)
                try:
                    save_spot_candle_data_from_exchange(exchange, symbol, time_interval, start_time, path)
                except Exception as e:
                    print(e)
                    error_list.append('_'.join([exchange.id, symbol, time_interval]))

    print(error_list)

```

The next step is to merge the acquired data, per coin, into one big table, still with everything undecided FOR loop merging:

```python
import pandas as pd
import os
from datetime import timedelta
from config import *

pd.set_option('expand_frame_repr', False) 
file_location = root_path + '/0_数据更新/coin_database/binance/spot'
symbol_name = ['1INCH-USDT','AAVE-USDT',
                'ADA-USDT','ALGO-USDT','ALICE-USDT','ALPHA-USDT',
                'ANKR-USDT','ANT-USDT','APE-USDT','ARPA-USDT',
                'API3-USDT','ATA-USDT','ATOM-USDT',
                'AUDIO-USDT','AVAX-USDT','AXS-USDT','BAKE-USDT',
                'BAL-USDT','BAND-USDT','BAT-USDT','BCH-USDT',
                'BEL-USDT','BLZ-USDT','BNB-USDT','BNX-USDT',
                'BTC-USDT','BTS-USDT','C98-USDT',
                'CELO-USDT','CELR-USDT','CHR-USDT','CHZ-USDT',
                'COMP-USDT','COTI-USDT','CRV-USDT','CTK-USDT',
                'CTSI-USDT','CVC-USDT','DAR-USDT','DASH-USDT',
                'DENT-USDT','DGB-USDT','DOGE-USDT',
                'DOT-USDT','DUSK-USDT','DYDX-USDT','EGLD-USDT',
                'ENJ-USDT','ENS-USDT','EOS-USDT','ETC-USDT',
                'ETH-USDT','FIL-USDT','FLM-USDT','FLOW-USDT',
                'FTM-USDT','FTT-USDT','GALA-USDT','GAL-USDT',
                'GMT-USDT','GRT-USDT','GTC-USDT','HBAR-USDT',
                'HNT-USDT','HOT-USDT','ICP-USDT','ICX-USDT',
                'IMX-USDT','IOST-USDT','IOTA-USDT','IOTX-USDT',
                'JASMY-USDT','KAVA-USDT','KLAY-USDT','KNC-USDT',
                'KSM-USDT','LINA-USDT','LINK-USDT','LIT-USDT',
                'LPT-USDT','LRC-USDT','LTC-USDT','MANA-USDT',
                'MASK-USDT','MATIC-USDT','MKR-USDT','MTL-USDT',
                'NEAR-USDT','NEO-USDT','NKN-USDT','OCEAN-USDT',
                'OGN-USDT','OMG-USDT','ONE-USDT','ONT-USDT',
                'PEOPLE-USDT','QTUM-USDT','RAY-USDT','REEF-USDT',
                'REN-USDT','RLC-USDT','ROSE-USDT','RSR-USDT',
                'RUNE-USDT','RVN-USDT','SAND-USDT',
                'SFP-USDT','SKL-USDT','SNX-USDT','SOL-USDT',
                'SRM-USDT','STMX-USDT','STORJ-USDT','SUSHI-USDT',
                'SXP-USDT','THETA-USDT','TLM-USDT','TOMO-USDT',
                'TRB-USDT','TRX-USDT','UNFI-USDT','UNI-USDT',
                'VET-USDT','WAVES-USDT','WOO-USDT','XEM-USDT',
                'XLM-USDT','XMR-USDT','XRP-USDT','XTZ-USDT',
                'YFI-USDT','ZEC-USDT','ZEN-USDT','ZIL-USDT',
                'ZRX-USDT'
               ]
for i in symbol_name:
    file_list = []
    for root, dirs, files in os.walk(file_location):
        for filename in files:
            if filename.startswith(i):
                file_path = os.path.join(root, filename)
                file_path = os.path.abspath(file_path)
                file_list.append(file_path)
                print(file_path)
    data = pd.DataFrame()
    for fp in sorted(file_list)[:]:
        df = pd.read_csv(fp, encoding='gbk')
        df['symbol'] = i
        df['candle_begin_time'] = pd.to_datetime(df['candle_begin_time'])
        df['candle_begin_time'] = df['candle_begin_time'] + timedelta(hours=8)
        data = data.append(df, ignore_index=True) 
    data.reset_index(drop=False, inplace=False)
    print(data)
    data.to_csv(root_path + '/1_策略实盘/data/Coin/swap_candle/%s.csv' % i, mode='w')

```

## Live output

Before outputting the live orders, you need to change the end_date in Config.py in the Hillel framework to the time of day, and then you can rely on reading the PKL file to output the live orders.

Depending on the strategy, the final output will look like this:

![901689](https://s2.loli.net/2023/10/27/BSUo6OYpGKQfLqZ.png)

1. When I saw the signal I backhanded an open short and poked it right in! [img](https://bbs.quantclass.cn/emoji/qq/ku.gif)

   ## Summary

   From the acquisition of data, to the output of real trading manually complete the opening position, a set of down my computer within about 8 minutes (8 generations of low-pressure U brain power is not enough! [img](https://bbs.quantclass.cn/emoji/qq/kuaikule.gif)), because the hold is usually more than 12 hours, the total seems quite feasible.

   It is said that the sharing will be a lot more bullish real trading a single piece of open order practice, I manually open a single belongs to the "can run on the line", I heard that the follow-up Xingda will also talk about the real code, very much looking forward to! [img](https://bbs.quantclass.cn/emoji/qq/aixin.gif).

   ps: the code is in the attachment, because the maximum can only transfer 25M, so the data I deleted, if you still want to use the back test, refer to the basic course, appropriate modification of the code will be able to get the past data.

   ------

   ## Updated June 21st

   ### There are a few problems in the current strategy

   - **There is an 8-hour time lag in obtaining data for B

   **The +8h approach was used in the past courses, but after actually using it I found that it's better to run it at 8am or 4pm as Xing Da said in the live broadcast*

   - **Manually da baa every night, putting the server is more appropriate***

   If it's our time zone, manually turn on the computer at 0pm every night to run it can still be realized, but turn on the computer at 8am every morning to run it manually and conflict with the work schedule. 

   - **Server capacity must be large, or manually clear a part of the useless data at regular intervals **

   * server at the same time need to assume a certain data storage function, if the time period of 24H, the parameters involved in hundreds, thousands of K line, it will require a fairly long time of data storage. 

   ### Solution ideas

   1. put the framework on the server
   2. transfer the results of the real trading output by pinning them out
   3. Optimize the framework a bit more, try to be as short as possible, only about the content of the real market
   4. Receive the pinned message to open and close positions manually every day (code has been attached)

## code file

[downloads](https://github.com/zqwuming/blogimage/blob/main/asset/B%E5%9C%88%E4%BA%8B%E4%BB%B6%E7%AD%96%E7%95%A5%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%89%88.zip)
