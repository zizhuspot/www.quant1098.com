---
title: For the uninitiated optimization of spot arbitrage
date: 2023-10-25 15:07:00
categories:
  - Quantitative Trading
tags:
  - Digital currency
  - spot
  - Quantitative tool
  - arbitrage
  - digiccy
  - quant1098.com
  - Binance
  - uninitiated
description: To be able to trade for a long term result that will satisfy you, you have to make changes to the current situation, and combining this with duration arbitrage will allow you to earn a sentiment premium and have a stable return in a bullish cycle.
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

## Preface

Since I started to listen to Xing Da's build big A strategy system from zero, for the overall strategy of the coin circle also has a new understanding, last year Yuan universe earned a lot of money, but this year basically all the losses, probably because of the summary in: 1. Frequent transaction losses 2. Income is not fixed, resulting in an imbalance in the mentality, which summarizes themselves is also if you want to be able to trade in the long term to achieve a result that can make you satisfied, it is necessary to target the The current situation to make changes, combined with the term arbitrage can make themselves in the bull market cycle can earn emotional premium, there is a stable income, so the overall strategy of their current mainly for the term arbitrage and other arbitrage way to do research, in order to strive for the bull market to get satisfied with the risk-free returns and then use it for risky investments in the road of trading to go a little farther, look up the term arbitrage video with the code, there should be a lot of family members need to target is to want for the overall delivery contract in the currency for all the monitoring, to achieve which currency to reach their own set premium, the first time to be able to arbitrage, so based on this, is sort of my start to optimize the start of the period arbitrage!

