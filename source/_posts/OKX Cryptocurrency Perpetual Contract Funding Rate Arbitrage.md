---
title: OKX Cryptocurrency Perpetual Contract Funding Rate Arbitrage
date: 2023-10-25 22:07:00
categories:
  - Quantitative Trading
tags:
  - Funding Rate
  - Stock
  - Quantitative tool
  - OKX
  - Arbitrage
  - quant1098.com
  - Okex
  - AIOQuant
description: The perpetual contract funding fee arbitrage program on okx written last year has a one year measured return of around 40%.
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

## Preamble

The perpetual contract funding fee arbitrage program on okx written last year has a one year measured return of around 40%.

It has the following features:

1. Positive Funding Fee Arbitrage
2. Negative funding fee arbitrage
3. Position leverage reset optimal settings
4. Leveraged account fractional smoothing
5. OKX top 20 total arbitrageable funds
6. Query the current position of the underlying trading limit

This program can automatically adjust the optimal leverage to improve capital utilization.

Put some thoughts on money fee arbitrage below:


## **Dynamic Balance Funds Fee Arbitrage on Spot and Perpetual Contracts Alpha 1.0**


- Project Outline
  - Project Outline
    - This project uses api calls to an exchange to realize multiple trades of spot (long) and perpetual contracts (short) within a specified time period, to balance the value of the two commodities before the time cutoff. After the completion of the arbitrage of the capital fee, dynamic trading out of the hands of the spot and contract exchange into stable virtual currency USDT.
  - Funding Fee Earnings
    - Settlement occurs every 8 hours, with one party paying the other. When the funding rate is positive, one party pays the other party; when the funding rate is negative, the other party pays the other party.
  - Arbitrage Principle
    - In the case of a positive funding fee, on the same exchange, under certain conditions, the value of the spot long and permanent contract short balance, to maintain no loss of principal to obtain the funds paid to the short side of the multi-side of the fee.
  - Arbitrage formula
    - Profit = (value of spot sold + value of contract sold + margin gain) - (value of spot bought + commission ① + value of contract bought + commission ②)
      - When Handling Fee ①② > Funding Fee, you lose money.
      - When the Handling Fee ①② = Funding Fee, the position is flat.
      - When the fee ①② is lower than the funding fee, then profit is made.
  - Handling Fee Correlation
    - The commission decreases as the level of equity increases.
    - Beginner's level Opening fee 0.12
      - Coin trading 0.1%
      - Contracts 0.02
  - Typically, the funding rate is 0.2%-0.4%.
    - All three trades are on different underlying instruments Yield 0.24%-0.84
    - Yield 0.48%-1.84% for three trades in the same instrument
    - Additional information
      - Funding rates fluctuate after each liquidation and need to be adjusted for fluctuations.
- Project flow
  - Successful call to Okex api to realize the function of obtaining real-time prices and placing orders.
    - The Okex api interface needs to be accessed through the Hong Kong server.
    - Get spot price spotAPI.get_specific_ticker(a + '-USDT')
    - execute spot order result = levelAPI.take_order(instrument_id='', side='', margin_trading='', client_oid='', type='', order_type='0', price='', size='', notional='', order_type='0', price='', size='') , notional='')
    - Return spot order results
    - Get the price of the perpetual contract swapAPI.get_specific_ticker(a + '-USDT-SWAP')
    - Execute a perpetual contract order result = swapAPI.take_order(instrument_id='', type='', price='', size='', client_oid='', order_type='0', match_price='0')
    - Return the result of placing an order for a perpetual contract
  - Think about strategies to achieve a balance of value between matching spot and perpetual contracts Principal risk
    - Risk
      - Short-term price fluctuations Short-term price difference between the two commodities purchased resulting in loss of capital
      - Factors such as exchange restrictions on high-frequency trading, such as ip limits and dollar limits.
      - Risk factor and uncertainty of the currency itself
  - Automated trading to meet the low risk of capital strategies and arbitrage
    - Monitoring page and order automation
- Project risk points
  - When the price does not move, it is very profitable. However, prices do not always move and short-term price fluctuations can cause risk to the asset.
  - High funding rates are not always sustainable and cannot be held for a long period of time.
  - If you add leverage, there is a risk of bursting the position
  - Based on USDT propriety The risk is that USDT and the exchange mine
