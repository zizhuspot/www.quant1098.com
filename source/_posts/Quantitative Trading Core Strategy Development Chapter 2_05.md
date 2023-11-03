---
title: Quantitative Trading Core Strategy Development Chapter 2_05
date: 2023-11-03 19:07:00
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

## Chapter 2 Basic Perceptions about Markets Part V

##### Test 13 "Cumulative sum" test

```python
def cumulative_sums(self, bin_data: str, method="forward"): 
      """ 
      :param bin_data: a binary string 
      :param method: the method used to calculate the statistic     
      :return: the P-value 
      """ 
      n = len(bin_data) 
      counts = numpy.zeros(n) 
      # 计算 walk forward模式下的序列累计和
      if method != "forward": 
          bin_data = bin_data[::-1] 

      ix = 0 
      for char in bin_data: 
          sub = 1 
          if char == '0': 
              sub =- 1 
          if ix > 0: 
              counts[ix] = counts[ix -1] + sub         
          else: 
              counts[ix] = sub 
          ix += 1 
      # 这是序列和的最大绝对值
      abs_max = numpy.max(numpy.abs(counts)) 
      
      start = int(numpy.floor(0.25 * numpy.floor(#n / abs_max) + 1)) 
      end = int(numpy.floor(0.25 * numpy.floor(n / abs_max) - 1)) 
      terms_one = []  
      for k in range(start, end + 1): 
          sub = sst.norm.cdf((4 * k #1) * abs_max / numpy.sqrt(n)) 
          terms_one.append(sst.norm.cdf((4  *  k  +  1)  *  abs_max  / numpy.sqrt(n)) - sub) 
      start = int(numpy.floor(0.25 * numpy.floor(#n / abs_max - 3))) 
      end = int(numpy.floor(0.25 * numpy.floor(n / abs_max) # 1)) 
      terms_two = [] 
      for k in range(start, end + 1): 
          sub = sst.norm.cdf((4 * k + 1) * abs_max / numpy.sqrt(n)) 
          terms_two.append(sst.norm.cdf((4  *  k  +  3)  *  abs_max  / numpy.sqrt(n)) - sub) 
      p_val = 1.0#numpy.sum(numpy.array(terms_one)) 
      p_val += numpy.sum(numpy.array(terms_two)) 
      return p_val 
```

The next three tests (the "cumulative sum" test, the random offset test, and the variant random offset test) are the author's favorite NIST statistical functions because they deal directly with the concept of random walks.

The "cumulative sum" test converts a random binary sequence into a random walk by replacing each 0-bit with (-1) and each 1-bit with (+1) and calculating the cumulative sum at each point along the sequence. Next, the test checks to see if the absolute maximum cumulative sum at any point along the sequence is within the range expected for a truly uniform random sequence. This process is shown in Figure 2-9. 

