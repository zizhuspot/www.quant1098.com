---
title: Automated Coin Selection Script Takes You to a Manual Live Coin Grid
date: 2023-10-20 23:07:00
categories:
  - quantitative Trading
tags:
  - Digital currency
  - Automated
  - Quantitative tool
  - Coin
  - digiccy
  - quant1098.com
  - Grid
  - binance
description: 
cover: https://s2.loli.net/2023/10/20/hZE1Dj65KRl4axU.gif
---

# Automated Coin Selection Script Takes You to a Manual Live Coin Grid

After tonight's BTC pin market quite a few students in the grid group group to share real disk screenshots, make everyone itchy to try. In fact, I have long planned to play in the real disk, see everyone eat meat after no longer wait, stay up all night also want to hurry to take advantage of the market to come immediately after the run up.

## Sources of grid parameters

The grid was created along the same lines as the parameters utilized in the backtesting framework, and also to fit the backtesting results as closely as possible

| Main parameters of the isometric grid |       |                           |
| ------------------------------------- | ----- | ------------------------- |
| Name                                  | Value | Remarks                   |
| Upper Boundary  Parameters            | 0.5   | Closing price * (1 + 0.5) |
| Lower Boundary  Parameters            | 0.5   | Closing price * (1 - 0.5) |
| Number of Grids                       | 90    |                           |
| Take Profit Ratio                     | 2%    |                           |
| Stop Loss Ratio                       | 5%    |                           |

## Code Functions

What this script can help you accomplish is

- Read the real-time K-line data of CoinShares
- Calculate the ranking according to the set factors
- Print out the relevant grid setup parameters

### Something that needs to be done by the owner himself:

- Follow the printed parameters to CoinSafe to set
- **Stop Loss and Take Profit! Stop Loss and Take Profit! Stop Loss and Take Profit! Important things to say three times, the code will only give the termination price, but did not realize the take-profit 2% and stop-loss 5%, if you run up after the real disk found to have reached the stop-loss line, you need to stop their own grid!**

## Code Usage

The code is divided into three folders, you only need to focus on `grid/config.py`. First open `grid/config.py`

```python

get_kline_num = 10
select_coin_num = 1
time_interval = '12H'
time_offset = '10H'
black_list = ['COCOS', 'BTCST', 'DREP', 'SUN', 'BTCDOM', 'DEFI', "BTCBUSD", "ETHBTC"]  #

factors = [
    ('ret', True, [1], 0.5),
    ('zhenfu', False, [2], 0.5),
]
_factor_col_list = set()
for factor_name, _, parameter_list, _ in factors:
    _factor_col_list.add(f'{factor_name}_{str(parameter_list)}')
factor_col_list = list(_factor_col_list)

```

### Setting time_offset

**time_offset** defines when you plan to open the grid manually each day, for example, according to the backtest setting is to run each grid for 12 hours, so I plan to open it every day at 10:00 am and 10:00 pm, then the definition of time_offset = '10H'
### Setting factors

**factors** should be better understood, that is, what factors and its corresponding parameters are used in the backtesting framework, such as ret_1 and zhenfu_2 used in my backtesting.

### Realizing Factors

The factors that appear in factors, then you need to add the corresponding factor calculation code in the factor directory, such as factor/ret.py, which looks like this

```python
def signal(df, params, factor_name):
    n = params[0]

    df[factor_name] = df['close'].pct_change(n)

    return df

```

## run production.py

The program picks all the perpetual contracts that are being traded in the entire market, which takes about a few minutes, and outputs

```
Finished organizing & announcing coin selection data, time spent: 0:00:01.699858
Coin Selection Results. 
     candle_begin_time symbol close zhenfu_[2] ret_[1] factor ret_[1]_rank zhenfu_[2]_rank rank
8 2023-10-16 10:00:00 LOOMUSDT 0.3184 0.332644 -0.103351 2.0 1.0 3.0 1.0
-------------------- Grid parameters --------------------
Contract: LOOMUSDT
Lowest price: 0.16545
Maximum price: 0.49635000000000007
Number of grids: 90
Termination minimum price: 0.16445
Maximum price: 0.49735000000000007

Process finished with exit code 0
```

