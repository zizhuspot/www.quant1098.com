---
title: Event Strategy CoinSwap Self-Used Framework
date: 2023-10-26 16:07:00
categories:
  - Quantitative Trading
tags:
  - Digital currency
  - CoinSwap
  - Quantitative tool
  - Framework
  - digiccy
  - quant1098.com
  - Event Strategy
  - AIOQuant
description: If the program is not running for the first time or exits unexpectedly, check if there is a hold log (hold_log.csv), if there is a hold log it reads the hold log, if there is not, it creates empty df's to record the positions for each strategy.
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

## Zero. Warm reminder

First, the framework for the current framework for my own use, currently running for more than a few weeks. Can not guarantee that there are no bugs, I hope that all bosses in a full understanding of the structure of the framework and fully debugged after the run. The use of the framework leads to the profit and loss of their own responsibility. If you find any problems with the framework, you are welcome to correct them in the comments section.

Second, the default strategy within the framework is only for demonstration purposes, without backtesting verification, not suitable for real trading.

## I. Framework highlights

**1.** Multiple policy implementations for a single account

**2.** Blacklist filtering

**3.** eliminates the problem of closing and then opening positions, such as the flotilla framework, near the time of closing the position will first close all the positions, and then select the coins and then buy, such an operation will have a few problems: 1 is the slippage will increase, there is the risk of stepping out of the market, and 2 is a frequent transaction, such as the current cycle of selecting coins the same as the previous cycle, a buy and sell to increase the burden of the commission. This framework is optimized for the effect of coin selection: if the number of coins selected for the cycle is the same as the previous cycle, the number of positions in the same coins remains unchanged from the previous cycle.

**4.** Break through the exchange 1500 k line limit, theoretically can be set to unlimited k line, because k line data are saved in the local.

**5.** You can specify any offset for a single strategy, e.g. strategy 1 can run 12-hour k-line, k-line start time for 9:00 and 21:00 respectively, strategy 2 can run 8-hour k-line, k-line start time for 0:45, 8:45, 16:45 respectively.

**6.** The capital curve of a single strategy is plotted, which is convenient to view the strategy's actual performance.

**7.** Automatic replenishment of bnb

**8.** Xingda course in the implementation of the basic code, a small white understand, easy to get started!

## II. Framework Logic

#### 1. Preparation before the loop starts

**Read the position log**, if the program is not running for the first time or exited due to accident, check if there is a position log (hold_log.csv), if there is one, then the position log will be read, if not, then an empty df will be created to record the position of each strategy.

**Checks if there is a local storage record for k-line data**, if it is run for the first time, it will request it from the server based on the number of k-lines needed. If there are more than 1500 k-lines, the k-lines are stored locally, if there are less than 1500 k-lines, the k-lines are stored in memory. The period of getting k-lines can be based on the k-lines required by the strategy. For example, if the period of the k-line is 12h and the offset is 1h, then the 1 hour k-line will be obtained. If the required period is 12h and the offset is 1h15m, then we need to get 15m of klines for the resample.

#### 2. Start the cycle

**Create strategy information**, initialize a strategy_df, the df can record the allocation of funds for each strategy in the cycle, the type of k-line required, the factor to be calculated, etc., and then according to the df to calculate a dictionary need_factor, the dictionary to the k-line type as the key, with the k-line type of the factor to be calculated as the value.

**Calculate the runtime for the next cycle**, e.g. if the current time is 10:20 and a strategy has a runtime of 10:45, then the next runtime is 10:45. if there are no strategies to run between 10:20 and 11:00, then the next runtime is 11:00.

**Calculate what is needed for the next cycle** to find out what strategies need to be run, what positions need to be closed in the next cycle, and what types of k-lines need to be aggregated for the next cycle, based on the next cycle's runtime

**sleep** This sleep ends two seconds early and is used to interact data with the exchange

**Strategy Allocation Calculation** After the sleep ends, it gets the wallet margin balance from the exchange and calculates the amount of money that needs to be allocated to each strategy based on the allocation ratio.

**Update K-line data**, there are two types, local and in-memory, depending on the number of k-lines you specify. The program uses multi-threaded operation, more than 100 threads to get at the same time, in the case of stable network conditions, all the currencies of the latest k-line data update in less than 3 seconds. At the same time will update the latest price of other variables within the program, such as symbol_info, hold_data, etc..

#### **3. k-line data resampling (resample) and factorization **

