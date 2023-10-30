---
title: Multi-Period Resonance Strategy based on Ichimoku Technical Indicator
date: 2023-10-30 10:07:00
categories:
  - Quantitative Trading
tags:
  - Multi-Period
  - Resonance
  - Quantitative tool
  - Strategy
  - Technical
  - quant1098.com
  - Ichimoku
  - AIOQuant
description: This paper describes a signal generation strategy based on the Ichimoku technical indicator of all cycle resonance, which uses the simultaneous occurrence of long or short alignments in the 15m, 1H and 4H cycles as a signal to open a position. The backtest and live code are attached at the end of this article.
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

**Abstract**

This paper describes a signal generation strategy based on the Ichimoku technical indicator of all cycle resonance, which uses the simultaneous occurrence of long or short alignments in the 15m, 1H and 4H cycles as a signal to open a position. The backtest and live code are attached at the end of this article.

**Keywords**: Ichimoku Ichimoku, multi-cycle resonance

**What is Ichimoku Equilibrium? **

The Ichimoku Equilibrium (also known as the Japanese Cloud Chart Indicator) is a **technical** similar to moving averages, but it has a unique way of showing the balance of current market conditions, including support and resistance levels, as well as implied momentum.

European and American traders use this indicator more often, especially in the Forex market. The digital currency market has been backtested by me, and six months of live trading experience tells me that this strategy also works.

**Calculation of the Ichimoku Indicator**

The Ichimoku chart has 5 sets of parameters that provide a clear and simple trend determination based on the high and low prices of each long and short period (the length of the period can be changed at will).The 5 sets of parameters are as follows:

***Conversion Line (Tenkan-Sen / Conversion Line)***
 Short-axis fast line = (Highest price in 9 days + Lowest price in 9 days) ÷ 2, with a short period of 9 days.
 ***Base Line (Kijun-Sen / Base Line)***
 Pivot Line = (26-day high + 26-day low) ÷ 2, with 26 days as a medium-term cycle.
 *** Late Moving Band (Chinkou Span / Lagging Span)***
 Backwardation Indicator (Backwardation Time) = Move today's closing price backward by one Medium Term Cycle (26 days).
 ***Forward Span A (Senkou Span A / Leading Span A)****
 Leading Span A (Leading Time A) = (Conversion Line + Baseline) ÷ 2 to move forward one mid-period.
 ***Forward Span B (Senkou Span B / Leading Span** B)*
 Leading Span B (Leading Time B) = (52-Day High + 52-Day Low) ÷ 2, shifted forward to one mid-period.
 ***Cloud Bands / Clouds (Kumo / Cloud)***
 Cloud Band (Resistance Band, Impedance Band) = Area between Prior Band A and Prior Band B.

Ichimoku's 5 lines are shown below on the chart:

