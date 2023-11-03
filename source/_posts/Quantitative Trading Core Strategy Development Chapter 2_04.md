---
title: Quantitative Trading Core Strategy Development Chapter 2_04
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
description: Most people don't really understand the marvelous markets we are in. Starting with the efficient market hypothesis, this chapter explains the fundamental characteristics of the markets we live in  randomness, chaos, and time-varying nature, through the construction of stochastic portfolios, statistical tests of randomness, and the successes and failures of machine learning in the markets.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---
## Chapter 2 Basic Perceptions about Markets Part IIII

##### Test 09 Generalized Statistical Tests

```python
def universal(self, bin_data: str): 
      """ 
      :param bin_data: a binary string     
      :return: the p#value from the test     
      """ 
      n = len(bin_data) 
      pattern_size = 5 
      if n >= 387840: 
          pattern_size = 6 
      if n >= 904960: 
          pattern_size = 7 ![ref2]
      if n >= 2068480: 
          pattern_size = 8 
      if n >= 4654080: 
          pattern_size = 9 
      if n >= 10342400: 
          pattern_size = 10 
      if n >= 22753280: 
          pattern_size = 11 
      if n >= 49643520: 
          pattern_size = 12 
      if n >= 107560960: 
          pattern_size = 13 
      if n >= 231669760: 
          pattern_size = 14 
      if n >= 496435200: 
          pattern_size = 15 
      if n >= 1059061760: 
          pattern_size = 16 
      if 5 < pattern_size < 16: 

          # 统计最长的二进制序列模式的长度
          ones = "" 
          for i in range(pattern_size):             
              ones += "1" 
					# 决定状态列表有多长 
					num_ints = int(ones, 2) 
					vobs = numpy.zeros(num_ints + 1) 
					# 记录数据块是否被初始化或加总
					num_blocks = math.floor(n /pattern_size) init_bits = 10 * pow(2, pattern_size) test_bits = num_blocks # init_bits 
					c = 0.7 # 0.8 / pattern_size + (4+32/pattern_size) * pow(test_bits, ](Aspose.Words.f69d06a5#e479#4946#82cc#1ab0286edc48.057.png)#3 / pattern_size) / 15
          variance = [0, 0, 0, 0, 0, 0, 2.954, 3.125, 3.238, 3.311, 3.356, 3.384, 3.401, 3.410, 3.416, 3.419, 3.421]![ref11] 
          expected = [0, 0, 0, 0, 0, 0, 5.2177052, 6.1962507, 7.1836656, 8.1764248, 9.1723243, 10.170032, 11.168765, 12.168070, 13.167693, 14.167488, 15.167379]
          sigma = c * math.sqrt(variance[pattern_size] / test_bits) 
          cumsum = 0.0 
          for i in range(num_blocks): 
              block_start = i*pattern_size 
              block_end = block_start + pattern_size 
              block_data = bin_data[block_start: block_end] 
              # 计算我们处于哪个状态
              int_rep = int(block_data, 2) ![ref2]
              # 初始化状态列表 
              if i < init_bits: 
                  vobs[int_rep] = i + 1 
              else: 
                  initial = vobs[int_rep] 
                  vobs[int_rep] = i + 1 
                  cumsum += math.log(I # initial + 1, 2) 
							# 计算统计量 
							phi = float(cumsum / test_bits) 
							stat = abs(phi # expected[pattern_size]) / (float(math.sqrt(2)) * sigma) 
          p_val = spc.erfc(stat)         
          return p_val 
      else: 
          return #1.0 
```

Maurer's generalized statistical test checks that the distances (in bits) between repeating patterns are the same as would be expected for a uniform random sequence. The theoretical idea behind this test and the linear complexity test is that non-random sequences should not be compressible. Consider the fact that most pseudo-random number generators have a period, and when that period runs out, the bits in the sequence begin to repeat. When this happens, the distance between matching patterns decreases, and eventually a generalized statistical test will consider the sequence to be non-random. Unfortunately, this test requires a very large amount of data to be statistically significant. In fact, if daily quotes were used, about 1500 years of daily data would be required. 