If there is a coin picking operation in the current cycle, the k-line data will be aggregated into the data required for coin picking. In order to save time and reduce loops, the functions in Factors/Factors.py are called together in this operation to calculate the time series factors such as averages, ups and downs for individual coins.![](https://s2.loli.net/2023/10/26/1MVYQCy2rKezBxO.jpg)



Figure 1, Single-currency aggregation, the

Afterwards, to save memory, only the data of the latest k-line is kept, and the latest data of all currencies is saved in a single df.![](https://s2.loli.net/2023/10/26/52aUlNApEfGj9dv.jpg)



Figure 2, multi-currency aggregation

Finally, according to this df call the function in Factors/CrossEctFactors.py and Factors/RankFactors.py to calculate the cross-section factor, and calculate the cross-section data such as the ranking of each type. Note here that if the cross-section data to be calculated requires time-series data for the currency, it needs to be obtained in the factor calculation in the previous step.![](https://s2.loli.net/2023/10/26/I9quC6kMTnRfyLm.jpg)

Figure 3, cross-section calculation

#### 4. Coin Selection

According to the data calculated in the previous step, call the function in strategies.py to select coins. If the selected coins are larger than the number of selected coins, they will be filtered according to the sorting factor, and if they are smaller than that, they will all be bought. After that the number of orders placed for each coin is calculated. Merge the strategy's open position information with the position information.

#### 5. Placing Orders

For each currency, add the number of open positions to the number of open positions, subtract the number of closed positions to get the target position, subtract the current position from the target position to get the number of open positions, and use this data to place orders.

#### 6. Calculate the funding curve for a single strategy

It's actually quite simple. We just need to record the opening price every time, and then when we close the position, we use the closing price minus the opening price multiplied by the number of coins to get a single profit and loss, and then sum the gains to get the overall profit and loss of the strategy, and then finally record it for each cycle.

#### 7. Other Functions

Slightly

## Three, the use of instructions

#### 1. Exchange and pinning configuration

Fill in the required key and secret in the exchange configuration, dingding information configuration, dingding error report configuration in config.py

#### 2.Strategy Configuration

Fill in the strategy_list in config.py, the variable is a list packet dictionary data structure, each dictionary is a strategy. The description of each field is as follows:

**①strategy_name**

Example: 'BtcBullAndCoinBull_21_55_89'

String, the name of the event, the first _ before the name of the strategy, there should be a corresponding function to calculate the event in Strategies.py, and the _ after that is used to input the strategy parameters.

**②position**

floating point number, position, if the total available funds for 10000, pos is 0.3, then the strategy allocation funds for 0.3 * 10000 = 3000

**③leverage**

Floating point number, leverage, strategy allocated funds for 3000, 1 times leverage, the strategy's maximum order contract value is 1 * 3000 = 3000

**④rul_type**

Example: '12H'

String, K-period

**⑤offset**

Example: '2h15m'

String that can be converted to time_delta, the offset period of the strategy k-line, if the offset is 0h, the start time of the k-line for the 12h period will be 8:00 and 20:00 respectively, if the offset is 2h, the start time of the k-line for the 12h period will be 10:00 and 22:00 respectively.

**⑥hold_period**

Integer, number of holding periods

**⑦max_cap_num**

Integer, the number of shares to divide the funds into, currently only supports numbers equal to the number of holding periods.

**⑧ymbol_num_limit**

Integer, the number of coins to choose, None for all to buy.

**9factors**

Example: ['Ema_21', 'Ema_55']

List of factors to be calculated, time series, split by _, the first _ before the factor name, you need to have the corresponding function to calculate the factor in Factor/Factors.py, and the _ after that is used to input the factor parameter.

**⑩cross_ect_factor**

Example: ['BtcBull_21_55']

List of factors to be calculated, cross section, split by __, first _ preceded by the factor name, need to have the corresponding function to calculate the factor in Factor/CrossEctFactors.py, the _ after that is used to input the factor parameter

**⑪rank_factor**

Example: ['EmaBias_21']

List, factors to be calculated, coin picking factor, when exceeding the count_limit, what to buy based on, None means no need to sort the factors, divided by _, the first _ before the factor name, need to have the corresponding function for calculating the factor in Factor/RankFactors.py, and after that _ is used for inputting the factor parameter.

**⑫ascending**

Boolean value, in positive or negative order.![](https://s2.loli.net/2023/10/26/IpVPOlG9CfXs4z8.jpg)

Figure 4, Policy Configuration

#### 3. Factor Configuration

Configure the factor function in the Factor folder

**①Configure Factors.py**

In the Factors.py file, write the time series factor function. The data structure needed for this file is the time series data for a single currency, as shown in Figure 2 right. Here you can calculate, for example, averages, macd, up and down, and other factors

**② Configure CrossEctFactors.py**

In the CrossEctFactors.py file, write the cross-section factor functions. The data structure needed for this file is the latest k-line data for all coins, as shown in Figure 3. Here you can calculate factors such as cmc_rank, up/down ranking, etc.

**③Configure RankFactors.py**

In the RankFactors.py file, write the cross-section factor function. The data structure needed for this file is the latest k-line data of all currencies, as shown in Figure 3. Here you can calculate the ranking factor based on the cross-section factor and the time series factor

### Attachment

[Time Strategy Code](https://github.com/zqwuming/blogimage/blob/main/asset/%E4%BA%8B%E4%BB%B6%E7%AD%96%E7%95%A5%E5%AE%9E%E7%9B%98%E6%A1%86%E6%9E%B6binance-swap.rar)
