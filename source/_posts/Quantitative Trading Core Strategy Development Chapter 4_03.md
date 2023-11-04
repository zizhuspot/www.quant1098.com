---
title: Quantitative Trading Core Strategy Development Chapter 4 Part III
date: 2023-11-04 10:44:00
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

### 4.3 Strategy Development Process

Consider the following criteria.

- Parameters are the same for all markets and time windows.

- Adaptive algorithms.

- Simple trading logic.

- Robust design.

  

**If there is a "holy grail" of trading in the world, it must fulfill the above criteria**. With this criterion in mind, let's take a look at the general strategy development process.

  #### 1. Start with a hypothesis

  Everything starts with a hypothesis. When we speculate fundamentally that there should be a relationship between two assets, or that there is a new trading tool in the market that enables a certain way of profiting, or that there is an unusual macroeconomic factor driving a certain micro pricing behavior. From this we can build a model that aims to capture this relationship.
  The next step is to find a closed solution for this model. Sometimes this is easy, sometimes this takes days and weeks, and sometimes there is no closed solution and numerical methods must be used. I have found Mathematica's symbolic calculations to be very useful in this process.

Now there is a model about the market and it needs to be tested whether it is realistic enough. At this stage it is common to turn to MATLAB or Python. i assume that the various parameters have some sensible values and run some simulations taking care to see that the simulation outputs look sensible or that they reflect, at least in direction, the actual changes in the market.
If the model passes this soundness test, then it is now ready to move on to formal research. When I say "formal study", I mean the transition from an abstract, stylized test of market performance to the building of an explicit model with real predictive power.
Building a true predictive model is difficult, and it's easy to fool yourself into thinking you've built a predictive model when in fact it's just overfitting, or using in-sample testing, or imposing a priori knowledge on the rules. As a result, most strategies break down in the real world.

#### 2. Building a trading strategy

The creation of the strategy rules must be done in one go. At this stage, it is important that trading rules must be developed comprehensively and consistently. One of the most dangerous things a quantitative trader faces is a strategy rule that does not respond to a boundary case (Comner Case). Unfortunately, this is one of the most common mistakes in strategy development. In its final form, a quantitative strategy must be reducible to a precise set of rules and formulas. If a trading idea cannot be reduced to this form, then it is not a systematic trading strategy.

#### 3 Writing Strategies as Code

As mentioned in the previous chapter, this step is much more difficult if the strategy construction phase uses a language (such as MATLAB) that cannot be directly written as a trading script. If MATLAB's built-in functions are involved, the consistency of the function calculations can be a real headache. So for newbies, I recommend Python as the language of choice for strategy research and modeling. Because it has many open source trading platforms, it is easy and natural to write as a trading script.

#### 4. Initial Testing

The trading strategy is now in script form and can be considered for testing with two objectives: the first objective is to determine if the trading strategy is performing as expected; the second objective is to make an initial assessment of trading performance. Now compare the consistency of the trading strategy with the performance of its program script. For example, is the strategy script generating buy signals when MA20>MA60 and sell signals when MA20MA60, and so on. In short, have we faithfully and successfully converted a trading idea into a script that we can later use for historical backtesting. The first goal is achieved when we are certain that the script for the trading strategy accurately represents the trading strategy as originally conceived. If not, the trading strategy's script must be corrected and then re-evaluated, and this process continues until we have achieved the first goal. The second goal is to get a general idea of the strategy's profit and risk profile before moving on to the next step in the testing cycle. The strategy should be moderately profitable, or at least should not lose money in many markets and over many different time periods. It doesn't have to be a winner in every test, however, if it is a loser in every test, or if the losses are significant, we should abandon the strategy. This test teaches us two things: firstly, that the trading strategy has been correctly formulated; and secondly, that its trading performance is within an acceptably good range. At this point, we have learned enough about the strategy's performance to either continue to improve it, or to move on to the next test step.

#### 5. Optimize your trading strategy

As the word "optimization" implies, optimizing a trading strategy is about using it most efficiently The most efficient use of a trading strategy is to achieve the highest possible and sustainable risk-adjusted return (i.e. Sharpe ratio). Achieving this goal is the true purpose of trading strategy optimization. Due to a long history of abuse and ignorance, the term
The word "optimization" is now considered a scourge and is even synonymous with "overfitting". Statistical overfitting refers to the tendency of an algorithm to pay too much attention to the noise in the data and to fit all the data in the sample exactly. When this process occurs in strategy development, the developer tends to boost the strategy's in-sample performance in an uncontrolled manner to fit the distribution of the sample data. Unfortunately, this mistake is common and difficult to avoid.

The danger posed by overfitting is one of the pitfalls of optimization. If we follow the steps outlined in this section correctly, we can avoid these pitfalls to the greatest extent possible and understand how proper optimization can best enhance our trading strategies. Without understanding the principles involved, it is virtually impossible to properly optimize a trading strategy to avoid these pitfalls See Chapter 8 of this book.
In short, the optimization process involves creating backtests with different model parameters using the same historical price data The optimization process is based on selecting the best performing backtests from a large number of backtests based on an objective function. If proper rules are observed during the optimization process, the resulting trading strategies will be those that offer the greatest potential for profitability in real trading. This phase is expected to result in a significant improvement in profit performance compared to the previous round of testing. This improved performance will manifest itself in many ways, but the key is in the increase in returns and the reduction in volatility If, during the test, trading performance increases significantly, then this test phase can be considered successful. Paying attention to some indicators of strategy robustness is also an important consideration at this stage. The important strategy metrics presented in the previous subsection need to be monitored when analyzing the performance and risk of the strategy