##### test 10 Linear Complexity Test 

```python
def linear_complexity(self, bin_data, block_size=500):     
      """ 
      :param bin_data: a binary string ![ref12]
      :param block_size: the size of the blocks to divide bin_data into. Recommended block_size>= 500 
      :return: 
      """ 
      dof = 6 
      piks = [0.01047, 0.03125, 0.125, 0.5, 0.25, 0.0625, 0.020833] 
      t2 = (block_size/3.0+2.0/9) / 2 ** block_size 
      mean  = 0.5 * block_size + (1.0 / 36) * (9 + (-1) ** (block_size + 1)) - t2 
      num_blocks = int(len(bin_data) / block_size) 
      if num_blocks > 1: 
          block_end = block_size 
          block_start = 0 
          blocks = []  
          for i in range(num_blocks): 
              blocks.append(bin_data[block_start:block_end])            
              block_start += block_size 
              block_end += block_size 
          complexities = [] 
          for block in blocks: 
              complexities.append(self.berlekamp_massey_algorithm(block)) ![ref13]
          t = ([-1.0 * (((-1) ** block_size) * (chunk - mean) + 2.0 / 9) for ![ref8]chunk in complexities]) 
          vg = numpy.histogram(t, bins = [-9999999999, -2.5, -1.5, -0.5, 0.5, ![ref12]1.5, 2.5, 9999999999])[0][::-1] 
          im = ([((vg[ii] - num_blocks * piks[ii]) ** 2) / (num_blocks * piks[ii]) for ii in range(7)]) 
          chi_squared = 0.0 
          for i in range(len(piks)): 
          chi_squared += im[i] 
          p_val = spc.gammaincc(dof / 2.0, chi_squared / 2.0)         
          return p_val 
      else: 
          return -1.0 

def berlekamp_massey_algorithm(self, block_data):     
      """ 
      :param block_data:
      :return: 
      """ 

      n = len(block_data) 
      c = numpy.zeros(n) 
      b = numpy.zeros(n) 
      c[0], b[0] = 1, 1 
      l, m, i= 0, -1, 0 
      int_data = [int(el) for el in block_data]     
      while i < n: 
          v = int_data[(i- l):i] 
          v = v[::-1] 
          cc = c[1:l + 1] 
          d = (int_data[i] + numpy.dot(v, cc)) % 2         
          if d == 1: 
              temp = copy.copy(c) 
              p = numpy.zeros(n) 
              for j in range(0, l): 
                  if b[j] == 1: 
                      p[j + i - m] = 1 
              c = (c + p) % 2 
              if l <= 0.5 * i: 
                  l = i + 1- l 
                  m = i 
                  b = temp ![ref2]
         i += 1 
      return l 
```

From a purely computer science perspective, the linear complexity test is one of the most interesting tests in the NIST suite. Like generalized statistical tests, the linear complexity test deals with the compressibility of a binary sequence. The linear complexity test is designed to check this by approximating the binary sequence with a Linear Feedback Shift Register (LSFR), which is a cascaded flip-flop that can be used to store state information. the LSFR input bits (or bits) are a linear function of the previous state.

