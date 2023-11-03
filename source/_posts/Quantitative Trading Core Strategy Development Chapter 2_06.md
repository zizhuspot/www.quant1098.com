---
title: Quantitative Trading Core Strategy Development Chapter 2_06
date: 2023-11-03 21:07:00
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
description: Most people don't really understand the marvelous markets we are in. Starting with the efficient market hypothesis, this chapter explains the fundamental characteristics of the markets we live in  randomness, chaos, and time-varying nature, through the construction of stochastic portfolios, statistical tests of randomness, and the successes and failures of machine learning in the markets.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---
## Chapter 2 Basic Perceptions about Markets Part VI

### 2.4 Why Machine Learning Fails

According to randomness tests, markets do not always seem to be random. So, is there any way to use machine learning techniques to automatically learn the distribution of market data and profit from it? To the best of my knowledge, it is very difficult. This section describes some of the reasons why machine learning algorithms have not been successful in the financial sector, starting with the historical changes in the correlation between stocks and bonds, and provides some ideas for possible solutions.

#### 2.4.1 Starting with bonds and stocks

Our general impression is that bond and stock returns always seem to be negatively correlated. Is this true? Figure 2-13 from `Expected Returns: An Investor's Guide to Harvesting Market Rewards' by `Antti Ilmanen' shows how the correlation between stocks and bonds has changed in the US over the last 200 years. It shows how the correlation between U.S. stocks and bonds has changed over the past 200 years.

The average correlation between stocks and bonds has been stable over the past two centuries, but this indication is not static. During periods of high inflation volatility, the stock-bond correlation remained positive from 1965 to 1997. During this period, changes in inflation expectations drove the return of stocks and bonds.

Only in the decade between the mid-1950s and the mid-1960s, and for a few years during the 1929 crash. The period of negative correlation between stocks and bonds was shorter, and in the last decade the negative correlation between stocks and bonds has reached an all-time high. That said, the stereotype of what we call "negative stock-bond correlation" has not been common throughout history!