#### 6. Advancement Analysis

This is the most critical stage in the trading strategy development process. It is at this stage that the trader determines whether the trading strategy is real and reliable. In other words, we determine whether the returns generated are caused by overfitting or by a robust trading strategy. If the strategy's returns are plagued by overfitting and lack robustness in out-of-sample tests, then the strategy must be re-evaluated. Conversely, if we are lucky enough to pass the round of testing, the next step is live trading.

How do we assess the out-of-sample robustness of a trading strategy? I suggest that a valid and reliable method at this stage is Walk-Forward Analysis (WFA).

> Walk-forward analysis is a methodology used in finance to determine the optimal parameters to use in a trading strategy. The trading strategy is optimized for a time window in the data series using in-sample data, with the rest of the data reserved for out-of-sample testing. In the case of recorded results, a small portion of the retained data after the in-sample data is tested the in-sample time window is moved forward for the time period covered by the sample test and the process is repeated. Finally, all recorded results are used to evaluate trading strategies .
> --Wikipedia

In fact, using this methodology saves a great deal of time and effort by replacing much of the optimization process. In short, Advancement Analysis is strictly and exclusively based on optimized strategy parameters and out-of-sample trades are made to determine how well a trading strategy is performing.

The first and most important benefit of push analysis is that it validates the actual trading ability of the strategy. In other words, is the trading strategy viable after optimization? Does it make money? Of course, Propulsion Analysis is not live trading, but it is a realistic simulation of live trading and comes closest to the way many traders optimize the parameters of their trading strategies when they actually trade.

The second benefit of Propulsion Analysis is a more accurate and reliable measurement of the change in profit and risk after optimization. Because Advancement Analysis automatically generates multiple statistical profiles of trading periods before and after optimization, it is possible to accurately compare differences in returns within and outside the sample. Finally, Advance Analytics provides greater insight into the impact of changing market conditions on trading strategies. Changes in market trends occur frequently, and large changes in volatility and liquidity can have a significant negative impact on the performance of a trading strategy. A good, robust model will be better equipped to respond effectively to these changes.

If the trading strategy passes a rigorous advancement analysis, it can be used to test it against live trades. In summary, this testing phase answers three fundamental questions.
(1) Will the optimized trading strategy make money?
(2) At what rate will the optimized trading strategy make money?
(3) How will changes in overall market conditions such as trend, volatility and liquidity affect the performance of the strategy?

#### 7. Live Trading

After such exhaustive testing and evaluation, a live trading system is relatively easy to implement. Simply allow the computer to generate signals and trade them cycle by cycle according to the formulas and rules created in the strategy. There are many profitable trading strategies on the market, however successful system traders are rare.

The reason why is because many traders lack sufficient confidence and deep understanding of their trading strategies as they enter the inevitable retracement period. Many traders also lack the self-discipline required to strictly adhere to a mechanical trading strategy. However, systematic trading has proven to be an effective way to make money.

A thorough understanding of how a trading strategy performs, where its profits come from and the risks it takes, and under what circumstances the system will stop losses, can dramatically improve a trader's confidence in every signal the strategy sends out and, as a result, his or her determination to be disciplined. The lesson is simple: when a trading strategy has been satisfactorily tested and traded live according to expectations, trust it, stick to it and accept every signal. However, this is not to say that a trading strategy cannot fail. It is the strategy developer's responsibility to accurately understand the typical risks of a trading strategy, and it is the strategy developer's responsibility to avoid these risks by utilizing position management in order to achieve profitability. Finally, the most important thing is to know when to stop trading.

#### 8. Evaluating live performance

In order to successfully implement a trading strategy, traders must continuously monitor the real-time performance of the trading strategy. It is important to understand that it is best to use the same framework for backtesting and live trading, otherwise you are asking for trouble. Even if the frequency and size of losses are consistent with the backtesting of the trading strategy, many traders abandon the trading strategy too quickly when losses are incurred, usually due to a lack of confidence in the trading strategy and a lack of self-discipline.

Conversely, few traders are shocked when a trading strategy begins to profit at a rate far exceeding their performance expectations. While pleasant, this is a serious bias. It is important to explore the reasons for this bias, and from this we may learn some important things that may help predict large future profits. It may also point to larger losses, as larger-than-expected profits are usually the result of increased volatility, and the other side of rising volatility is often larger losses. This knowledge can also improve trading strategies. Keep in mind that larger than expected profits can be a warning of future problems

#### 9. Improving Strategies

Continuing to examine in depth the performance of a trading strategy on each trade can provide traders with valuable information over time, and it will certainly reveal the strengths and weaknesses of the trading strategy. Having such an in-depth understanding of how a trading strategy works, combined with observations of its behavior under different market conditions, will inspire many ideas for improving it. Of course, each improvement must go through the entire development and testing process again before the improved version can be used for live trading.