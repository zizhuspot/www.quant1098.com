---
title: Quantitative Trading Core Strategy Development Chapter 3
date: 2023-11-03 22:22:00
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
description: "To do a good job, you have to have the right tools." In this chapter, we will introduce the programming languages that may be used in strategy development, including MATLAB, Python, R, Julia, etc. In this chapter, we will compare their features (including syntax, speed, richness of libraries, etc.). In this chapter, we will compare their features (syntax, speed, library richness, etc.), and finally, we will give an example of multi-language collaborative development.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---
## Chapter 3 Programming Language Selection

"To do a good job, you have to have the right tools." In this chapter, we will introduce the programming languages that may be used in strategy development, including MATLAB, Python, R, Julia, etc. In this chapter, we will compare their features (including syntax, speed, richness of libraries, etc.). In this chapter, we will compare their features (syntax, speed, library richness, etc.), and finally, we will give an example of multi-language collaborative development.

### 3.1 Introduction to Programming Languages

For quantitative research, choosing the right programming language for different problems can yield twice the results with half the effort. In this section, we will discuss the similarities and differences of various programming languages and the applicable working scenarios. The programming languages commonly used for strategy development include MATLAB, Python, R, Julia, and so on.

1. MATLAB 

![](https://s2.loli.net/2023/11/03/PiR167zgZ3IwlaB.png)

**MATLAB** (meaning Matrix Studio) is a commercial mathematical software with an integrated development environment (IDE) developed by `MathWorks`, and is also the name of the scripting language that comes with the software.

One of the strengths of **MATLAB** is the diverse and highly optimized "toolbox" (including very powerful image and other signal processing features), which makes it ideally suited for use in scientific research, engineering, and many areas of the sciences where efficient numerical computation is required. Like other scripting languages, it has cross-platform support and uses dynamic typing to provide a convenient program interface, but it is also very memory-intensive for computations on large data sets. MATLAB is still the most popular numerical language used for scientific computation in academia and engineering design and modeling in industry.




2. **Python** 

![](https://s2.loli.net/2023/11/03/UCpKPWXwRNoTm8Y.png)

If you're going to write strategies and trade in the same language, then Python is pretty much the only choice (C++ is fine, if you can stand that kind of development efficiency). With an easy-to-use syntax, a wealth of libraries and packages, comprehensive functionality, and free and open source, it's no wonder that Python is one of the most popular development languages out there today when it comes to all of these features. There are many open source trading frameworks that use Python, including `Quantopian` and VNPY. **For strategy development, the importance of using the same framework for live trading and backtesting is indisputable. **The disadvantage of Python, if you want to say it, is that it is too comprehensive, and inevitably not as deep as other languages. As a rival to MATLAB's computational functions, the `NumPy` library is very difficult to use, for example, the multiplication of matrices is natively supported by MATLAB, but in `NumPy`, you must be converted to Matrix type first, and then call the dot function, which is very inconvenient. These inconveniences are hard to tolerate for people who are used to the convenience of MATLAB. Also, Python has always had problems with precision in numerical calculations, especially in calculations where precision is important.

3. **R** 

![](https://s2.loli.net/2023/11/03/sbyHrLfwhEFxRiV.png)

The **R** programming language was developed in 1993 as a modern GNU implementation of the old statistical programming language called S, which was developed at Bell Labs in 1976. Since its release, it has had a fast-growing user base, and is especially popular with statisticians. 

R may not be as well known as the two previous programming languages, but it is indispensable to users of statistical tests. It has long been a tradition in the statistical community to publish a new study and then share the algorithms used in the study as an R package. This has resulted in many cutting-edge statistical results that are only available in R. At the same time, R has a very user-friendly IDE, RStudio, and it is not easy to realize a user experience similar to that of commercial software (e.g., **MATLAB**) as free software.

4. **Julia** 

![](https://s2.loli.net/2023/11/03/DcJYIuzjZ5lWLdH.png)

First released in 2012, Julia is the youngest programming language mentioned in this chapter. Although Julia can also be used as a dynamically typed interpreted language on the command line, it is aimed at high performance in scientific computing. It is particularly noteworthy that its Convex Optimization Library provides free support for some commercial libraries and is more powerful than MATLAB's `Yalmip'. The only drawback is that due to the relatively recent development of the library, third-party libraries are few and far between, and the features of the language have not yet been stabilized.

### 3.2 Financial Computing 

R and MATLAB, being well established languages, have a wealth of libraries to call upon in this area. In my financial research, I have never encountered any serious language limitations.

As for Python, we can use `NumPy` for most of the work, but there are often some minor problems. Some of the available library codes, such as GARCH estimation, have convergence problems, and there is no multidimensional GARCH estimation. 

As for Julia, it is even harder to find an existing library. Even when it exists, it is often not clear how to use it. For example, `StatsFuns.jl` and `Distributions.jl` both perform statistical computations, but the former does not support vectorization and is poorly documented. There is only one package that supports univariate GARCH(1, 1), not general GARCH (p, q) or Student-t distributions. Not to mention multidimensional GARCH. 

Thus, R and MATLAB are the winners when it comes to implementing financial computations, with Python lagging behind and Julia far behind. 

### 3.3 Language Features 

R and MATLAB originated in the 1970s. Since then, their evolution has been erratic. Some R functions are inconsistent and exhibit problematic behavior, as noted in The R Inferno, and MATLAB has its share of undesirable features, such as using the same type of parentheses () for matrix accesses as for function calls, making the code difficult to read. The other three languages use [] and (), which avoids this problem and minimizes errors.MATLAB functions must be located at the end of the source file or in a separate file. They are neither type-safe nor have proper namespaces, and their packages often override function names, leading to hard-to-diagnose errors. r supports limited object-oriented programming, while MATLAB's object-oriented operations were improved with the 2015b update.

Python is 20 years younger than R and MATLAB, and it is very well designed (e.g., file handling).

For numerical programming, Python uses two additional libraries - Pandas for data structures and `NumPy` for computation. While both are powerful, neither seems to be a good fit for Python, and while ideally the appearance and behavior of data structures should be the same between libraries, it's often necessary to convert Pandas and `NumPy` data structures when switching from one package to another.

Julia is the newest competitor, and it incorporates the most advanced language design concepts. Unlike the other three, Julia can optionally use type declarations, and multithreaded computation is more natural than in other languages. Built-in object-orientation and multi-assignment are at the core of its language design. It also allows the use of Unicode characters, so you can use Greek and other characters for variable names. 

So, in terms of language features, Julia is the clear winner, with Python trailing slightly, and R and MATLAB far behind. 

### 3.4 Speed 

R, MATLAB and Python are interpreted languages, which by nature use more processing time. Although they all now offer just-in-time (JIT) compilation, this may not always be useful, and is particularly slow when iterating through loops; the thinking behind MATLAB is that it doesn't matter because it was designed for linear algebra, and only as a front-end to numerical libraries for FORTRAN or C. The same reasoning applies to R. The language is not a JIT language, but a JIT language. The same reasoning applies to the R language. 

Both R and MATLAB can use a variety of techniques to speed up calculations, applying libraries from C or FORTRAN to common calculations. One can also write C/C++/FORTAN code in these languages. However, from an implementation point of view, all these tricks make the language more complex. The same applies to Python, which often significantly improves performance by running parts of its code in C. We can also use `Numb' to improve performance. We can also use `Numba`, a JIT compiler that involves minimal extra code. However, it can only be used in certain simple cases, and does not support class definitions and throwing exceptions.

As for Julia, based on on-the-fly compilation, the language developers promise to be as fast as FORTRAN or C, while at the same time not requiring the user to use additional tricks to speed up the code, thus making the language simpler and easier to program. Figure 3-1 compares the speed of Julia with several languages on the official Julia website, noting that smaller values are better.。 

![image-20231103212956074](https://s2.loli.net/2023/11/03/BwmJFCn91zt2M57.png)

### 3.5 Data Handling 

Many studies involve large data sets, often with a variety of different data types such as integers, strings, real numbers, dates, logics, or lists. Processing such data may require filtering and conversion operations.

Data is often read from and written to a variety of formats including text files, CSV files, Excel files, SQL databases, `noSQL` databases and local/remote proprietary data formats.

This is where R absolutely shines, designed specifically for scientific data. It can handle complex data structures in a wide variety of formats and sources, and many open source packages provide a variety of ways to access and manipulate data. It can also handle datasets much larger than those that fit into memory.

Python is also very good at this, and its `Pandas' and `NumPy' libraries do a lot of things that R can't do. But it doesn't look like R. `NumPy` arrays lack column names, which makes data retrieval less convenient. With Pandas, accessing and changing elements requires special syntax, such as `.iloc /.loc`, and often requires type conversions from Pandas datasets to `NumPy` arrays. For example, to access elements in M of type `DataFrame`, you may have to use syntax such as `M.iloc[1,:].values[1]`. In R, the same operation is much simpler: M[2,2]. 

MATLAB's support for different data types has been improved in recent updates, and it has import capabilities for most common files.

Julia currently lacks support for handling multiple file types. In addition, some of the base libraries are still being rewritten, such as the CSV and `DataFrames` libraries for importing CSV files. 

In terms of data handling, Julia is the worst, followed by MATLAB and Python, with R being the winner.

### 3.6 Library support 

Each of the four languages provides a basic structure, but many specialized features require external library support.

MATLAB is designed to be a numerical computation-specific language and has many useful functions built in. In addition, it has a rich set of libraries available, especially for many engineering applications (e.g. signal processing).

R offers more libraries: for any desired statistical function, there is always a library that will support it. On the downside, some of the libraries are of low quality or poorly documented, and there may be multiple libraries that implement the same function over and over again. 

Python has a lot of libraries available, but not as many as R or MATLAB. 

Julia, as a newcomer, has by far the fewest open source libraries.

Therefore, in terms of library diversity, Julia is the worst, followed by Python and MATLAB, and R is the best.

### 3.7 Ease of use 

When it comes to ease of use, MATLAB has a good Integrated Development Environment (IDE) with very good documentation support. r has come a long way, and `RStudio' is even more user-friendly than MATLAB's IDE. shiny allows interactive web applications to be built directly from r, providing an online-friendly representation of the data. r has good plotting capabilities, and MATLAB is not far behind. R has good plotting capabilities, and MATLAB is not far behind: the Anaconda release of Python comes bundled with the friendly Spyder IDE, and Python's plotting is done primarily with `Matplotlib`, which has a MATLAB-like interface; Juno for Julia is an IDE that integrates the Atom editor, and has a similar appearance and functionality to Spyder, `Plots', and `Plots'. Spyder, `Plots.jl` is used for plotting, but usually depends on packages from other languages.

Commonly used libraries in Julia still change from time to time, which leads to incomplete or missing documentation. In many cases, when converting code from R / MATLAB to Julia, we have to check the source code to find the required settings.

Therefore, in terms of ease of use, especially for novice users, MATLAB is the best, R and Python are a little behind, and Julia still has a way to go.

None of the four languages meets all the evaluation criteria. r and MATLAB benefit from their age, and one can easily search for anything one wants. R and MATLAB benefit from their age in that it is easy to search for anything you want, but their syntax is outdated, with considerable historical baggage and operational inefficiencies, and Python is more modern, but its libraries are insufficient in comparison, and its numerical programming is clunky. What about Julia? It is a modern language, very elegant and fast. What it currently lacks is comprehensive library support for data manipulation and numerical computation, and Julia has been undergoing significant development, with the recent release of version 1.0 bringing structural stability to the language and making it safer to use Julia for long-term projects.

Of course, there is no need to stick to one language, but rather to use a combination of languages to get the most out of your work.

### 3.8 Example of collaborative multilingual development with applications

Consider the following formula for asset allocation:
$$
X = [(∑)^{-1} \times μ]^{T} \times (∑)^{-1}
$$
Style:

- ∑ is the Cholesky decomposition of the asset covariance matrix; 

- $$
  μ_i = R_i - r_f  + \frac{1}{2} \times δ_i^{2}
  $$

  

2

When there are only two asset classes, this formula is not difficult to calculate:

$$
\left[
    \begin{matrix} 
    x_1 \\
    x_2
    \end{matrix}
    \right] = \frac{1}{1 - ρ^{2}}
    \left[ 
    \begin{matrix} 
    (λ_1  + 1/2σ_1 - ρ  \times λ_2 - 1/2 \times ρ \times σ_2)/σ_1\\
    (λ_2  + 1/2σ_2 - ρ  \times λ_1 - 1/2 \times ρ \times σ_1)/σ_2
    \end{matrix}
    \right]
$$
However, in the case of four asset classes, this formula is very difficult to derive. Can we only expect MATLAB's numerical calculations to be accurate enough? Let's use Mathematica to derive the analytic solution.

First, define the covariance matrix function:

```mathematica
covariancematrix[n\_] :=  
    Table[sigma[i] sigma[j] rho[i, j]^(1 - KroneckerDelta[i, j]), {i, 1, n, 1}, {j, 1, n, 1}] /. {rho[i\_, j\_] :> rho[j, i] /; i>j} 
```

Assigns the function and computes its Cholesky decomposition and inversion matrices:

```mathematica
m = covariancematrix[4] 
mm = Transpose[CholeskyDecomposition[m]] HH = Inverse[mm] 
```

Calculate the results according to Equation 3-1:

```
u = {{lamda1 sigma[1] + sigma[1]^2/2}, {lamda2 sigma[2] + sigma[2]^2/ 2}, {lamda3 sigma[3] + sigma[3]^2/2}, {lamda4 sigma[4] +  sigma[4]^2/2}} 
S = Dot[Transpose[Dot[HH, u]], HH] 
```

Calls the `ToMatlab` library and converts the generated `Mathmatica` code to MATLAB code:

```mathematica
Import["ToMatlab.m", "Package"] 
Transpose[S] // ToMatlab 
```



The results are as follows:

```matlab
sigma(1).^(-2).\*(lamda1.\*sigma(1)+(1/2).\*sigma(1).^2)+(-1).\*rho( ... 

    1,2).\*sigma(1).^(-1).\*sigma(2).\*((-1).\*Conjugate(rho(1,2).\*sigma( ...   1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,2).\*sigma(1).\*(sigma( ...   1).^2).^(-1/2).\*sigma(2)+sigma(2).^2).^(-1/2).\*((-1).\*rho(1,2).\* ...   sigma(1).^(-1).\*(lamda1.\*sigma(1)+(1/2).\*sigma(1).^2).\*sigma(2).\*( ...   (-1).\*Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma( ...   2)).\*rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2) ...   .^2).^(-1/2)+(lamda2.\*sigma(2)+(1/2).\*sigma(2).^2).\*((-1).\* ... 

`  `Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\* ...   rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2).^2) ... 

.^(-1/2))+(sigma(1).^2).^(-1/2).\*((-1).\*Conjugate(rho(1,2).\*sigma( ...   1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,2).\*sigma(1).\*(sigma( ...   1).^2).^(-1/2).\*sigma(2)+sigma(2).^2).^(-1/2).\*((-1).\*rho(1,3).\* ... 

`  `sigma(1).\*(sigma(1).^2).^(-1/2).\*((-1).\*Conjugate(rho(1,2).\*sigma( ... 

量化交易核心策略开发：从建模到实战![ref3]

`  `1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,2).\*sigma(1).\*(sigma( ...   1).^2).^(-1/2).\*sigma(2)+sigma(2).^2).^(1/2).\*sigma(3)+rho(1,2).\* ...   sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2).\*((-1).\*Conjugate(rho(1, ...   2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,2).\*sigma(1) ...   .\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2).^2).^(-1/2).\*((-1).\* ... 

`  `Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\* ...   rho(1,3).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(3)+rho(2,3).\* ... 

`  `sigma(2).\*sigma(3))).\*((-1).\*Conjugate(rho(1,3).\*sigma(1).\*(sigma( ...   1).^2).^(-1/2).\*sigma(3)).\*rho(1,3).\*sigma(1).\*(sigma(1).^2).^( ... 

`  `-1/2).\*sigma(3)+sigma(3).^2+(-1).\*Conjugate(((-1).\*Conjugate(rho( ...   1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,2).\*sigma( ... ![ref15]  1).\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2).^2).^(-1/2)).\*( ... 

`  `Conjugate(rho(2,3).\*sigma(2).\*sigma(3))+(-1).\*Conjugate(rho(1,3).\* ...   sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(3)).\*rho(1,2).\*sigma(1).\*( ...   sigma(1).^2).^(-1/2).\*sigma(2)).\*((-1).\*Conjugate(rho(1,2).\*sigma( ...   1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,2).\*sigma(1).\*(sigma( ...   1).^2).^(-1/2).\*sigma(2)+sigma(2).^2).^(-1/2).\*((-1).\*Conjugate( ... ![ref2]  rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*rho(1,3).\* ... 

`  `sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(3)+rho(2,3).\*sigma(2).\* ... 

`  `sigma(3))).^(-1/2).\*((-1).\*(lamda2.\*sigma(2)+(1/2).\*sigma(2).^2).\* ...   ((-1).\*Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma( ...   2)).\*rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2) ...   .^2).^(-1).\*((-1).\*Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^( ...   -1/2).\*sigma(2)).\*rho(1,3).\*sigma(1).\*(sigma(1).^2).^(-1/2).\* ... 

`  `sigma(3)+rho(2,3).\*sigma(2).\*sigma(3)).\*((-1).\*Conjugate(rho(1,3) ...   .\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(3)).\*rho(1,3).\*sigma(1).\* ...   (sigma(1).^2).^(-1/2).\*sigma(3)+sigma(3).^2+(-1).\*Conjugate(((-1) ...   .\*Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\* ...   rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2).^2) ... ![ref12]  .^(-1/2)).\*(Conjugate(rho(2,3).\*sigma(2).\*sigma(3))+(-1).\* ... 

`  `Conjugate(rho(1,3).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(3)).\* ... ![ref13]  rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\*((-1).\* ... 

`  `Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)).\* ...   rho(1,2).\*sigma(1).\*(sigma(1).^2).^(-1/2).\*sigma(2)+sigma(2).^2) ...   .^(-1/2).\*((-1).\*Conjugate(rho(1,2).\*sigma(1).\*(sigma(1).^2).^( ... 

`  `-1/2).\*sigma(2)).\*rho(1,3).\*sigma(1).\*(sigma(1).^2).^(-1/2).\* ... 
```

............................. (2000+ lines omitted here) 

Substituting the resulting analytical solution into the computation allows you to compare the accuracy with the MATLAB numerical solution.

### 3.9 Summary of the chapter 

This chapter introduced the advantages and disadvantages of MATLAB, Python, R, and Julia, as well as examples of collaborative development. By the end of this chapter, the reader should have a good understanding of the characteristics of these scripting languages and be able to choose the right one for different tasks.