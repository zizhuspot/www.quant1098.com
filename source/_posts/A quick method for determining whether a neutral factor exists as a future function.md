---
title: A quick method for determining whether a neutral factor exists as a future function
date: 2023-10-26 13:07:00
categories:
  - Quantitative Trading
tags:
  - Digital currency
  - factor
  - Quantitative tool
  - future function
  - quick
  - quant1098.com
  - Okex
  - method
description: A method to quickly determine whether a factor contains a future function has been worked out after tireless efforts
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

## Preface

 Before participating in the neutral group to obtain a very outstanding return factor, and then used in the real market (before the real market also asked gpt the factor does not use the future function, also asked for guidance). But the actual disk for more than a month after the discovery of the back test picturesque real disk loss into a dog, and began to re-suspect accidentally introduced the future function, and then the actual disk data and back test data every calculation step are printed out a full 4 days to find out that the data will be typed out real disk data will be added because of the back of the new data every hour, so that the actual disk calculated results continue to change (obviously use the future of the data), and then after the Tireless efforts to research a quick way to determine whether the factor contains a future function.

![](https://s2.loli.net/2023/10/26/MJdFmh8Z57261Rs.png)

1. 2. Quick judgment method
   1. still use our real trading framework code, config.py inside fill in the need to check the factor and 6 offset are open. 2. then put my following program into the same layer.
   2. and then put my following program into the same layer of the real market code, run - fast check the factor future function program.py
   3. analyze the program, this program is in order to run a different real program in order, different real program is only set up in order to set the time in order.
2. ![](E:\Download\input\iZyU3RN4BkLvtnx.png)
3. Then there will be a txt log result that you name after the program has finished running, and the log will be set up inside the live program.![](https://s2.loli.net/2023/10/26/wZI95pbgNThDAfY.png)
4. Next to explain how to determine whether the future function, open the txt log will find that there are different time periods of the real market elected pairs of coins, the following picture is the use of the future function, as shown in the figure 07:00 elected offset5 pairs of coins and the next cycle of offset5 elected pairs of coins are different, because of the introduction of the next cycle of the data and thus the results of the calculations in the constant change. (Keep looking to find that basically every cycle and the previous cycle of the same offset pairs will change)![](https://s2.loli.net/2023/10/26/TisAOFCKX6NVB1v.png)
5. After n-factor checking confirms that this method is absolutely valid judgment, the following picture is an example graph without future function, check down how many cycles can match the coin pair calculated by the same offset.![](https://s2.loli.net/2023/10/26/UwuSJjvWhyrzfOg.png)

### 总结：

I've been struggling for a long time over factor future functions, so I hope this quick way to determine if a factor has a future function will help you bosses.

![](https://s2.loli.net/2023/10/26/PZ91QpyTLkfjCzs.png)

## 代码：

main.py:

```python
import time
import subprocess


program_list = ['startup-信号测试1.py', 'startup-信号测试2.py', 'startup-信号测试3.py', 'startup-信号测试4.py', 'startup-信号测试5.py', 'startup-信号测试6.py', 'startup-信号测试7.py', 'startup-信号测试8.py', 'startup-信号测试9.py', 'startup-信号测试10.py'] # 按顺序列出要运行的程序

for program in program_list:

    process = subprocess.Popen(['python', program])

    start_time = time.time()

    while True:

        elapsed_time = time.time() - start_time
        if elapsed_time >= 140:
            process.terminate()
            break

        time.sleep(1)

    time.sleep(10)

```

test_code_01.py

```python
import pandas as pd
import warnings
import sys
warnings.filterwarnings('ignore')
pd.set_option('display.max_rows', 1000)
pd.set_option('expand_frame_repr', False) 
pd.set_option('display.unicode.ambiguous_as_wide', True) 
pd.set_option('display.unicode.east_asian_width', True)
from api.market import *
from api.position import *
from api.trade import *
from utils.functions import *
from utils.commons import *
from utils.dingding import *
from config import *

class Logger(object):
    def __init__(self, fileN="default.log"):
        self.terminal = sys.stdout
        self.log = open(fileN, "a")
    def write(self, message):
        self.terminal.write(message)
        self.log.write(message)

    def flush(self):
        pass
sys.stdout = Logger('Coop_[3].txt')
def run():
    while True:
        symbol_list, min_qty, price_precision, min_notional = load_market(exchange, black_list)
        # symbol_list = symbol_list[:10] 

        equity = retry_wrapper(exchange.fapiPrivate_get_account, func_name='fapiPrivate_get_account')  
        equity = pd.DataFrame(equity['assets'])
        equity = float(equity[equity['asset'] == 'USDT']['walletBalance'])
        # print('账户金额:', equity)
        if equity <=0:
            exit()

        position_df = get_position_df(exchange)

        send_dingding_msg_for_position(strategy,equity, position_df,accumulations,Time_period,Cumulative_Average)

        # ===sleep直到下一个整点小时
        # run_time = sleep_until_run_time('1h', if_sleep=True, cheat_seconds=10)
        run_time = datetime.strptime('2023-06-09 08:00:00', "%Y-%m-%d %H:%M:%S")
        # run_time = datetime.strptime('2023-06-09 09:00:00', "%Y-%m-%d %H:%M:%S")
        # run_time = datetime.strptime('2023-06-09 10:00:00', "%Y-%m-%d %H:%M:%S")
        # run_time = datetime.strptime('2023-06-09 11:00:00', "%Y-%m-%d %H:%M:%S")
        # run_time = datetime.strptime('2023-06-09 12:00:00', "%Y-%m-%d %H:%M:%S")
        # run_time = datetime.strptime('2023-06-09 13:00:00', "%Y-%m-%d %H:%M:%S")
        # run_time = datetime.strptime('2023-06-09 14:00:00', "%Y-%m-%d %H:%M:%S")
        # ===获取所有币种的1小时K线
        s_time = datetime.now()
        symbol_candle_data = fetch_all_binance_swap_candle_data(exchange, symbol_list, run_time, get_kline_num)
        # print(symbol_candle_data)
        print('获取所有币种K线数据完成，花费时间：', datetime.now() - s_time)

        s_time = datetime.now()
        select_coin = cal_factor_and_select_coin(symbol_candle_data, strategy, run_time, get_kline_num)

        print('完成选币数据整理 & 选币，花费时间：', datetime.now() - s_time)
        print('选币结果：\n', select_coin)

        time.sleep(20)


if __name__ == '__main__':
    reset_leverage(exchange, 8)
    while True:
        try:
            run()
        except Exception as err:
            msg = '系统出错，10s之后重新运行，出错原因: ' + str(err)
            print(msg)
            print(traceback.format_exc())
            send_dingding_msg(msg)
            time.sleep(10)



```