- Program Evaluation
  - Arbitrage through funding fees alone is not very secure
- Alternative solution (stabilized version)
  - To find a very high funding rate of coins, the difference returns to profit can fully cover the transaction costs and more surplus to complete the two sides of the order, such as the difference in the expansion of the completion of the ladder to increase the position, due to the existence of the funding rate, the difference will return sooner or later. If it does not return, you can continue to collect funds fees, and use this to complete a stable arbitrage.
  - Negative funding rate Borrowing coins to go short Contracts to go long
  - Positive funding rate Buy long contracts, short contracts.
- Requirements for each step of the program (core trading steps)
  - ① Input the name of the cargo to be arbitraged, in the form of a character string, and the principal amount to be arbitraged, in integer form.
  - ② call api interface to get the relevant data, including the latest spot price, transaction fees, the latest price of the perpetual contract, the opening fee, the capital rate, etc. ③ calculate this arbitrage.
  - ③ calculate the estimated return on this arbitrage
  - ④ Calculate the price and quantity of the first order to be placed.
  - ⑤ Monitor the number of spot transactions and the number of perpetual contract transactions, as well as the ups and downs, respectively.
  - ⑤ Monitor the number of spot transactions, the number of permanent contracts traded, and the ups and downs.
  - ⑦If the funding rate is adjusted or reversed, sell in batches, refer to the above process.

### High Frequency Funding Rate Arbitrage Model Design and its Extension

- Alpha 1.0 Version
  - Function ① Go long on spot and short on swap perpetual contract with positive funding rate (single exchange).
  - Function 2: Detect the price fluctuations of spot and swap perpetual contracts on OKEX during the trading process.
  - Function 3: Fulfill buy and sell orders in optimal conditions.
  - A few problems need to be solved
    - 1, the pursuit of the second order of the transaction at the expense of profits, the pursuit of price balance or price advantage, the transaction is often completed by pending orders.
    - 2, spot spot and swap perpetual contract price fluctuations in real time, only in a very few times can be traded.
    - 3、Small currency price fluctuations is very affecting the capital, how to find the price of relatively stable oscillation time period.
- Alpha 2.0 Version
  - Ability to monitor prices and place orders on different exchanges.
  - Function 4: Sell spot borrowed coins and go long on swap perpetual contracts (on a single exchange), taking into account negative funding rates.
  - Function 5: Go long and long swap perpetual contracts on different exchanges (multiple exchanges)
- Alpha 3.0 Version
  - Mixed ETF arbitrage model with multiple currency pairs to further reduce risk points.
  - Multi-account, multi-IP, multi-exchange, multi-currency pair matrix crossover modeling
- Model Extension - High Frequency Price Spread Arbitrage Modeling
  - Timing of buys and sells based on monitoring the price fit in the Funding Fee Arbitrage model.

## Code File

### core code :

swap_rates.py:

```python
# -*- coding = utf-8 -*-

from time import sleep
from uuid import uuid4
from Private.api import marketAPI
from Common.common import set_leverage, inquire_info
from Common.common import account_about,get_position,get_pending,prepare_var
from Common.public_doc_link_info import get_rates_history
from Common.Transaction_settings import get_ms_info, calculation, fee_info, rate_info, calculation_dev
from Function.swap_rates_common import rates_set_settings, pos_max_buy, calculate_profit
from Function.swap_rates_common import calculate_open_list, open_order_buy, calculate_close_list, close_order_sell


def open_swap_rates(type):
    eq, availEq = account_about()
    pos_name, pos_pos, pos_upl, pos_imr = get_position()
    pend_pos, pend_px, pend_id, pend_time = get_pending()
    spot_name, margin_name, swap_name, p_name = prepare_var()
    ctVal, m_tickSz, s_tickSz, m_lotSz, s_lotSz, m_minSz, s_minSz = get_ms_info(margin_name)
    median, swap_last, max_money = calculation(margin_name, swap_name, ctVal)
    p_log_p, p_0, p_1, p_2, p_3, p_4, p_5, p_bzj = rates_set_settings(type, margin_name, swap_name, ctVal, pos_pos, pos_imr, pend_pos, pend_px)
    set_leverage(margin_name, swap_name, p_2, p_4) 
    m_fee, s_fee = fee_info(margin_name)  
    print(m_fee, s_fee)

    u_rate = get_rates_history(m_name='USDT') 
    # b_rate = get_rates_history(name=margin_name[:-5])  
    rate = eval(rate_info(swap_name))

    m_max_buy, m_max_sell, s_max_buy = pos_max_buy(margin_name, swap_name) # 持仓最大可交易量

    p_swap, p_margin, n, p_margin_list, p_swap_list = calculate_open_list(swap_name, p_2, p_3, ctVal, max_money, p_bzj, m_max_buy)

    calculate_profit(s_fee, m_fee, rate, u_rate)

    yes = input("\nPlease enter any key to start the program!\n")

    m = 1
    while n > 0:
        p_margin = p_margin_list[m - 1]
        p_swap = p_swap_list[m - 1]
        client_oid = 'zjf' + str(uuid4())[:8]

        while True:
            m_max_buy, m_max_sell, s_max_buy = pos_max_buy(margin_name, swap_name)

            if type == 'positive':
                m_max_open_number = m_max_buy
            else:
                m_max_open_number = m_max_sell

            string = "当前杠杆开仓量为{:.3f} 最大开仓量为{:.3f} ".format(p_margin, m_max_open_number)

            if p_margin <= m_max_open_number:
                pass
            else:
                print(string, "不可开仓")
                break

            dev = calculation_dev(swap_name, median, string)

            if abs(dev) < 0.08: # 如果偏离度绝对值小于0.8% 开始下单
                lever_order, swap_order = open_order_buy(type,margin_name,swap_name,p_swap,client_oid,m_fee,ctVal,m_tickSz)
                break

            sleep(5)

        j = 1
        while True:

            s, l, s_price, l_price = inquire_info(client_oid, margin_name, swap_name, ctVal)

            if s == 'filled' and l == 'filled':
                break
            else:

            j = j + 1
            sleep(5)

        n = n - 1
        m = m + 1
        sleep(1)


def close_swap_rates(type):
    pos_name, pos_pos, pos_upl, pos_imr = get_position()
    spot_name, margin_name, swap_name, p_name = prepare_var()

    swap_info = marketAPI.get_ticker(swap_name)
    last = eval(swap_info['data'][0]['last'])

    ctVal, m_tickSz, s_tickSz, m_lotSz, s_lotSz, m_minSz, s_minSz = get_ms_info(margin_name)

    median, swap_last, max_money = calculation(margin_name, swap_name, ctVal)

    print("\n当前持仓 {} USDT\n{} : {}\n{} : {}\n".format(abs(pos_pos[margin_name] * last - eval(pos_upl[margin_name])),
                                                      margin_name, pos_pos[margin_name], swap_name, pos_pos[swap_name]))

    a = pos_pos[margin_name]
    b = pos_pos[swap_name]

    p_swap, p_margin, n, p_margin_list, p_swap_list = calculate_close_list(swap_name,a,b,ctVal,max_money)

    yes = input("\nPlease enter any key to start the program!")

    m = 1
    while n > 0:
        p_lever = p_margin_list[m - 1]
        p_swap = p_swap_list[m - 1]

        client_oid = 'zjf' + str(uuid4())[:8]

        while True:
            string = ''
            dev = calculation_dev(swap_name, median, string)

            if abs(dev) < 0.08:
                margin_order, swap_order = close_order_sell(type, margin_name, swap_name, p_margin, p_swap, client_oid, s_tickSz)
                break
            sleep(5)
        j = 1
        while True:

            s, l, s_price, l_price = inquire_info(client_oid, margin_name, swap_name, ctVal)

            if s == 'filled' and l == 'filled':
                break

            else:
            j = j + 1
            sleep(5)

        m = m + 1
        n = n - 1

```

### code file:

[Arbitrage_swap_rates_OKX](https://github.com/zqwuming/blogimage/blob/main/asset/Arbitrage_swap_rates_OKX.zip)