The linear complexity test works as follows: the `Berlekamp-Massey' algorithm is used to compute the approximate shortest LSFR, and then the length of this LSFR is compared to the expected value of a true uniform random sequence. The idea is that if the LSFR is significantly shorter than expected, then the sequence is compressible and therefore non-random.

##### test 11 Serial test

```python
def serial(self, bin_data, pattern_length=16, method="first"):    
      """ 
      :param bin_data: a binary string 
      :param pattern_length: the length of the pattern (m) 
      :return: the P value 
      """ 
      
      n = len(bin_data) 
      # 加入 m#1 bits 到末尾 
      bin_data += bin_data[:pattern_length # 1:] 
      # 生成最大模式
      max_pattern = '' 
      for i in range(pattern_length+1):         
          max_pattern += '1' 
      # 记录各种模式出现的次数
      vobs_one=numpy.zeros(int(max_pattern[0:pattern_length:], 2) + 1)           
      vobs_two=numpy.zeros(int(max_pattern[0:pattern_length#1:], 2) + 1) 
      vobs_thr=numpy.zeros(int(max_pattern[0:pattern_length#2:], 2) + 1) 
      for i in range(n): 
          # 计算哪种模式出现了
          vobs_one[int(bin_data[i:i+pattern_length:], 2)] += 1 
          vobs_two[int(bin_data[i:i+ pattern_length#1:], 2)] += 1 
          vobs_thr[int(bin_data[i:i+ pattern_length#2:], 2)] += 1 
          
      vobs = [vobs_one, vobs_two, vobs_thr] 
      sums = numpy.zeros(3) 
      for i in range(3): 
          for j in range(len(vobs[i])): 
              sums[i] += pow(vobs[i][j], 2) 
          sums[i] = (sums[i] * pow(2, pattern_length # i)/n) # n 
			# 计算统计量和 p 值  
			del1 = sums[0] # sums[1] 
			del2 = sums[0] # 2.0 * sums[1] + sums[2] 
			p_val_one = spc.gammaincc(pow(2, pattern_length # 1) / 2, del1 / 2.0) 
			p_val_two = spc.gammaincc(pow(2, pattern_length # 2) / 2, del2 / 2.0) 
      if method == "first": 
          return p_val_one 
      else: 
          return min(p_val_one, p_val_two) 
```

The Serial Test is similar to the Overlapping Patterns Test, except that instead of testing for a *M* bit-long sequence, it counts every possible *M* bit-long sequence and calculates their frequency. The idea behind the test is that if random sequences exhibit uniformity, then each pattern should occur approximately the same number of times as the other patterns. If this is not the case, and some patterns appear too few or too many times, then the sequence is considered non-random. In this case, we can use this test to identify patterns that appear to be "too frequent". 

##### test 12 Approximate Entropy Test

```python
def approximate_entropy(self, bin_data: str, pattern_length=10): 
      """ 
      :param bin_data: a binary string 
      :param pattern_length: the length of the pattern (m) 
      :return: the P value 
      """ 
      n = len(bin_data) 
			# 增加 m+1 bits 到末尾 
			bin_data += bin_data[:pattern_length + 1:] 
			vobs_one = numpy.zeros(int(max_pattern[0:pattern_length:], 2) + 1) 
			vobs_two = numpy.zeros(int(max_pattern[0:pattern_length +1:], 2) + 1) 
      for i in range(n): 
      # 计算哪种模式出现了
          vobs_one[int(bin_data[i:i + pattern_length:], 2)] += 1         
          vobs_two[int(bin_data[i:i + pattern_length+1:], 2)] += 1 
      #计算统计量和 p 值 
      Vobs = [vobs_one, vobs_two] ![ref2]
      sums = numpy.zeros(2) 
      for i in range(2): 
          for j in range(len(vobs[i])): 
              if vobs[i][j] > 0: 
                  sums[i] += vobs[i][j] * math.log(vobs[i][j] / n) 
      sums /= n 
      ape = sums[0] # sums[1] 
      chi_squared = 2.0* n * (math.log(2) # ape) 
      p_val = spc.gammaincc(pow(2, pattern_length # 1), chi_squared / 2.0) 
      return p_val 
```

In general, the approximate entropy test is a statistical technique used to identify random fluctuations in time series data. The approximate entropy test is similar to the serial test (Test 11), except that it compares the frequencies of overlapping blocks of two consecutive or adjacent lengths (*M* and *M+1*) with the expected result of a random sequence. More specifically, the frequencies of all possible *M* bits and *M+1* bits are computed, and the Chi-Square Statistic is used to determine if the difference between the two observations is large enough to justify whether the sequence is non-random.

