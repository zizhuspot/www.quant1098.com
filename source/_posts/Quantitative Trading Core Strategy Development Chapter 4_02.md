---
title: Quantitative Trading Core Strategy Development Chapter 4 Part II
date: 2023-11-04 10:22:00
categories:
  - Quantitative Trading
tags:
  - Development 
  - Strategy
  - Quantitative tool
  - Core
  - randomness
  - quant1098.com
  - successes
  - machine learning
description:  This chapter will mainly introduce the basics of strategy development, including Introduction to common types of strategies; Common strategy evaluation indicators; Strategy development process; Frequently asked questions about strategy development; Strategy development and backtesting using TqSdk.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---
### 4.2 Common Strategy Evaluation Metrics

Trading strategies can be analyzed using historical data to predict the future performance of the strategy, a process known as "backtesting". The results of backtesting provide statistics that can be used to measure the effectiveness of the strategy. There are a number of benefits to using rules-based trading strategies.

- It helps to remove human emotion from decision making.
- Strategies can be easily tested on historical data in order to test its value before investing money.

There are many indicators that can be used to evaluate the performance of a given trading strategy. In this section we present the following indicators.

- **Rate of Return:** Rate of Return (RR) symbolizes the profit or loss of a trading system over a certain period of time. Total Profit (TP) represents the profitability of the total number of trades. We calculate TP by removing the sum of all losing trades from the sum of all winning trades, which can be negative when losses exceed profits. We use RR to denote the gain or loss of an investment over a given evaluation period by expressing it as a percentage of the amount invested. In equation (4-2), INV denotes the initial capital used in the investment.。![image-20231104095124891](https://s2.loli.net/2023/11/04/cDw35aJ7zCARmMp.png)


- ** Profit/Loss Ratio:**  Profit/Loss Ratio is defined as the sum of the profits of all profitable trades divided by the sum of the losses of all losing trades during the trading period. This indicator measures the amount of profit per unit of risk, a value greater than 1 means that the trading strategy is profitable.![image-20231104095320038](https://s2.loli.net/2023/11/04/wQoFT7dheflmI9n.png)
- **Maximum Drawback:** Drawback (4-4) is defined as the percentage difference between the highest profit (or capital) prior to the current point in time and the current profit (or capital) value. Maximum Drawback (MDD) is the largest loss observed during a given trading period.MDD measures risk as the "worst case scenario" during a trading period. This indicator can help measure strategy risk and determine whether a trading strategy is practical. In (4-4) and (4-5), t; denotes the time index (i.e., timestamp). Maximum net asset value (ti) is the peak value of assets reached since the start of the trade.![1699063049769](https://s2.loli.net/2023/11/04/gVdCl9hTp5KsWJi.png)
- **Winning Ratio**: Winning ratio (4-6) is calculated by dividing the number of winning trades by the total number of trades. It indicates the likelihood of a trade having a positive return![1699063181327](https://s2.loli.net/2023/11/04/IkJL7tXydqQb3NU.png)
- **Sortino Ratio** : The Sortino Ratio represents the excess return earned per unit of downside volatility. Downside risk ( 4-7) is defined as the standard deviation of negative returns. The Sortino Ratio uses downside risk to measure the risk associated with a given investment. In (4-7), "Return" represents the return generated by a given trading strategy (4-8) and "Target Return" represents the minimum acceptable return (4-9).![1699063225199](https://s2.loli.net/2023/11/04/QpZzTBxoaVEq1uC.png)
- **Sharpe Ratio**: The Sharpe Ratio (4-10) is a metric for calculating risk-adjusted returns. The basic purpose of the Sharpe Ratio is to allow the investor to analyze how much return he earns compared to the level of additional risk incurred in generating that return. The Sharpe ratio can be thought of as the excess return earned per unit of volatility. To date, it remains one of the most popular risk-adjusted performance indicators.![1699063510578](https://s2.loli.net/2023/11/04/YLA42nykrsFi5Vd.png)
- **Beta**: Beta is a measure of the volatility or systematic risk of a security or portfolio Beta measures how a strategy responds to a benchmark index Beta (4-11) greater than 1 indicates that the price of a security will be more volatile than the benchmark index. For example, if an asset has a Beta of 1.3, it is theoretically 30% more volatile than the benchmark. Essentially, Beta represents an important trade-off between reducing risk and maximizing return, and is calculated as follows.![1699063656220](https://s2.loli.net/2023/11/04/M68IJU3rSAoceGT.png)
- **Jensen's Alpha:** Jensen's Alpha is a measure of investment performance on a risk-adjusted basis.Jensen's Alpha(4-12) measures the extent to which a security or portfolio of securities has traded in excess of theoretically expected returns. For example, a Jensen's Alpha of 1.0 means that the fund outperformed its benchmark index by 1%. The formula for calculating Jensen's Alpha is as follows.![1699063697577](https://s2.loli.net/2023/11/04/EnpAGiT1gjk6swc.png)

