---
title: Quantitative Trading Core Strategy Development Chapter 2_03
date: 2023-11-03 07:07:00
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
description: Most people don't really understand the marvelous markets we are in. Starting with the efficient market hypothesis, this chapter explains the fundamental characteristics of the markets we live in: randomness, chaos, and time-varying nature, through the construction of stochastic portfolios, statistical tests of randomness, and the successes and failures of machine learning in the markets.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---

## Chapter 2 Basic Perceptions about Markets Part III

##### test 03 RUNS Inspection

```python
def independent_runs(self, bin_data: str): 
      """ 
      :param bin_data: a binary string 
      :return: the p-value from the test 
      """ 
      ones_count, n = 0, len(bin_data) 
      for char in bin_data: 
          if char == '1': 
             ones_count += 1 
      p, vobs=float(ones_count / n), 1 
      tau = 2 / math.sqrt(len(bin_data)) 
      if abs(p -0.5) > tau: 
          return 0.0 
      else: 
          for i in range(1, n): 
              if bin_data[i] != bin_data[i - 1]: 
                  vobs += 1 
          num = abs(vobs - 2.0 * n * p * (1.0 - p)) 
          den = 2.0 * math.sqrt(2.0 * n) * p * (1.0- p)     
         p_val = spc.erfc(float(num / den)) 
         return p_val 
```

The RUN test is arguably the best-known randomness test, focusing on the number of RUNs that occur in a binary sequence. A RUN is defined as an identical uninterrupted sequence, and a RUN of length *K* is a sequence of *K* identical bits.The RUN test examines whether the number of RUNs in a sequence is significantly different from what would be expected assuming uniform randomness in the sequence.The number of RUNs also indicates how often the sequence oscillates between bit 0 and bit 1.In this test, the RUN is a series of consecutive trading days, indicating that the market has gone up or down continuously.

 In this test, the RUN is a series of consecutive trading days, indicating that the market has risen or fallen consecutively. Therefore, the performance of this test may imply momentum or mean reversion market anomalies. Previous academic research has shown that a market with a higher than expected number of RUNs represents a market that oscillates between "up" and "down" more rapidly than would be expected from a randomized series.

##### test 04   LONGEST RUNS TEST

```python
def longest_runs(self, bin_data: str): 
      """ 
      :param bin_data: a binary string 
      :return: the p-value from the test 
      """ 
      if len(bin_data) < 128: 
          print("t", "Not enough data to run test!") 
          return -1.0 
      elif len(bin_data) < 6272: 
          k, m = 3, 8 
          v_values = [1, 2, 3, 4] 
          pik_values = [0.21484375, 0.3671875, 0.23046875, 0.1875] 
      elif len(bin_data) < 75000: 
          k, m = 5, 128 
          v_values = [4, 5, 6, 7, 8, 9] 
          pik_values = [0.1174035788, 0.242955959, 0.249363483, 0.17517706, 0.102701071, 0.112398847] 
      else: 
          k, m = 6, 10000
          v_values = [10, 11, 12, 13, 14, 15, 16] 
          pik_values = [0.0882, 0.2092, 0.2483, 0.1933, 0.1208, 0.0675, 0.0727]
          # 计算数据块数目
      num_blocks = math.floor(len(bin_data) / m) 
      frequencies = numpy.zeros(k + 1) 
      block_start, block_end = 0, m 
      for i in range(num_blocks): 
          #将二进制序列按标记点划分
          block_data = bin_data[block_start:block_end] 
          # 记录 1的比例 
          max_run_count, run_count = 0, 0 
          for j in range(0, m): 
              if block_data[j] == '1': 
                  run_count += 1 
                  max_run_count = max(max_run_count, run_count) 
              else: 
                  max_run_count = max(max_run_count, run_count) 
                  run_count = 0 
          max_run_count = max(max_run_count, run_count) 
          if max_run_count < v_values[0]: 
              frequencies[0] += 1 
          for j in range(k): 
              if max_run_count == v_values[j]: 
                  frequencies[j] += 1 
              if max_run_count > v_values[k -1]: 
                  frequencies[k] += 1 
          block_start += m 
          block_end += m 
      chi_squared = 0 
      for i in range(len(frequencies)): 
          chi_squared += (pow(frequencies[i] - (num_blocks*pik_values[i]), 2.0)) / (num_blocks*pik_values[i]) 
      p_val = spc.gammaincc(float(k / 2), float(chi_squared / 2)) 
      return p_val 
```

