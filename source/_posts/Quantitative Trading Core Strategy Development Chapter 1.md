---
title: Quantitative Trading Core Strategy Development Chapter 1
date: 2023-11-02 16:07:00
categories:
  - Quantitative Trading
tags:
  - Digital currency
  - Chapter
  - Quantitative tool
  - Kuangbiao
  - Strategy
  - quant1098.com
  - Development
  - AIOQuant
description: This chapter will introduce the trading view and even the world view that quantitative traders should have. In this chapter, the author verifies several important principles in the field of quantitative research by means of numerical simulation, Gambler's Bankruptcy Theory illustrates the importance of money management, the Law of Large Numbers is very important in strategy development and verification, the Central Limit Theorem is pivotal in the financial risk-control quota; and the Principle of Maximum Entropy constrains the world at a deeper level.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---

## Chapter 1: Trading is a Game of Probabilities

This chapter will introduce the trading view and even the world view that quantitative traders should have. In this chapter, the author verifies several important principles in the field of quantitative research by means of numerical simulation: Gambler's Bankruptcy Theory illustrates the importance of money management, the Law of Large Numbers is very important in strategy development and verification, the Central Limit Theorem is pivotal in the financial risk-control quota; and the Principle of Maximum Entropy constrains the world at a deeper level.

## 1.1 From the Theory of Gambler's Bankruptcy

  Consider a game in which the weight of a coin is unevenly distributed, with a 90% probability that heads will come up on each toss, as opposed to a 10% probability that tails will come up. Obviously, when gambling with this coin, there is a strategy that has a very high probability of winning, and that is to bet on heads. So, when we know this information, is it possible to lose money? The answer is: if we don't take care of position management, we can still lose money. We use the following MATLAB-based program code to explore the results of repeating the investment T-steps with a fixed percentage of positions at a time.



```R
%胜率
p=0.9;
%重复试验次数
T-100000:
%投资比例
f-0:0.01:17n=length(f);
result=zeros(n,1);
for i=l:n
ret=0;forj=1:Tx=rand(1);if x<=pret=ret+log(f(i)+1);elseret=ret+log(-f(i)+1);end
end
result(i)=ret;
end

```

The results are shown in the figure