![487999](https://s2.loli.net/2023/10/30/WqUDgQCZ8iKvYlH.png)

**Usage of Ichimoku Ichimoku**

Ichimoku is used in a variety of ways, not only to provide trend judgment, but also to provide signals to open positions. For trend traders, if the K line is above the cloud chart, it indicates a long trend and you can take a long position. On the other hand, if the K-line is below the cloud chart, it indicates a short trend, so you can take the opportunity to go short.

Regarding position opening signals, I will introduce the most commonly used one: in a long market, the K-line is above the cloud, when Tenkan sen crosses Kijun sen to open a long position; on the contrary, when in a short market, the K-line is below the cloud, when Tenkan sen crosses, to go short.  Of course, you can also add some restrictions, such as long when the corresponding Chikou span in the cloud, short when the cloud below.

Other opening signals please bosses to research by themselves.

**Multi-cycle resonance**

Multi-cycle resonance, that is, different cycles (usually more than 4 times the cycle) at the same time issued a long or short signal. Multi-cycle resonance is very helpful in improving the quality of signals. My live strategy uses the 15m K-line, and then the signals from the 1H and 4H K-lines as confirmation.

The backtest and live code is shown below, the code is my actual live code, there should be no error when running. The strategy works pretty well on ETH, so if you're interested, please backtest it yourself.

def produce_ichimoku_add_end_time () This function is to organize the original df matrix, add an equalization parameter, and at the same time add a column of candle_end_time, so that a number of multiple cycles of signals are displayed in a matrix at the same time.

def real_signal_multiple_timeframe_ichimoku_15m () is the function that actually generates the signal.

Another thing to remind you bosses is that this strategy has a take-profit and stop-loss, when you open a position, set the take-profit and stop-loss, stop loss stop_loss_price, stop gain stop_gain_price, which is based on the price of the open position and the profit/loss ratio to determine. If you have any questions, please feel free to send me a private message. I hope to improve this strategy together.

```python
import pandas as pd
import numpy as np
from tapy import Indicators
import talib as ta

def produce_ichimoku_add_end_time(df, para = [9, 26, 52]):
    period_tenkan_sen = para[0]
    period_kijun_sen = para[1]
    period_senkou_span_b = para[2]

    df.rename(columns={
        'close': 'Close',
        'open': 'Open',
        'high': 'High',
        'low': 'Low',
        'volume': 'Volume'
    }, inplace=True)
    #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #  #
    i = Indicators(df)
    i.ichimoku_kinko_hyo(period_tenkan_sen=period_tenkan_sen, period_kijun_sen=period_kijun_sen, period_senkou_span_b=period_senkou_span_b,
                                       column_name_chikou_span='chikou_span', column_name_tenkan_sen='tenkan_sen',
                                       column_name_kijun_sen='kijun_sen', column_name_senkou_span_a='senkou_span_a',
                                       column_name_senkou_span_b='senkou_span_b')

    df = i.df

    df.rename(columns={
        'Close': 'close',
        'Open': 'open',
        'High': 'high',
        'Low': 'low',
        'Volume': 'volume'
    }, inplace=True)
    interval = df['candle_begin_time_GMT8'].iat[1] - df['candle_begin_time_GMT8'].iat[0]
    df['candle_end_time'] = df['candle_begin_time_GMT8'] + interval
    return df

def real_signal_multiple_timeframe_ichimoku_15m(df_list, now_pos, avg_price, para=[1.5, 9, 26]):

    df = df_list[0]  
    df_1h = df_list[1]  
    df_4h = df_list[2]

    now_pos = float(now_pos)
    avg_price = float(avg_price)
    win_loss_ratio = para[0]
    period_tenkan_sen = para[1]
    period_kijun_sen = para[2]


    df_4h = produce_ichimoku_add_end_time(df_4h, para = [period_tenkan_sen, period_kijun_sen, 52])
    df_4h['ema200'] = ta.EMA(df_4h['close'], 200)

    condition1 = (df_4h['close'] > df_4h[["senkou_span_a", "senkou_span_b"]].max(axis=1))
    condition2 = (df_4h['close'] > df_4h['ema200'])

    df_4h.loc[condition1 & condition2 , 'signal_long'] = 1

    condition1 = (df_4h['close'] <= df_4h[["senkou_span_a", "senkou_span_b"]].max(axis=1))
    df_4h.loc[condition1, 'signal_long'] = 0


    condition1 = (df_4h['close'] < df_4h[["senkou_span_a", "senkou_span_b"]].min(axis=1))
    condition2 = (df_4h['close'] < df_4h['ema200'])
    df_4h.loc[condition1 & condition2 , 'signal_short'] = -1

    df_4h['signal_4h'] = df_4h[['signal_long', 'signal_short']].sum(axis=1, min_count=1, skipna=True)
    df_4h['signal_4h'].fillna(0, inplace=True)
    df_4h.drop(['open','high', 'low', 'close', 'volume', 'tenkan_sen', 'kijun_sen', 'senkou_span_a',
                'senkou_span_b', 'chikou_span', 'signal_long', 'signal_short'], axis=1, inplace=True)

    df_1h = produce_ichimoku_add_end_time(df_1h, para = [period_tenkan_sen, period_kijun_sen, 52])
    condition1 = (df_1h['close'] > df_1h[["senkou_span_a", "senkou_span_b"]].max(axis=1))
    condition2 = (df_1h["senkou_span_a"] > df_1h["senkou_span_b"])
    condition3 = (df_1h['close'] > df_1h['tenkan_sen'])
    df_1h.loc[condition1 & condition2 & condition3, 'signal_long'] = 1

    condition1 = (df_1h['close'] <= df_1h[["senkou_span_a", "senkou_span_b"]].max(axis=1))
    condition2 = (df_1h["senkou_span_a"] <= df_1h["senkou_span_b"])
    df_1h.loc[condition1 & condition2, 'signal_long'] = 0

    condition1 = (df_1h['close'] < df_1h[["senkou_span_a", "senkou_span_b"]].min(axis=1))
    condition2 = (df_1h["senkou_span_a"] < df_1h["senkou_span_b"])
    condition3 = (df_1h['close'] < df_1h['tenkan_sen'])
    df_1h.loc[condition1 & condition2 & condition3, 'signal_short'] = -1

    df_1h['signal_1h'] = df_1h[['signal_long', 'signal_short']].sum(axis=1, min_count=1, skipna=True)
    # print(df_1h)
    df_1h['signal_1h'].fillna(0, inplace=True)
    df_1h.drop(['open', 'high', 'low', 'close', 'volume', 'tenkan_sen', 'kijun_sen', 'senkou_span_a',
                'senkou_span_b', 'chikou_span', 'signal_long', 'signal_short'], axis=1, inplace=True)


    df = produce_ichimoku_add_end_time(df, para=[9, 26, 52])



    df = pd.merge(df, df_4h[['signal_4h',  'candle_end_time']], how='left', on='candle_end_time',
                  suffixes=('', '_4h'))
    df = pd.merge(df, df_1h[['signal_1h',  'candle_end_time']], how='left', on='candle_end_time',
                  suffixes=('', '_1h'))
    df['signal_4h'].fillna(method='ffill', inplace=True)
    df['signal_4h'] = df['signal_4h'].shift(periods = 1, axis=0)
    df['signal_4h'].fillna(0, inplace=True)

    df['signal_1h'].fillna(method='ffill', inplace=True)
    df['signal_1h'] = df['signal_1h'].shift(periods = 1, axis=0)
    df['signal_1h'].fillna(0, inplace=True)


    df['SAR_15m'] = ta.SAR(df['high'], df['low'], acceleration=0.02, maximum=0.2) 
    condition1 = (df['close'] > df[["senkou_span_a", "senkou_span_b"]].max(axis=1))

    condition2 = ((df['tenkan_sen'] > df['kijun_sen']) & (df['tenkan_sen'].shift(1) < df['kijun_sen'].shift(1)))
    condition3 = (df['close'] > df['SAR_15m'])
    condition4 = ((df['signal_4h'] +  df['signal_1h']) ==2)
    condition5 = (df['chikou_span'].shift(26) > (df[["senkou_span_a", "senkou_span_b"]].max(axis=1)).shift(26))
    condition6 = (abs((df['close']  - df['senkou_span_b'] )/df['close'])<0.15)
    df.loc[condition1 & condition2 & condition3 & condition4 & condition5 & condition6, 'signal_long'] = 1

    condition1 = (df['close'] < df[["senkou_span_a", "senkou_span_b"]].min(axis=1))
    # condition2 = ((df['close'] < df['kijun_sen']) & (df['close'].shift(1) > df['kijun_sen'].shift(1)))
    condition2 = ((df['tenkan_sen'] < df['kijun_sen']) & (df['tenkan_sen'].shift(1) > df['kijun_sen'].shift(1)))
    condition3 = (df['close'] < df['SAR_15m'])
    condition4 = ((df['signal_4h'] +  df['signal_1h']) == -2)
    condition5 = (df['chikou_span'].shift(26) < (df[["senkou_span_a", "senkou_span_b"]].min(axis=1)).shift(26))
    condition6 = (abs((df['close']  - df['senkou_span_b'] )/df['close'])<0.15)
    df.loc[condition1 & condition2 & condition3 & condition4 & condition5 & condition6, 'signal_short'] = -1

    info_dic = {'pre_signal': 0, 'stop_loss_price': None, 'stop_gain_price': None}

    for i in range(df.shape[0]):
        if info_dic['pre_signal'] == 0:
            if (df.at[i, 'signal_long'] == 1):
                df.at[i, 'signal'] = 1 
                stop_loss_price = df.at[i, 'senkou_span_b']  *(1-0.02)
                stop_gain_price = df.at[i, 'close'] + (df.at[i, 'close'] - df.at[i, 'senkou_span_b']) * win_loss_ratio
                pre_signal = 1
                info_dic = {'pre_signal': pre_signal, 'stop_loss_price': stop_loss_price, 'stop_gain_price': stop_gain_price }
            elif (df.at[i, 'signal_short'] == -1):
                df.at[i, 'signal'] = -1  
                pre_signal = -1
                stop_loss_price = df.at[i, 'senkou_span_b'] *(1+0.02)
                stop_gain_price = df.at[i, 'close'] - (df.at[i, 'senkou_span_b']-df.at[i, 'close'])*win_loss_ratio
                info_dic = {'pre_signal': pre_signal, 'stop_loss_price': stop_loss_price, 'stop_gain_price': stop_gain_price }
            else:
                info_dic = {'pre_signal': 0, 'stop_loss_price': None, 'stop_gain_price': None}
                df.at[i, 'signal'] = None

        elif info_dic['pre_signal'] == 1:
            if (df.at[i, 'signal_long'] == 0) or (df.at[i, 'close'] < info_dic['stop_loss_price']) or (df.at[i, 'close'] > info_dic['stop_gain_price']):
                df.at[i, 'signal'] = 0  
                info_dic = {'pre_signal': 0, 'stop_loss_price': None, 'stop_gain_price': None }

            if (df.at[i, 'signal_short'] == -1):
                df.at[i, 'signal'] = -1 
                pre_signal = -1
                stop_loss_price = df.at[i, 'senkou_span_b']  * (1 + 0.02)
                stop_gain_price = df.at[i, 'close'] - (df.at[i, 'senkou_span_b']-df.at[i, 'close'])*win_loss_ratio
                info_dic = {'pre_signal': pre_signal, 'stop_loss_price': stop_loss_price, 'stop_gain_price': stop_gain_price }


        elif info_dic['pre_signal'] == -1:
            if (df.at[i, 'signal_short'] == 0) or (df.at[i, 'close'] > info_dic['stop_loss_price']) or (df.at[i, 'close'] < info_dic['stop_gain_price']):
                df.at[i, 'signal'] = 0  
                info_dic = {'pre_signal': 0, 'stop_loss_price': None, 'stop_gain_price': None }

            if df.at[i, 'signal_long'] == 1:
                df.at[i, 'signal'] = 1
                pre_signal = 1
                stop_loss_price = df.at[i, 'senkou_span_b'] * (1 - 0.02)
                stop_gain_price = df.at[i, 'close'] + (df.at[i, 'close'] - df.at[i, 'senkou_span_b']) * win_loss_ratio

                info_dic = {'pre_signal': pre_signal, 'stop_loss_price': stop_loss_price, 'stop_gain_price': stop_gain_price }

        else:
            raise ValueError('不可能出现其它情况，报错')

    try:
        return df['signal'].iloc[-1]
    except Exception as e:
        print(str(e))
        send_dingding_msg('返回信号错误', dingding_error_api)
        return None
```