![image-20231103184104531](https://s2.loli.net/2023/11/03/XhsgBJHFjc7ZbNy.png)

The example of stocks and bonds is a good illustration of a property of financial markets: time- varying. As the AMH hypothesis suggests, financial markets are constantly gulping and absorbing information from the outside world to evolve, and the immediate effect of this is that all fixed-parameter models based on static assumptions will fail sooner or later.

On the other hand, starting with `AlexNet', machine learning - and deep learning in particular - has produced a dizzying array of records in benchmarks and has brought the discipline to the forefront. This culminated in AlphaGo's victory over Lee Sedol, which made it seem as if we had found a reliable path to strong AI. More recently, some have applied this technology to the highly complex world of financial investing, hoping to find the "holy grail" of trading. 

However, I am not a fan of this effort for several reasons:

1. Time-varying data distribution

The issue of data distribution is critical, and has been neglected in almost all price forecasting papers.

We can compare financial datasets with image classification datasets to better understand this. Let's consider the CIFAR-10 dataset, which consists of 10 classes with 5000 images in the training set for each class and only 1000 images in the test set for each class, as shown in Figure 2-14. 

We expect the distribution of pixel weights in the training set of the dog class to be similar to the distribution of pixel weights in the test set of the dog class. In other words, the dog image will contain dogs from both the training and test sets. The obvious thing is: the dog image must contain dogs (kind of like crap). This obvious property simply doesn't apply to most financial datasets, where the data you see in the future may be completely different from the data you see today. In fact, this is a dead giveaway when applying machine learning to real-world problems in finance. While most researchers pay attention to the distributional bias of the data they use, they are almost never concerned about the change in the distribution of the data over time. 

![image-20231103184201245](https://s2.loli.net/2023/11/03/JGXfYFyCjsbBSZg.png)

2. Insufficient sample size

Forecasting based on small data sets is often required in finance. An example is macro statistics such as unemployment and non-farm payrolls. This type of data has only one data point per month and simply does not have enough history. Another extreme example is the financial crisis, where there is only one data point to learn from.

This makes it very difficult to apply machine learning methods, and one approach that many people end up using is to combine less frequent statistics with relatively frequent data. For example, it is possible to combine non-agricultural data with daily stock returns and feed this combined dataset to a machine learning model. However, a significant amount of supervised learning is usually required to remove any doubt about the quality of the model. 3.

3. Very high complexity

JP Morgan ran into this problem when trying to apply artificial intelligence, specifically deep reinforcement learning techniques, to market trading. In their NIPS2018 paper, "Idiosyncrasies and challenges of data driven learning in electronic trading", they found that the complexity of a moderately frequent automated trading system was already made them almost unmanageable.

They found that the complexity of a moderately frequent automated trading system was already almost too much for them to handle.

`A game of chess is about 40 moves. A game of Go is about 200 moves. If a medium-frequency electronic trading algorithm reconsidered its choices every second, that would amount to 3,600 moves per hour. In chess or Go, each move is the manipulation of a qualified piece, and only pieces are manipulated. In the case of electronic trading, each move is a collection of operational sub-orders: it consists of multiple concurrent orders with different characteristics (price, size, order type, etc.). For example, an action may submit a passive buy order and an active buy order at the same time. The passive sub-order will remain in the order book at the specified price, thus providing liquidity to other market participants. The provision of liquidity by a passive sub-order may ultimately be profitable by obtaining a spread at the time of the trade: it is possible to complete the trade at a better price than a participant who obtains liquidity in the same trade. An active sub-order can be used to gain access to a price movement, and the two can form a single action. The resulting action space can be very large and grows exponentially with the number of features in the portfolio. ``

4. Partially Observable Markov Decision Process (POMDP) 

Academically, financial markets are typical of Partially Observable Markov Decision Making (POMDP). ! [ref2] At no time does anyone really have the complete picture. We don't know what will happen tomorrow, but still need to make decisions about our trades. We have very little information, and at the same time, the distribution of data is constantly changing.

#### 2.4.2 How to deal with time-varying markets

How to deal with POMDP? In fact, this type of proposition has been studied for a long time. However, not in the financial field, but in more practical fields such as aerospace, missile guidance, ship's trajectory estimation, etc. The reason is that the real-world object of study is not the same as the real-world object of study. Because the object of study in the real world is a complex system just like the financial system, only the degree of complexity is different, and the study of how to control this system to achieve our goals is called control science. **In control theory, stochastic control is the closest to financial practice. The financial investment problem is ultimately a stochastic optimal control problem. ** Stochastic Control

Stochastic Control or Stochastic Optimal Control is an area of control theory that deals with the control of systems with uncertainty, either in the measurements or due to the effects of noise. The system designer assumes that the random noise affecting the state variables has a known probability distribution. The purpose of stochastic control is to design the time trajectory of the controlled variable in the presence of noise in such a way that the system performs the desired control task at minimum cost (which may be appropriately defined). Stochastic control may be used for discrete-time systems as well as for continuous-time systems.

Admittedly, we can see that the theory currently has limitations, such as the fact that only noise distributions can be assumed in advance, due to the complexity of real systems. With solid modeling of the system, it is possible to distinguish whether it is the assumptions that are at fault, the estimation of the parameters, or the model itself that is at fault, so that improvements can be made. The fascinating thing about financial markets is that they are multi-dimensional, and what we derive from complex mathematical formulas, someone else may derive from a system of averages, but the process is based on a different understanding of the market. 

If you are interested in this field, you can read Prof. Feiyue Wang's book "Optimal Control: Mathematical Theory and Intelligent Methods" to get started, and then read Prof. Yangwang Fang's book "Optimal Control Theory of Stochastic Systems and Applications" to deepen your understanding. In Chapter 7, there will be an example of retraction control based on optimal control for the reader's reference.

### 2.5 Summary of the Chapter 

This chapter introduced the main characteristics of financial markets: stochasticity, chaos and time-varying. Randomness is accomplished through a simple thought experiment: if we construct our portfolio randomly, there will always be a portfolio that can beat our carefully prepared portfolio over a long investment history; chaos is verified by rigorous statistical tests; and finally, the author points out that the reason why traditional machine learning does not perform well in the financial markets is due to the time-varying nature of the financial markets, and gives directions for further research.

Through the study of this chapter, the reader should have gained a basic understanding of the market, and be able to build a simple backtesting environment and test it by themselves, and also have the basis for further study.