As mentioned in Test 03, a RUN is any unbroken sequence of full bits. The previous test counts the number of occurrences of a RUN and checks if it is statistically significantly different from the expected number of occurrences of a RUN in a truly random binary sequence. The longest RUN test checks whether the longest RUN in a *M* bitblock consisting of bit 1 is statistically significantly longer (or shorter) than the expected length of a real random binary sequence. Since the longest RUN of

Since a significant difference in the length of the longest RUN consisting of bit 1 also implies a significant difference in the length of the longest RUN consisting of bit 0, it is sufficient to run the test only once.

In this case, the test measures whether the number of subsequent up days in a given time window is random, and whether the number of market returns corresponds to the number of times a coin is flipped. Failure of the test may indicate the presence of momentum or mean reversion effects in the market. Momentum is a property of financial markets that makes it more likely that the market will go up (or down) today if it went up (or down) the day before; mean reversion is the opposite - it suggests that the market is less likely to go up (or down) today if it went up (or down) the day before.

##### test 05 binary matrix rank test

```python
def matrix_rank(self, bin_data: str, q=32):    
      """ 
      :param bin_data: a binary string 
      :return: the p-value from the test 
      """ 
      shape = (q, q) 
      n = len(bin_data) 
      block_size = int(q * q) 
      num_m = math.floor(n / (q * q)) 
      block_start, block_end = 0, block_size ![ref2]
      if num_m>0: 
          max_ranks = [0, 0, 0] 
          for im in range(num_m): 
              block_data = bin_data[block_start:block_end]             
              block = numpy.zeros(len(block_data)) 
              for i in range(len(block_data)): 
                  if block_data[i] == '1': 
                      block[i] = 1.0 
              m = block.reshape(shape) 
              ranker = BinaryMatrix(m, q, q) 
              rank = ranker.compute_rank() 
              if rank == q: 
                  max_ranks[0] += 1 
              elif rank == (q -1): 
                  max_ranks[1] += 1 
              else: 
                  max_ranks[2] +=1 
              # 更新下标位置
              block_start += block_size 
              block_end += block_size 
              piks = [1.0, 0.0, 0.0] 
              for x in range(1, 50): 
                  piks[0] *= 1 - (1.0 / (2 ** x)) 
              piks[1] = 2*piks[0] 
              piks[2] = 1 - piks[0] - piks[1] 
              chi = 0.0 
              for i in range(len(piks)): 
                  chi += pow((max_ranks[i] - piks[i] * num_m), 2.0) / (piks[i] * num_m) 

          p_val = math.exp(-chi / 2) 
          return p_val 
       else: 
          return -1.0
```

The binary matrix rank test is interesting because it takes a binary sequence and converts it to a sequence of matrices and then tests for linear correlation between those matrices. A group of vectors is said to be linearly correlated if one or more of the vectors in the group can be defined as a linear combination of the other vectors.

##### test 06 discrete Fourier transform test

```python
def spectral(self, bin_data: str): 
      """ 
      :param bin_data: a binary string     
      :return: the p#value from the test     
      """ 
      n = len(bin_data) ![ref2]
      plus_minus_one = [] 
      for char in bin_data: 
          if char == '0': 
              plus_minus_one.append(#1) 
          elif char == '1': 
              plus_minus_one.append(1) 
			      # 计算离散傅里叶变换
			      s = sff.fft(plus_minus_one) 
			      modulus = numpy.abs(s[0:n /2]) 
			      tau = numpy.sqrt(numpy.log(1/0.05) * n) 
			# 理论峰值的个数
			count_n0 = 0.95* (n / 2) 
			# 实际峰值的个数（ m > T） 
			count_n1 = len(numpy.where(modulus < tau)[0]) 
			# 计算 p值 
			d = (count_n1 # count_n0) /numpy.sqrt(n * 0.95 * 0.05 / 4) 
			p_val = spc.erfc(abs(d) / numpy.sqrt(2)) 
			return p_val 
```

The Discrete Fourier Transform test, commonly referred to as the Spectrum test, is one of the best known tests for random number generators. It was developed by Donald Knuth to identify some of the weaknesses of the popular linear congruence series of pseudo-random number generators. The test examines a binary sequence by transforming it from the time dimension to the frequency dimension using the Discrete Fourier Transform. Once this is done, the test looks for periodicity in the transformed series, and if so, indicates that the series deviates from the true random sequence.