![image-20231102144143933](https://s2.loli.net/2023/11/02/8D3IUEgYK6em1an.png)

We were surprised to find that the cumulative logarithmic returns went up and then down as the percentage of each bet increased, and that the closer the percentage got to 1, the bigger the drop in returns (the last point on the graph is a negative infinity and can't be plotted), a phenomenon known as the Gambler's Ruin. Gambler's Ruin theory reveals a terrible fact in trading, even if the investor has the absolute advantage (in this case the winning rate is 90%, which is usually impossible), poor position management may still make us lose our money. So, is there a way to calculate the position for each bet? Yes, there is. It's called the "Kelly formula".
$$
\begin{align*} 
f &= \frac{bp - q + s}{2s} \\
p &= \frac{f + q - s}{2}
\end{align*}
$$
included among these 

```
f - futures price
b - margin factor
p - spot price
q - Risk-free rate
s - Warehouse receipt service charge rate
```

We calculate this formula by substituting the numbers from the previous example to get f = 0.8, which happens to be the above chart's cumulative
position corresponding to the highest logarithmic return. This formula first appeared in 1956 in the paper ANew Interpretation of the Information Rate by Bell Labs scientist John L. Kelly.

The formula was first applied to blackjack gambling by math professor Edward Thorp, who used Kelly's formula to manage blackjack bet sizes, and later extended the principle to money management in trading. Recently, in response to a question about whether he uses the Kelly formula in his asset allocation, he replied that if you bet half of what the Kelly formula suggests each time, you will get about 3/4 of the return with half the volatility. He believes that betting half of a partially profitable position is psychologically much better. Thorpe's views represent those of the vast majority of practitioners.

Going back to the coin toss game, if we use a bet size of 80% and then after one loss (with a margin of 10%), we could lose 80% of our total wealth. If we are unlucky and suffer two losses in a row, the total loss would be 96%. A gambler with a little common sense doesn't need any formula to know that this is too risky. For practitioners, therefore, Kelly's formula provides a useful guide to capping positions in risky assets. The point of the game is always how to minimize risk; after all, for systematic traders, Kelly's formula focuses on the profit side of the equation and ignores the risk side.

Indeed, recent research by Vince & Zhu, Optimal Betting Sizes for the Game of Blackjack, suggests that Kelly's recommended bet sizes need to be adjusted significantly when combined with actual risk and when gambling only for a limited period of time. This confirms what practitioners have been doing in the real world for a long time.

## 1.2 The Law of Large Numbers and the Central Limit Theorem

  Kelly's formula solves the problem of managing positions in static repeatable situations, and its validity is guaranteed by the statistically important Law of Large Numbers. The law of large numbers has many mathematical forms, which we can loosely summarize as follows: the frequency of occurrence of a random event tends to the expected probability when the random event occurs a sufficiently large number of times, provided that the test conditions remain unchanged. Borrowing the example from the previous subsection, the validation code is as follows.

```R
%胜率
p=0.9;
% 重复试验次数
T=10000;
result=zeros(T,1);
win=0;
count=0;
forj=1:T
% 生成0~1之间的随机数
x=rand(1);
count=count+1;
    if x<=p
        win=win+l;
    end

result(j)=win/count;
end
```

The results are shown in Figure 1-2, and we can see that as the game progresses, the calculated win rate is very close to the true probability of 90% by the time the game is played about 2000 times.

![image-20231102145752650](https://s2.loli.net/2023/11/02/N28hFajJxsRqlmL.png)

The power of the law of large numbers in trading is more often seen in high-frequency trading, as other types of trading have difficulty reaching the required number of trades in a short period of time. At the strategy design stage, we can benefit from this ~thinking~ about how a strategy will perform in the future by looking at its winning percentage and profit/loss ratio over a period of time as an intrinsic property of the strategy. The basis on which we can do this is the law of large numbers.
Another important theorem in statistical theory is the Central Limit Theorem. This theorem states that, given an arbitrarily distributed population, each time n samples are randomly drawn from the population, a total of m samples are taken and the mean of each of these m sets of samples is obtained. The distribution of these means is close to the Gaussian distribution (also known as the normal distribution).
Following the example of the coin toss, we first generate the distribution.

```R
% 胜率
p=0.9;
% 重复试验次数
T=100000;
result=zeros(T,1);
for j=l:T
x=rand(1);
if x<=p
result(j)=l;
elseresult(j)=-1;
end
end
```

We visualize the generated data by plotting it in a histogram, as shown in Figure 1-3

![image-20231102150206285](https://s2.loli.net/2023/11/02/pqwdSUN5KW84kVc.png)

As can be seen in Figure 1-3, most (90%) of the data are concentrated in 1, with a very uneven distribution. From this distribution, we randomly sample 10,000 groups of 1,000 data each.

```matlab
% 10,000 randomly selected groups of 1000 data each
dist=zeros(10000,1);
for1=1:10000
    randindx=randperm(T);
    dist(i)=sum(result(randindx(1:1000)));
end

```

We can then plot these 10,000 sets of data in a histogram, as shown in Figure 1-4.

![image-20231102150523018](https://s2.loli.net/2023/11/02/DElbdJnGiPCf72Z.png)

As can be seen in Fig. 14, we have sampled from an extremely inhomogeneous distribution to obtain a bell-shaped curve that approximates a Gaussian cloth. The world is full of Gaussian distributions. As a mathematical idealization, we will never get a complete Gaussian distribution. But it is a universal pattern that appears again and again at different scales and in different fields. Measurement errors, height variations, and molecular velocity distributions all tend to favor Gaussian distributions. Because essentially upstream these processes sum up a myriad of subtle fluctuations to the total fluctuation, they day by day eliminate all information about the underlying process except for the mean and variance. So one of the costs is that statistical models based on Gaussian distributions cannot reliably identify the underlying processes.

But the wide distribution of Gaussian distributions in nature is only one reason why people prefer to model with them Another reason why the Gaussian distribution is our preferred choice is that it represents a state of ignorance. When all we know about a variable is its mean and variance, the Gaussian distribution is our only choice. That is, the Gaussian distribution is the most natural expression of our state of ignorance, because if all we are willing to assume is the mean and variance of the distribution, then the Gaussian distribution is the shape that can accomplish this in a variety of ways without introducing any new assumptions.

In this way, the Gaussian distribution is the distribution that is most -consistent with our assumptions. Or more precisely, it is the most consistent with our modeling assumptions. If we don't think that the distribution should be Gaussian, then this means that we know more information about the model that is relevant to this model and that affects the model's performance.

In contrast to the law of large numbers, the application of the central limit theorem in finance is mainly in the area of risk control. The distribution of the price series of an asset, although unknown, is always close to a Gaussian distribution in terms of their rate of change (i.e., logarithmic rate of return) according to the Central Limit Theorem. This property provides a theoretical basis for financial risk control.

## 1.3 The Maximum Principle

In the previous subsection we mentioned that nature favors the Gaussian distribution. Although the market is not random at the transaction level, the amount of information constrains us to reduce it to randomness, and the Gaussian distribution represents our ignorance. In information theory, a measure of the amount of information is generally provided by the information entropy formula.


$$
\begin{align*}
H(p) = -\sum_{i=1}^n p_i \log_b(p_i) 
\end{align*}
$$
The maximum entropy principle applies this metric to the problem of choosing a probability distribution. Simply put, the maximum entropy principle is that the distribution that is most likely to occur is also the one with the greatest information entropy, and that the distribution with the greatest entropy is the most conservative one that obeys its constraints.

This sentence seems to be more difficult to understand, let's use the example of a coin toss to explain. Suppose there is 1 coin, then after 100 coin tosses, the number of times heads/tails face up is (90, 10), (70, 30) (50, 50), (30, 70), (10, 90) information, according to the formula is calculated as follows.

```R
p <-list()
PSA<-c(90,10)
p$B <- c(30,70)
psc <- c(50,50)
psD <- c(70,30)
psE <- c(10,90)
pnorm <- lapply( p，function(q) q/sum(q))
H <- sapply( p_norm ，function(q) - sum(ifelse(g==0,0,q*log(g))) )
```

The results of the calculations are as follows.

```
A           B          C          D        E
0       0.6108643 0.6931472 0.6108643 0.3250830
```

It can be seen that the third scenario (i.e., with the (50,50) distribution), without making any a priori assumptions about the coin, has the highest information entropy and also happens to provide no information about the coin. If the actual result is A, then we can infer from this that the distribution of the weight of the coin is inhomogeneous. Therefore, the information entropy is exactly what its name implies.
information entropy is, as the name implies, a measure of the amount of information that can be given about the outcome (the reverse).

Nature (the objective world) tends to produce distributions with high information entropy. Returning to the previous subsection, we have verified through numerical simulations that summing (or averaging) the results of independent sampling from each distribution tends to produce empirical distributions with a Gaussian shape. The shape does not contain any information about the underlying process other than location and variance, so it has the maximum information entropy. We use this principle in our research because it has a very good track record in solving real-world problems.

## 1.4 Summary of the Chapter

This chapter, as Chapter 1 of this book, focused on important statistical properties of the world. Starting with the counterintuitive gambler's bankruptcy theory, the chapter naturally leads to important laws in quantitative research such as Kelly's formula and the law of large numbers, and along the way introduces the central limit theorem as well as the maximum entropy principle and other fundamentals that in fact rule the world but are not well known, which provide the reader with a foundation for constructing a probabilistic view of trading. Let the author conclude this chapter with a quote from E.TJaynes.
The probability distributions used by Maxwell, Boltzmann, and Gibbs are not real in nature; they are merely descriptions of nature based on incomplete human information, and the best possible predictions are generated from the information contained therein. Probability distributions are not, in fact, "right" or "wrong"; rather, some distributions make better predictions than others simply because they contain more relevant information.

