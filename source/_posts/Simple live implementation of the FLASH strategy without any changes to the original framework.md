---
title: FLASH策略的简易实盘实现，无需更改任何原有框架
date: 2023-08-14T18:12:35Z
lastmod: 2023-08-14T18:17:25Z
---

# FLASH策略的简易实盘实现，无需更改任何原有框架

FLASH​策略的实盘实现（基于框架少量改动）

先贴signal代码：

```python
import os
import pandas as pd
from datetime import datetime


def signal_adapt_bolling_with_flash(df_base, now_pos, avg_price,symbol_config,symbol ):
    holding_times_min=10
    #本地文件名,初始化本地文件
    para=symbol_config[symbol]['para']
    id=para['id']

    file_name=os.path.join(father_path,'data',id+'.csv')
    ini_data(file_name,dic = {'pre_signal': 0, 'stop_lose_price': None,'holding_times':0,'stop_win_times':0,'stop_win_price':0})

    # ===计算指标

    n = para['n']

    #固定止损参数
    df=df_base.copy()
    stop_loss_pct=para['stop_loss_pct']

    df['median'] = df['close'].rolling(n).mean()
    # 计算上轨、下轨道
    df['std'] = df['close'].rolling(n).std(ddof=0)  # ddof代表标准差自由度
    df['zscore'] = abs(df['close'] - df['median']) / df['std']
    df['zsocre_abs'] = df['zscore'].rolling(n).max().shift()

    df['upper'] = df['median'] + df['zsocre_abs'] * df['std']
    df['lower'] = df['median'] - df['zsocre_abs'] * df['std']




    #初始化信号
    signal=None

    # 做空条件
    condition1=df.iloc[-1]['close']<df.iloc[-1]['lower']
    condition2=df.iloc[-2]['close']>df.iloc[-2]['lower']
    short_condition =condition1 & condition2

    # 做多条件
    condition1=df.iloc[-1]['close']>df.iloc[-1]['upper']
    condition2=df.iloc[-2]['close']<df.iloc[-2]['upper']
    long_condition =condition1 & condition2

    #平空条件
    condition1 = df.iloc[-1]['close'] > df.iloc[-1]['median']
    condition2 = df.iloc[-2]['close'] < df.iloc[-2]['median']
    close_short_condition = condition1 & condition2

    #平多条件
    condition1 = df.iloc[-1]['close'] < df.iloc[-1]['median']
    condition2 = df.iloc[-2]['close'] > df.iloc[-2]['median']
    close_long_condition = condition1 & condition2

    #检查本地文件与实际仓位是否有出入
    # 从本地文件读取当前参数
    info_dict = read_data(file_name)

    if now_pos != info_dict['pre_signal']:
        #如果实际仓位为0
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

    #开始下单逻辑
    #做空
    if short_condition & (now_pos!=-1):
        signal = -1
        stop_lose_price=df.iloc[-1]['close'] * (1 + stop_loss_pct / 100)
        info_dict = {'pre_signal': -1,
                     'stop_lose_price': stop_lose_price,
                     'holding_times': 0, 'stop_win_times': 0, 'stop_win_price': 0}
        save_data(info_dict,file_name)
    #做多
    elif long_condition & (now_pos!=1):
        signal = 1
        stop_lose_price = df.iloc[-1]['close'] * (1 - stop_loss_pct / 100)
        info_dict = {'pre_signal': 1,
                     'stop_lose_price': stop_lose_price,
                     'holding_times': 0,
                     'stop_win_times': 0, 'stop_win_price': 0}
        save_data(info_dict,file_name)
    # ===考察是否需要止盈止损
    #当前持仓多头时
    elif now_pos == 1:
        # 由持仓次数，决定flash加速均线用哪个ma
        holding_times = info_dict['holding_times']
        ma_temp = max(n - holding_times, holding_times_min)
        #更新info_dict的holding_times
        info_dict['holding_times'] = holding_times + 1
        #计算止盈均线
        df_temp=df['close'].rolling(ma_temp,min_periods=1).mean()
        #提取均线最新值
        flash_stop_win=df_temp.iloc[-1]
        #中线平多
        if close_long_condition:
            signal = 0
            info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                         'stop_win_price': 0}

        #固定止损
        elif df.iloc[-1]['close'] < (avg_price*(1-stop_loss_pct/100)): #固定止损
            signal = 0
            info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                         'stop_win_price': 0}

        #flash!!
        #如果价格到达止盈点
        elif df.iloc[-1]['close'] < flash_stop_win:
            # 如果价格超过了上一次止盈点价格，说明上方可能还有空间，则重新开始flash加速，
            if df.iloc[-1]['close'] > info_dict['stop_win_price'] or info_dict['stop_win_times'] == 0:
                info_dict['stop_win_price'] = df.iloc[-1]['close']
                # 产生的止盈点次数+1
                info_dict['stop_win_times'] = info_dict['stop_win_times'] + 1
                # flash加速均线的持仓次数清零
                info_dict['holding_times'] = 0

            else:
                #止盈出场
                signal=0
                info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                             'stop_win_price': 0}
        # 更新本地文件
        save_data(info_dict,file_name)
    # 当前持仓空头时
    elif now_pos == -1:
        # 由持仓次数，决定flash加速均线用哪个ma
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
        # 如果价格达到止盈点
        elif df.iloc[-1]['close'] > flash_stop_win:
            # 如果价格跌破了上一次止盈点价格，说明下方可能还有空间，则重新开始flash加速，
            if df.iloc[-1]['close'] < info_dict['stop_win_price'] or info_dict['stop_win_times'] == 0:
                info_dict['stop_win_price'] = df.iloc[-1]['close']
                # 产生的止盈点次数+1
                info_dict['stop_win_times'] = info_dict['stop_win_times'] + 1
                # flash加速均线的持仓次数清零
                info_dict['holding_times'] = 0

            else:
                signal = 0
                info_dict = {'pre_signal': 0, 'stop_lose_price': None, 'holding_times': 0, 'stop_win_times': 0,
                             'stop_win_price': 0}
        #更新本地文件
        save_data(info_dict,file_name)

    return signal

```

这是一个自适应布林加上flash的实盘代码。我和刑大的一个不同在于我讲para改成了dict，symbol_config变成了：

```python
'eth-usdt': {'instrument_type': 'SWAP_USDT',
                 'size': 1,
                 'leverage': '1.2',
                 'strategy_name': 'signal_adapt_bolling_with_flash',
                 "para": {'n': 515,'stop_loss_pct': 7}
                 },
```

这个样子，然后我在主程序给symbol_config的para里加了个id号，各位可以将​ ​id=para['id']

```
```

这段代码改成自己喜欢的样子。。。随意就好。father_path是一个自动读取当前文件夹上一文件夹的路径，来自于：

  

```python
true_path=os.path.abspath(__file__)  # 绝对路径
father_path=os.path.dirname(os.path.dirname(true_path)) #当前工作目录
root_path=os.path.dirname(os.path.dirname(os.path.dirname(os.path.dirname(true_path)))) #OKEX项目根目录
```

‍