##### test 07 Non-overlapping pattern test

```python
def non_overlapping_patterns(self, bin_data: str, pattern="000000001", num_blocks=8):
      """ 
      :param bin_data: a binary string 
      :param pattern: the pattern to match to 
      :return: the p#value from the test 
      """
      n = len(bin_data) 
      pattern_size = len(pattern) 
      block_size = math.floor(n / num_blocks) 
      pattern_counts = numpy.zeros(num_blocks) 
      # 针对每个数据块执行操作
      for i in range(num_blocks): 
          block_start = i*block_size 
          block_end = block_start + block_size 
          block_data = bin_data[block_start:block_end] 
          # 计算模式出现次数
          j = 0 
          while j < block_size: 
              sub_block = block_data[j:j + pattern_size]             
              if sub_block == pattern: 
                  pattern_counts[i] += 1 
                  j += pattern_size 
              else: 
                  j += 1 
      #计算理论均值与方差
      mean = (block_size # pattern_size+1) / pow(2, pattern_size) 
      var = block_size * ((1 / pow(2, pattern_size)) # (((2 * pattern_size) - 1) / (pow(2, pattern_size * 2))))
      # 计算这些模式的 Chi Squared统计量 
      chi_squared = 0 
      for i in range(num_blocks): 
          chi_squared += pow(pattern_counts[i] # mean, 2.0) / var 
      # 计算 p值 
      p_val =s pc.gammaincc(num_blocks / 2, chi_squared / 2) 
      return p_val 
```

Unlike the previous two tests, the non-overlapping and overlapping pattern tests (tests 07 and 08) are well suited for quantitative finance, especially for the development of trading strategies. The non-overlapping pattern test looks at the frequency of a pre-specified pattern in a binary string. If the frequency is statistically significantly different from the expected number of occurrences of a true random sequence, then this would indicate that the sequence is non-random and that the probability of the pattern we are testing for is too high or too low.

Suppose we believe that the market is more likely than a true random sequence to exhibit the following pattern 101100011111..., the Fibonacci pattern. Next, we will use this test and test 08 to accept or reject the hypothesis at a given confidence level. I like these two tests because they provide the quantitative analyst with a reliable theoretical method to test the significance of a given pattern in a market return series. These tests can be applied to test concepts such as Elliott Wave Theory and the Fractal Market Hypothesis.

##### Test 08 Overlapping Patterns Test

```python
def  overlapping_patterns(self,  bin_data:  str,  pattern_size=9, block_size=1032):
      """
      :param pattern_size: the length of the pattern 
      :return: the p-value from the test 
      """ 
      n = len(bin_data) 
      pattern = "" 
      for i in range(pattern_size): 
          pattern += "1" 
      num_blocks = math.floor(n / block_size) 
      lambda_val = float(block_size - pattern_size+1) / pow(2, pattern_size)     
      eta = lambda_val / 2.0 
    
      piks = [self.get_prob(i, eta) for i in range(5)] 
      diff = float(numpy.array(piks).sum()) 
      piks.append(1.0 - diff)
      pattern_counts = numpy.zeros(6) 
      for i in range(num_blocks): 
          block_start = i * block_size 
          block_end = block_start+block_size 
          block_data = bin_data[block_start:block_end]         #计算模式出现次数
          pattern_count = 0 
          j = 0 
          while j < block_size: 
              sub_block = block_data[j:j+pattern_size]             
              if sub_block == pattern: 
                  pattern_count += 1 
                  j += 1 
              if pattern_count <= 4: 
                  pattern_counts[pattern_count] += 1 
              else: 
                  pattern_counts[5] += 1 
      chi_squared = 0.0 
      for i in range(len(pattern_counts)): 
          chi_squared += pow(pattern_counts[i] - num_blocks*piks[i], 2.0) / (num_blocks*piks[i]) 
      return spc.gammaincc(5.0 / 2.0, chi_squared / 2.0) 
      
def get_prob(self, u, x): 
      out = 1.0 * numpy.exp(-x) 
      if u != 0: 
          out = 1.0 * x * numpy.exp(2 * -x)*(2 ** -u)*spc.hyp1f1(u + 1, 2, x)
    return out 
```

The difference between the overlapping pattern test and the non-overlapping pattern test is the way the patterns are tested. In the non-overlapping pattern test, once a pattern of length *M* is successfully matched, the search continues to the end of the pattern. With the overlapping pattern test, the search continues from the next bit.