Then just follow this to open the appropriate grid

![2023-10-20_232039.png](https://s2.loli.net/2023/10/20/tlTruDcUxSWFpBY.png)

## Structure of the document

![2023-10-20_233118.png](https://s2.loli.net/2023/10/20/BXoHij2OIA3stZz.png)

### source code

`binance.py`

```python
import json
import math
import time
import traceback
from datetime import timedelta

import ccxt
import pandas as pd
from tqdm import tqdm

from common.utils import retry_wrapper

exchange = ccxt.binance()
exchange.https_proxy = 'http://127.0.0.1:7890/'
exchange.timeout = 3000

def fetch_account_balance(enable_retry=False):
    account_data = retry_wrapper(
        exchange.fapiPrivateV2GetAccount,
        func_name='fapiPrivateV2GetAccount',
        enable_retry=enable_retry,
    )
    assets = pd.DataFrame(account_data['assets'])
    return float(assets[assets['asset'] == 'USDT']['walletBalance'])


def fetch_positions(enable_retry=False):
    position_data = retry_wrapper(
        exchange.fapiPrivateV2GetPositionRisk,
        func_name='fapiPrivateV2GetPositionRisk',
        enable_retry=enable_retry,
    )

    df = pd.DataFrame(position_data)
    columns = {
        'positionAmt': '当前持仓量',
        'entryPrice': '持仓均价',
        'markPrice': '当前价格',
        'unRealizedProfit': '持仓收益',
    }

    df.rename(columns=columns, inplace=True)
    df = df.astype({
        '当前持仓量': float,
        '持仓均价': float,
        '当前价格': float,
        '持仓收益': float,
    })
    df = df[df['当前持仓量'] != 0]
    df.set_index('symbol', inplace=True)

    df = df[['当前持仓量', '持仓均价', '当前价格', '持仓收益']]

    return df


def fetch_candle_data(symbol, end_time, time_interval, limit, enable_retry=False):
    
    start_time_dt = end_time - pd.to_timedelta(time_interval) * limit
    params = {
        'symbol': symbol,
        'interval': time_interval,
        'limit': limit,
        'startTime': int(start_time_dt.timestamp() * 1000)
    }
    try:
        kline_data = retry_wrapper(
            exchange.fapiPublicGetKlines,
            params,
            func_name='fapiPublicGetKlines',
            enable_retry=enable_retry,
        )
    except Exception as e:
        print(traceback.format_exc())
        return pd.DataFrame()

    df = pd.DataFrame(kline_data).astype(float)
    columns = {
        0: 'candle_begin_time',
        1: 'open',
        2: 'high',
        3: 'low',
        4: 'close',
        5: 'volume',
        6: 'close_time',
        7: 'quote_volume',
        8: 'trade_num',
        9: 'taker_buy_base_asset_volume',
        10: 'taker_buy_quote_asset_volume',
        11: 'ignore',
    }
    df.rename(columns=columns, inplace=True)
    df['symbol'] = symbol
    df.sort_values(by=['candle_begin_time'], inplace=True)
    df.drop_duplicates(subset=['candle_begin_time'], keep='last', inplace=True)
    df.reset_index(drop=True, inplace=True)

    return df


def fetch_all_candle_data(symbol_list, run_time, time_interval, limit, enable_retry=False):
    symbol_candle_data = {}
    for symbol in tqdm(symbol_list):
        df = fetch_candle_data(symbol, run_time, time_interval, limit, enable_retry)
        if df.empty:
            continue

        # 转换时区
        utc_offset = int(time.localtime().tm_gmtoff / 60 / 60)
        df['candle_begin_time'] = pd.to_datetime(df['candle_begin_time'], unit='ms') + timedelta(hours=utc_offset)

        # 删除未走完的那根K线
        df = df[df['candle_begin_time'] < run_time]

        symbol_candle_data[symbol] = df

    return symbol_candle_data


def fetch_ticker_price(enable_retry=False):
    ticker_data = retry_wrapper(
        exchange.fapiPublicGetTickerPrice,
        func_name='fapiPublicGetTickerPrice',
        enable_retry=enable_retry,
    )
    tickers = pd.DataFrame(ticker_data).astype({'price': float, 'time': float})
    tickers.set_index('symbol', inplace=True)

    return tickers['price']


def load_market():
    
    exchange_data = retry_wrapper(
        exchange.fapiPublicGetExchangeInfo,
        func_name='fapiPublicGetExchangeInfo',
    )

    symbol_dict_list = exchange_data['symbols']
    df_list = []
    for symbol_info in symbol_dict_list:
        symbol = symbol_info['symbol']
        df_data = {
            'symbol': symbol,
            'onboardDate': int(symbol_info['onboardDate']),
            'status': symbol_info['status'],
            'quoteAsset': symbol_info['quoteAsset'],
            'contractType': symbol_info['contractType'],
        }

        for _filter in symbol_info['filters']:
            if _filter['filterType'] == 'PRICE_FILTER':
                df_data['pricePrecision'] = int(math.log(float(_filter['tickSize']), 0.1))
            if _filter['filterType'] == 'LOT_SIZE':
                df_data['minQuantity'] = int(math.log(float(_filter['minQty']), 0.1))
            if _filter['filterType'] == 'MIN_NOTIONAL':
                df_data['minNotional'] = float(_filter['notional'])

        df_list.append(df_data)

    df = pd.DataFrame(df_list)
    df.set_index('symbol', inplace=True)

    return df


def place_order(symbol_order, symbol_market_info, enable_retry=False, enab
    order_params = []
    symbol_ticker_price = fetch_ticker_price(enable_retry)

    for symbol, row in symbol_order.iterrows():
        min_qty = symbol_market_info.at[symbol, 'minQuantity']
        min_notional = symbol_market_info.at[symbol, 'minNotional']
        price_precision = symbol_market_info.at[symbol, 'pricePrecision']

        if pd.isna(min_qty) or pd.isna(min_notional) or pd.isna(price_precision) :
            raise Exception('当前币种没有最小下单精度或者最小价格精度，币种信息异常')

        quantity = row['实际下单量']
        quantity = round(quantity, min_qty)
        # 根据订单的买卖方向以一定滑点确定下单价格
        if quantity > 0:
            side = 'BUY'
            price = symbol_ticker_price[symbol] * 1.015
        else:
            side = 'SELL'
            price = symbol_ticker_price[symbol] * 0.985
        quantity = abs(quantity)
        price = round(price, price_precision)
        reduce_only = (row['交易模式'] == '清仓')

        # 下单金额小于5块交易所不接受
        if quantity * price < min_notional:
            # 清仓允许比5U少的单
            if not reduce_only:
                print(symbol, '交易金额小于最小下单金额，跳过该笔交易')
                print('下单量: ', quantity, '价格: ', price, '最小下单金额: ', min_notional)
                continue

        params = dict()
        params['symbol'] = symbol
        params['type'] = 'LIMIT'
        params['timeInForce'] = 'GTC'
        params['newClientOrderId'] = str(time.time())
        params['side'] = side
        params['price'] = str(price)
        params['quantity'] = str(quantity)
        params['reduceOnly'] = str(reduce_only)
        order_params.append(params)

    print('每个币种的下单参数: ', order_params)
    order_results = []

    if not enable_place_order:
        return order_params, order_params

    for i in range(0, len(order_params), 5):
        order_params = order_params[i:i+5]
        try:
            result = retry_wrapper(
                exchange.fapiPrivatePostBatchOrders,
                params={'batchOrders': json.dumps(order_params)},
                func_name='fapiPrivatePostBatchOrders',
                enable_retry=enable_retry,
            )
            print('批量下单完成，批量下单结果: ', result)
            order_results += result
        except Exception as e:
            print(e)
            continue

    return order_params, order_results



```

`utils.py`

```python
import time
import pandas as pd
from datetime import datetime, timedelta
from math import floor


def retry_wrapper(func, params={}, func_name='', retry_times=5, sleep_seconds=5, enable_retry=True):

    if not enable_retry:
        return func(params)

    for _ in range(retry_times):
        try:
            return func(**params)
        except Exception as e:
            print(f'{func_name} 报错，错误信息：{e}')
            time.sleep(sleep_seconds)
    else:
        raise Exception(f'{func_name} 报错次数达到上限，程序退出')


def next_run_time(time_interval, ahead_seconds=5, cheat_seconds=0):
    
    if time_interval.endswith('m') or time_interval.endswith('h'):
        pass
    elif time_interval.endswith('T'):
        time_interval = time_interval.replace('T', 'm')
    elif time_interval.endswith('H'):
        time_interval = time_interval.replace('H', 'h')
    else:
        print('time_interval格式不符合规范。程序退出')
        exit()

    ti = pd.to_timedelta(time_interval)
    now_time = datetime.now()
    # 计算当日时间的零时 00：00：00
    this_midnight = now_time.replace(hour=0, minute=0, second=0, microsecond=0)
    # 此时距零时已过去多久
    time_passed = now_time - this_midnight
    # 将过去的时间转换为以 time_interval 为单位，那么下一个单位就是下次运行时间
    next_passed = floor(time_passed.total_seconds() / ti.total_seconds()) + 1

    next_time = this_midnight + timedelta(seconds=next_passed * ti.total_seconds())
    if (next_time - datetime.now()).seconds < ahead_seconds:
        next_time += ti

    if cheat_seconds != 0:
        next_time = next_time - timedelta(seconds=cheat_seconds)

    return next_time


def sleep_until_run_time(time_interval, ahead_seconds=1, if_sleep=True, cheat_seconds=0):

    run_time = next_run_time(time_interval, ahead_seconds, cheat_seconds)
    if if_sleep:
        time.sleep(max(0, (run_time - datetime.now()).total_seconds()))
        while True:
            if datetime.now() > run_time:
                break

    return run_time

```

`ret.py`

```python
def signal(df, params, factor_name):
    n = params[0]

    df[factor_name] = df['close'].pct_change(n)

    return df
```

`zhenfu.py`

```python
def signal(df, params, factor_name):
    n = params[0]

    high = df[f'high'].rolling(n, min_periods=1).max()
    low = df[f'low'].rolling(n, min_periods=1).min()
    df[factor_name] = high / (low + 0.0001) - 1

    return df
```

`config.py`

```python

get_kline_num = 10

select_coin_num = 1

time_interval = '12H'

time_offset = '9H'

black_list = ['COCOS', 'BTCST', 'DREP', 'SUN', 'BTCDOM', 'DEFI', "BTCBUSD", "ETHBTC"]  # 
factors = [
    ('ret', True, [1], 0.5),
    ('zhenfu', False, [2], 0.5),
]
_factor_col_list = set()
for factor_name, _, parameter_list, _ in factors:
    _factor_col_list.add(f'{factor_name}_{str(parameter_list)}')
factor_col_list = list(_factor_col_list)
```

`production.py`

```python
from datetime import datetime
import pandas as pd
from api.binance import load_market
from api.binance import fetch_all_candle_data
from api.binance import fetch_ticker_price
from config import *

pd.set_option('expand_frame_repr', False)
pd.set_option('display.max_rows', 5000)
pd.set_option('display.unicode.ambiguous_as_wide', True)
pd.set_option('display.unicode.east_asian_width', True)


def filter_symbol_list(symbol_market_info):
    """
    :param symbol_market_info:
    :return:
    """
    now_ms = datetime.now().timestamp() * 1000
    result = []
    for symbol, row in symbol_market_info.iterrows():
        # 上线时间大于等于3天
        if (now_ms - row['onboardDate']) / 1000 / (60 * 60 * 24) < 3:
            continue
        if row['status'] != 'TRADING':
            continue
        if row['quoteAsset'] != 'USDT':
            continue
        if row['contractType'] != 'PERPETUAL':
            continue
        if symbol in black_list:
            continue

        result.append(symbol)

    return result


def calculate_factors(df):
    for factor, if_ascending, parameter_list, weight in factors:
        factor_name = f'{factor}_{str(parameter_list)}'
        _cls = __import__(f'factor.{factor}', fromlist=('',))
        df = getattr(_cls, 'signal')(df, parameter_list, factor_name)

    df = df[['candle_begin_time', 'symbol', 'close'] + factor_col_list]
    return df


def calculate_ranking_factor(df):
    df['因子'] = 0
    for factor, if_ascending, parameter_list, weight in factors:
        factor_name = f'{factor}_{str(parameter_list)}'
        factor_rank = factor_name + '_rank'
        df[factor_rank] = df.groupby('candle_begin_time')[factor_name].rank(ascending=if_ascending)
        df['因子'] += df[factor_rank] * weight

    return df


def calculate_factors_and_select_coin(symbol_candle_data, run_time):
    """
    :param symbol_candle_data:
    :param run_time:
    :return:
    """
    symbol_list = list(symbol_candle_data.keys())
    for symbol in symbol_list:
        df = symbol_candle_data[symbol].resample(time_interval, on='candle_begin_time', offset=time_offset).agg({
            'open': 'first',
            'close': 'last',
            'high': 'max',
            'low': 'min',
            'volume': 'sum',
            'quote_volume': 'sum',
            'trade_num': 'sum',
            'taker_buy_base_asset_volume': 'sum',
            'taker_buy_quote_asset_volume': 'sum',
            'symbol': 'last',
        })
        df.reset_index(drop=False, inplace=True)
        # 删除最后一根未走完的k线
        df = df.iloc[:-1]
        # 删除该币种成交量=0的k线
        df = df[df['volume'] > 0]
        # 若该币种的k线数量太少，不满足要求，直接删除这个币种的数据
        if len(df) < get_kline_num:
            del symbol_candle_data[symbol]
        else:
            symbol_candle_data[symbol] = df

    df_list = []
    for df in symbol_candle_data.values():
        df = calculate_factors(df)
        df_list.append(df)

    df = pd.concat(df_list, ignore_index=True)
    df.dropna(subset=factor_col_list, inplace=True)
    df.sort_values(by=['candle_begin_time', 'symbol'], inplace=True)
    df.reset_index(drop=True, inplace=True)

    df = calculate_ranking_factor(df)

    df['rank'] = df.groupby('candle_begin_time')['因子'].rank(method='first')
    df.sort_values(by=['candle_begin_time', 'rank'], inplace=True)
    df = df.groupby('candle_begin_time').head(select_coin_num)
    print(df)

    df.reset_index(drop=True, inplace=True)
    last_candle_time = df.iloc[-1]['candle_begin_time']
    df = df[df['candle_begin_time'] == last_candle_time]

    return df


if __name__ == '__main__':
    symbol_market_info = load_market()
    #symbol_market_info = pd.read_pickle('symbol_market_info.pkl')
    symbol_list = filter_symbol_list(symbol_market_info)
    #symbol_list = symbol_list[:10]

    start_time = datetime.now()
    symbol_candle_data = fetch_all_candle_data(symbol_list, start_time, '1h', get_kline_num * int(time_interval[:-1]))
    print('获取所有币种K线数据完成，花费时间:', datetime.now() - start_time)

    start_time = datetime.now()
    select_coin = calculate_factors_and_select_coin(symbol_candle_data, start_time)
    print('完成选币数据整理 & 宣布，花费时间:', datetime.now() - start_time)
    print('选币结果: \n', select_coin)

    symbol = select_coin['symbol'].iloc[0]
    tickers = fetch_ticker_price()
    print('-' * 20, ' 网格参数 ', '-' * 20)
    print('合约: ', symbol)
    print('最低价格: ', tickers[symbol] * 0.5)
    print('最高价格: ', tickers[symbol] * 1.5)
    print('网格数量：', 90)
    print('终止最低价格: ', tickers[symbol] * 0.5 - 0.001)
    print('终止最高价格: ', tickers[symbol] * 1.5 + 0.001)
```