![](https://github.com/zqwuming/blogimage/blob/main/img/1kGNGHztXbtIabYBOBIgTdHuKcobjGGGygw8OMJB.png?raw=true)

Constant traversal monitoring is performed for the coins of the `CoinSafe` delivery contract, so as to be able to intervene at the first time when the premium occurs, and then the improved code is also interpreted at the level of a white person.

![461113](https://github.com/zqwuming/blogimage/blob/main/img/kigvaCAydkPvu59DtOQn8v3TlmYmAFwBEhtXsjoB.png?raw=true)

![576489](https://github.com/zqwuming/blogimage/blob/main/img/TDI13PBX4he9j4WeH0rsRnUTXGNyKZVNjHO7xkLs.png?raw=true)

This combination of methods can be applied to the strategy want to monitor all of the exchange's underlying, based on Xingda coursework and thus optimization, the next will be more in-depth optimization of arbitrage, the profits of cash arbitrage comes from the bull market incremental funds as well as emotional premium, if you want to have a high demand for returns, you need to do enough details in place, carefully go to the exchange's documentation, the next will be more in-depth optimization of arbitrage, the profit from the bull market incremental funds and emotional premium, if you want to have high requirements for returns, you need to do enough details in place, carefully go to the exchange's documentation. The optimization of the handling fee to do how cheap it can be how cheap it must be, Xingda provides the registration link of the fee rebate, as well as the configuration of the necessary BNB as a fee reduction, if you do not want to bear the volatility of the BNB with other sub-accounts on the BNB hedge, all the reduced expenses can be arbitrage on the arbitrage for you to generate more profits, if there is a better optimization of the demand for more guidance from the family, I will Follow up on this strategy for continuous optimization

```python
import time
import ccxt
import test_Function


coin_list = ['BTC', 'ETH', 'ADA', 'LINK', 'BCH', 'DOT', 'XRP', 'LTC', 'BNB']
future_date_list = ['221230']
future_coin_date_list = []
for coin in coin_list:
    for future_date in future_date_list:
        future_coin_date = coin + future_date
        future_coin_date_list.append(future_coin_date)  

coin_presision = {
    'BTC': 1,
    'ETH': 2,
    'ADA': 5,
    'LINK': 3,
    'BCH': 2,
    'DOT': 3,
    'XRP': 4,
    'LTC': 2,
    'BNB': 3}
execute_amount = 2000  
max_execute_num = 5  
r_threshlod = 0.01  
spot_fee_rate = 1 / 1000  
future_fee_rate = 4 / 10000  0

contract_szie = {
    'BTC': 100,
    'ETH': 10,
    'ADA': 10,
    'LINK': 10,
    'BCH': 10,
    'DOT': 10,
    'XRP': 10,
    'LTC': 10,
    'BNB': 10
}



exchange = ccxt.binance()
exchange.apiKey = ''
exchange.secret = ''


execute_num = 0
spot_symbol_name_list = []
future_symbol_name_list = []



for coin in coin_list:
    spot_symbol_name = {'type1': coin + 'USDT', 'type2': coin + '/USDT'}
    spot_symbol_name_list.append(spot_symbol_name)



for coin in coin_list:
    for future_date in future_date_list:
        future_symbol_name = {'type1': coin + 'USD_' + future_date}
        future_symbol_name_list.append(future_symbol_name)

while True:

    spot_sell1_price_list = []

    future_buy1_price_list = []

    spot_name_price_dict = {}

    future_name_price_dict = {}
    for spot_symbol_name in spot_symbol_name_list:
        spot_sell1_price = exchange.publicGetTickerBookTicker(params={'symbol': spot_symbol_name['type1']})['askPrice']
        spot_sell1_price_list.append(spot_sell1_price)

    spot_name_price_dict = dict(zip(coin_list, spot_sell1_price_list)) 

    for future_symbol_name in future_symbol_name_list:
        future_buy1_price = exchange.dapiPublicGetTickerBookTicker(params={'symbol': future_symbol_name['type1']})[0][
            'askPrice']
        future_buy1_price_list.append(future_buy1_price)

    future_name_price_dict = dict(zip(coin_list, future_buy1_price_list)) 

    for (key1, key2) in zip(spot_name_price_dict.keys(), future_name_price_dict.keys()):

        r = (float(future_name_price_dict[key2]) - float(spot_name_price_dict[key1])) / float(
            spot_name_price_dict[key1])
        print('现货: %s 价格：%.5f，期货 %s 价格：%.5f,价差：%.5f%%' % (
        key1, float(spot_name_price_dict[key1]), key2, float(future_name_price_dict[key2]), r * 100))

        if r < r_threshlod:
            print('套利价差小于目标阈值，不进行套利交易')

        else:
            print('套利价差大于目标阈值，开始进行套利交易')
            # todo 进入套利循环
            while execute_num <= max_execute_num:
                # todo 计算开空的合约数量，买入现货币的数量
                future_contract_num = int(execute_amount / contract_szie[key1]) 
                future_coin_num = future_contract_num * contract_szie[key1] / float(future_name_price_dict[key2]) 
                future_fee = future_coin_num * future_fee_rate 
                spot_amount = future_coin_num / (1 - spot_fee_rate) + future_fee 
                print('交易计划：开空合约张数',future_contract_num,'对应币数量',future_coin_num,'合约手续费',future_fee,
                      '需要买入现货数量',spot_amount,'\n')
                # todo 重新定义
                spot_symbol_name = {'type1': key1 + 'USDT', 'type2': key1 + '/USDT'}
                future_symbol_name = {'type1': key2 + 'USD_' + future_date}
                # todo 买币
                price = float(spot_name_price_dict[key1]) * 1.02
                spot_order_info = test_Function.binance_spot_place_order(exchange=exchange,symbol=spot_symbol_name['type2'],
                                                                         long_or_short='买入',price=price,amount=spot_amount)

                price = float(future_name_price_dict[key2]) * 0.98
                price = round(price,coin_presision[key1])
                future_order_info = test_Function.binance_future_place_order(exchange=exchange,symbol=future_symbol_name['type1'],
                                                                             long_or_short='开空',price=price,amount=future_contract_num)
                time.sleep(2)
                balance = exchange.fetch_balance()
                num = balance [key1]['free']
                print('待转账%s的数量:%s' % (key1,num))
                test_Function.binance_account_transfer(exchange=exchange,currency=key1,amount=num,from_account='币币',to_account='合约')

                # todo 计数
                print(key1,'*')
                execute_num += 1
                print(spot_order_info['average'])
                print(future_order_info)

    # todo 循环结束
    print('*' * 20, '本次循环结束，暂停','*' * 20)
    time.sleep(2)

    if execute_num >= max_execute_num:
        print('达到最大下单次数，完成建仓计划，退出程序')
        break

```