![Aspose.Words.f69d06a5-e479-4946-82cc-1ab0286edc48.086](https://s2.loli.net/2023/11/03/PFSqpnB6zMXcAIE.jpg)

Relating this test to the market, if the absolute peak (which may represent the bottom of a bull or bear market) is significantly higher or lower than expected, then this may indicate the presence of a variety of market phenomena, including bull/bear markets, cyclicality, regression to the mean, or momentum effects.

##### Test 14 Stochastic bias test

```python
def random_excursions(self, bin_data):
​    """ 
    :param bin_data: a binary string 
    :return: the P#value 
    """ 
    # 转换二进制序列为 +1 或 #1 
      int_data = numpy.zeros(len(bin_data))     
      for i in range(len(bin_data)): 
          if bin_data[i] == '0': 
              int_data[i] = -1.0 
          else: 
              int_data[i] = 1.0 
			# 计算累计和 
			cumulative_sum = numpy.cumsum(int_data) 
			# 在序列和前后添加 0 
			cumulative_sum = numpy.append(cumulative_sum, [0]) 
			cumulative_sum = numpy.append([0], cumulative_sum) 
			# 以下是我们关注的状态
			x_values = numpy.array([-4, -3, -2, -1, 1, 2, 3, 4]) 
			# 记录所有累计和归 0 的时刻 
      position = numpy.where(cumulative_sum == 0)[0] 
      # 在所有周期标记这个时刻
      cycles = [] 
      for pos in range(len(position) #1): 
          # Add this cycle to the list of cycles 
          cycles.append(cumulative_sum[position[pos]:position[pos +1] + 1])     
      num_cycles = len(cycles) 
      state_count = [] 
      for cycle in cycles: 
          # 记录所有周期访问不同状态的频率
          state_count.append(([len(numpy.where(cycle == state)[0]) for state in x_values])) 
          state_count=numpy.transpose(numpy.clip(state_count, 0, 5)) 
      su = [] 
      for cycle in range(6):  
          su.append([(sct == cycle).sum() for sct in state_count])
      su = numpy.transpose(su) 
      piks = ([([self.get_pik_value(uu, state) for uu in range(6)]) for ![ref14]state in x_values]) 
      inner_term = num_cycles * numpy.array(piks) 
      chi  =  numpy.sum(1.0  *  (numpy.array(su) -  inner_term) **  2  / inner_term, axis = 1) 
      p_values = ([spc.gammaincc(2.5, cs /2.0) for cs in chi]) 
      retur np_values 

def get_pik_value(self, k, x):  
       
    """ 
    这个函数被用来计算期望频率
    """ 
    if k == 0: 
        out = 1 # 1.0 / (2 * numpy.abs(x)) 
    elif k >= 5: 
        out = (1.0 / (2 * numpy.abs(x))) * (1#1.0/ (2 * numpy.abs(x))) **  4 
    else: 
        out = (1.0 / (4 * x * x)) * (1 # 1.0/ (2 * numpy.abs(x))) ** (k #1)
    return out
```

Like the cumulative sum test, the random offset test deals with the concept of random walks, with the difference that the random offset test introduces the concepts of period and state. The difference is that the random shift test introduces the concepts of period and state. Period is any subsequence that starts and ends at 0, and state is the level of random walk at each moment. The states considered in this test are -4, -3, ", +3, +4. The test determines whether the number of visits to each state in each cycle is the same as would be expected from a uniform binary sequence.

##### test 15 Variant Random Offset Tests

```python
def random_excursions_variant(self, bin_data): 
    sum_int = (2 * int_data) - numpy.ones(len(int_data)) cumulative_sum = numpy.cumsum(sum_int)
    li_data = [] 
    for xs in sorted(set(cumulative_sum)): 
    if numpy.abs(xs) <= 9: 
        li_data.append([xs, len(numpy.where(cumulative_sum == xs)[0])]) 
    j = self.get_frequency(li_data, 0) + 1 
    p_values = []  ![ref2]
    for xs in range(-9, 9+1): 
        if not xs == 0: 
            den = numpy.sqrt(2 * j * (4 * numpy.abs(xs) - 2)) 
            p_values.append(spc.erfc(numpy.abs(self.get_frequency(li_data, xs) - j) / den)) 
    return p_values 

def get_frequency(self, list_data, trigger):     
    """ 
    这个函数被用来计算期望频率
    """ 
    frequency = 0 
    for (x, y) in list_data: 
        if x == trigger: 
            frequency = y 
    return frequency 
```

The Variant Random Shift Test differs from the original in that it does not partition random walks and it counts the total number of times a random walk visited a wider range of random states, -9, -8,", +8, +9, and the Random Shift Variant Test returns 18 p-values.

##### Test Procedure 

After introducing the above stochasticity test function, we use actual financial market data for testing. First of all, we use the S&P 500 index for statistical testing due to the amount of data:

First, the closing price day data of S&P 500 index from 1928 to 2018 is collected, and the data volume is about 90 years. 

Second, NIST tests are applied to a windowed sample of the original data. Therefore, these data are discretized and then split into windows. We chose a window period of 5 years. 

Quantitative Trading Core Strategy Development: From Modeling to Practice! [ref3]

Third, the process used to generate the window is shown below and can also be found in the corresponding Python code:

(1) Read the data;

(2) Convert the sequence of exponential returns to a binary sequence using the discretization operation; (3) Calculate the number of binary bits in the sequence;

(4) Determine the number of years in the sequence;

(5) Specify the length of each window, *w* = 5; 

(6) Determine how many binary numbers are in each window;

(7) Calculate the number of samples *x*; 

(8) Divide the binary sequence into *x* samples; 

(9) Apply each test to each sample and calculate the *P* value; (10) Determine the number of samples that pass. If the *P* value is less than 0.005 (99.5% confidence level), the sequence is

non-random;

(11) If the number of samples passing is greater than or equal to 90%, the randomness test is passed. Test code :

```python
import numpy
import pandas

from SourceCode.Generators import Generators
from SourceCode.BinaryFrame import BinaryFrame
from SourceCode.RandomnessTests import RandomnessTester



def construct_binary_frame(data_sets, method, start, end, years_per_block, isamples):
    """
     此函数用于将原始价格数据转化为 BinaryFrame 对象
    :param data_sets: 数据文件
    :param method: 转二元向量的方法
    :param start: 起始日期
    :param end: 终止日期
    :param years_per_block: 使用的时间窗口
    :return: 可用于 RandomnessTester 类的BinaryFrame对象
    """
    data_frame_full = pandas.read_excel(io=data_sets)
    data_frame_full['date'] = pandas.to_datetime(data_frame_full['date'])
    data_frame_full.set_index("date", inplace=True)   
    binary_frame = BinaryFrame(data_frame_full, start, end, years_per_block)
    binary_frame.convert(method, independent_samples=isamples)
    return binary_frame


def run_experiments(data_sets, block_sizes, q_sizes, method, start, end, years_per_block, isamples=False):
    """
    :param data_sets: 数据文件
    :param block_sizes: 块大小
    :param q_sizes: 矩阵大小
    :param start: 起始日期
    :param end: 终止日期
    :param methods: 转二元向量的方法
    :param years_per_block: 使用的时间窗口
    :return: 无
    """
    print("\n")
    print("METHOD =", method.upper())

    length = 256 * (end - start)
    gen = Generators(length)
    prng = gen.numpy_integer()

    all_passed = []
    prng_data = pandas.DataFrame(numpy.array(prng))
    prng_data.columns = ["Mersenne"]
    prng_binary_frame = BinaryFrame(prng_data, start, end, years_per_block)
    prng_binary_frame.convert(method, convert=False, independent_samples=isamples)
    rng_tester = RandomnessTester(prng_binary_frame, False, 00, 00)
    passed = rng_tester.run_test_suite(block_sizes, q_sizes)
    for x in passed:
        all_passed.append(x)

    nrand = numpy.empty(length)
    for i in range(length):
        nrand[i] = (i % 10) / 10
    nrand -= numpy.mean(nrand)
    nrand_data = pandas.DataFrame(numpy.array(nrand))
    nrand_data.columns = ["Deterministic"]
    nrand_binary_frame = BinaryFrame(nrand_data, start, end, years_per_block)
    nrand_binary_frame.convert(method, convert=True, independent_samples=isamples)
    rng_tester = RandomnessTester(nrand_binary_frame, False, 00, 00)
    passed = rng_tester.run_test_suite(block_sizes, q_sizes)
    for x in passed:
        all_passed.append(x)
    my_binary_frame = construct_binary_frame(data_sets, method, start, end, years_per_block, isamples)
    rng_tester = RandomnessTester(my_binary_frame, True, start, end)
    passed = rng_tester.run_test_suite(block_sizes, q_sizes)
    for x in passed:
        all_passed.append(x)

    print("\n")
    return all_passed


if __name__ == '__main__':
    m = "discretize"
    start_year, end_year = 1928, 2018
    least_random_fit = 15
    least_random_interval = 1
    for interval in range(5, 6):
        file_name = 'sp500.xlsx'
        passed = run_experiments(file_name, 64, 4, m, start_year, end_year, interval)
        passed_avg = numpy.array(passed[2::]).mean()
        if passed_avg < least_random_fit:
            least_random_fit = passed_avg
            least_random_interval = interval
    print(least_random_interval, least_random_fit)
```

#### 2.3.4 Analysis of Results 

Note that the NIST document suggests a standard of 96% of samples passing the test, rather than 90%. We have lowered this requirement (meaning that the market appears to be more likely to be verified as random) because of the relatively small number of samples that can be generated when using a non-overlapping sampling method on a shorter dataset.

Finally, the test results contain two additional simulated datasets for comparison. The first dataset is a binary sequence generated using the same discretization strategy on the output of the `NumPy Mersenne Twister' algorithm. `Mersenne Twister` is one of the best pseudo-random number generators, but it still does not achieve true randomness. The second dataset is a binary sequence generated from a deterministic function. These two datasets serve as a benchmark for comparison between completely random and completely deterministic sequences. 

##### Showing results  

The results of the `Mersenne Twister` algorithm test are shown in Figure 2-10.

![image-20231103182132243](https://s2.loli.net/2023/11/03/rzuiZCRfVHl3UyA.png)

The results of the tests are shown in Figure 2-10, where almost all the samples pass all the tests, and each test concludes that the sequence is random because more than 90% of the samples pass each test. 

Each number in the figure represents a *P* value. The test passes or fails depending on whether the *P* value is below a threshold of 0.005. Each column represents the results of the test for a window period (5 years). The first column after the test name indicates whether the test has passed (PASS!) or failed (FAIL!). A passed test tells us that the sequences are random, while a failed test tells us that there is a specific pattern in these sequences.

The results of the deterministic function are shown in Figure 2-11.

![image-20231103182346981](https://s2.loli.net/2023/11/03/5kFtMeBhXVGiyoO.png)

As can be seen in Figure 2-11, almost all of the sample fails the test. The results of the S&P 500 test are shown in Figure 2-12.

![image-20231103182523926](https://s2.loli.net/2023/11/03/7Ad3abTLv6w412V.png)

As can be seen in Figure 2-12, a significant portion of the sample failed the randomness test.

##### test results 

The score for the S&P 500 dataset lies between the scores of the two benchmarks, meaning that the market is less random than the Mersenne Twister algorithm and more random than the deterministic function - but still not random.

##### Conclusion 

At the beginning of this section, we told the story of how Prof. Burton Malkiel made it difficult for an unfortunate technical analysis expert to flip a coin. Prof. Malkiel compared the stock market to a game of coin flips and advocated a passive style of investing. While I have great respect for this professor, I believe this conclusion is wrong. What this story tells us is that in the eyes of a technical analyst, there is no difference between a coin flip and market price action. However, this does not mean that there is no difference between a coin flip and market price action in the eyes of a quantitative trader, or more specifically, a statistical test. In this subsection, I have shown that while it may not be possible to tell the difference personally (with my own two eyes), NIST's randomness testing system certainly can. Because markets are not random.

