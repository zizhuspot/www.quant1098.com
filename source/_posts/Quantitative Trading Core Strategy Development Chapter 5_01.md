---
title: Quantitative Trading Core Strategy Development Chapter 5 Part I
date: 2023-11-04 11:22:00
categories:
  - Quantitative Trading
tags:
  - Development 
  - Strategy
  - Quantitative tool
  - Core
  - yalmip
  - quant1098.com
  - successes
  - DE
description:  This chapter introduces common convex and non-convex optimization problems in quantitative research and the corresponding solutions. For convex optimization problems, we use the `yalmip` framework; for non-convex optimization problems, we use the DE algorithm. At the application level, this chapter also introduces how to construct mean-reverting portfolios and detect insider trading in A-shares using these tools.
cover: https://s2.loli.net/2023/11/02/NTmzbJ9LgCBtQFs.webp
---
2. Chapter 5: Introduction to Quantitative Tools

   
   This chapter introduces common convex and non-convex optimization problems in quantitative research and the corresponding solutions. For convex optimization problems, we use the `yalmip' framework; for non-convex optimization problems, we use the DE algorithm. At the application level, this chapter also introduces how to construct mean-reverting portfolios and detect insider trading in A-shares using these tools.
   
   ### 5.1 Solving `Conve` Convex Optimization Problems with `yalmip`
   
   Convex optimization problems are problems in which both the objective function and the constraint function are convex functions. This section describes the origin of yalmip, its basic syntax, and how to use it to solve convex optimization problems such as portfolio optimization and mean-reverting portfolio construction.
   
   #### 5.1.1 `yalmip` Introduction
   
   1. What is `yalmip'?
   
      `yalmip` is a free optimization solver toolkit developed by `Lofberg`. Its most important feature is that it integrates a number of external optimization solvers to form a unified modeling and solving language, and it provides MATLAB calling APIs to reduce the learning cost of learners.
   
      
   
   2. `yalmip` installation method 
   
      Take MATLAB installation as an example, download the latest toolkit from the official website, unzip it to the toolbox folder of MATLAB (of course, it can also be stored in other folders), and then open the MATLAB software to add the Path path. Finally, type `yalmip test` and run the test to get the following result.
   
2. 

   ```matlab
   +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
   | 					Test|	Solution|     			Solver message|
   +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
   |    Core funtionalities|       N/A|    Successfully solved (YALMIP)|
   |                     LP|   Correct|   Successfully solved (LINPROG)|
   |                     LP|   Correct|   Successfully solved (LINPROG)|
   |                     QP|   Correct|  Successfully solved (QUADPROG)|
   |                     QP|   Correct|  Successfully solved (QUADPROG)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   |                   SOCP|   Correct|Successfully solved (SeDuMi-1.3)|
   +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
   summarize
   ```
   
   Note that `yalmip` automatically selects the appropriate solver for the problem, and since I only have `SeDuMi` installed, I called `SeDuMi` to solve it.

#### 5.1.2 `yalmip`  syntax

1. ##### variables

   `yalmip` The most common function in use is `spdvar`, which is used to create decision variables with real rows. To create a matrix with 5 rows and 5 columns, we can use the following statement.

   ```matlab
   P= sdpvar(5，5):
   ```

   Note that when creating square matrices, `yalmip` creates symmetric matrices by default, so if you want the elements inside to be asymmetric, then you need to add an entry parameter:.

   ```matlab
   sdpvar(5,5，"full");
   ```

   Once the matrix has been created, the details can be viewed by entering the variable name in MATLAB's command window

   ```matlab
   Linear matrix variable 5x5 (full, real, 25 variables)
   ```

   The third parameter can be used to obtain a number of predefined types of matrices, such as Toeplitz (Toeplitz matrices abbreviated as T-matrices, i.e., where the elements on the main diagonal are equal, and those parallel to the main diagonal are equal), Hankel (Hankel matrices, i.e., square matrices where the elements on each subdiagonal are equal), diagonal, symmetric, and skew-symmetric matrices. It is also possible to generate these matrices first as a vector and then obtain them using standard MATLAB commands:.

   ```matlab
   x = sdpvar(n,1);
   D= diag(x); % Diagonal 矩阵
   H hankel(x); %  Hankel 矩阵
   T= toeplitz(x);% toeplitz矩阵
   ```

   In `yalmip`, scalars can be obtained in one of three ways

   ```matlab
   x = sdpvar(1,1); y = sdpvar(1,1);
   sdpvar x y
   ```

   The `sdpvar` object can be mixed with other variables in MATLAB, and most of the MATLAB built-in functions have been overloaded. For example, we can do the following.

   ```matlab
   P= sdpvar(3,3) + diag(sdpvar(3,1));
   X=[P P;P eye(length(P))] + 2*trace(P);
   X + sum(sum(P*rand(length(P)))) + P(end,end)+hankel(X(:,1));
   ```

   

2. ##### Binding settings
   To define constraint sets, we simply create them separately and connect them using an array. The meaning of a constraint depends on the context. If the left and right sides of the constraint expression are ergodic matrices, the constraint is interpreted in positive-definition, otherwise it is interpreted in a single-element-wise manner. An example of declaring a symmetric matrix and a positive definiteness constraint is as follows

   ```
   n = 3
   P = sdpvar(n, n);
   C = [P>=0];
   ```

   Or it is also possible to restrict only part of the matrix to be positive definite

   ```matlab
   P = sdpvar(n,n);
   C = [P(:)>=0];
   ```

   The above limitations can also be used with MATLAB's built-in functions.

   ```
   C = [triu(P) >=0];
   ```

   When there are multiple constraints, use an array to join them:

   ```matlab
   P = sdpvar(n,n);
   C = [P>=0，P(1,1)>=2];
   ```

   It is easy to check in MATLAB's command window that the intended constraint types have indeed been defined. Just as we are in the habit of looking at expressions when writing code, it is also worth looking at the list of constraints to see if they match the intent:.

   ```shell
   +++++++++++++++++++++++++++++++++++++
   |  ID|                  Constratint|
   +++++++++++++++++++++++++++++++++++++
   |  #1|        Matrix inequality 3x3|
   |  #2|  Element-wise inequality 1x1|
   +++++++++++++++++++++++++++++++++++++
   ```

   Of course, the expression involved can be any `sdpvar` object, and it is also possible to define equation constraints (--), just as with the <= constraints:.

   ```matlab
   C = [P>=0, P(1,1), sum(sum(P)) == 10]
   +++++++++++++++++++++++++++++++++++++
   |  ID|                  Constratint|
   +++++++++++++++++++++++++++++++++++++
   |  #1|        Matrix inequality 3x3|
   |  #2|  Element-wise inequality 1x1|
   |  #2|      Equality constraint 1x1|
   +++++++++++++++++++++++++++++++++++++
   ```

   It is even possible to write the constraints concatenated: the

   ```matlab
   F = [0 <= P(l,1) <= 2];
   ```

   Strict inequalities cannot be used (`yalmip` issues a warning), and they are meaningless when using numerical solvers for optimization, as the numerical errors of these solvers would cause them to fail to be strict. Therefore, if we need the upper bound to be strict, we must choose a tolerance threshold. Note that choosing this threshold is a tricky issue. If it is too small, it will serve no purpose, as it will drown in the numerical errors of the general numerical optimizer; if it is too large, it may reduce our solution space too much:.

   ```matlab
   my_tolerance_for_strict = 1e-5;
   F = [0 <= P(1,1) <= 2-my tolerance for strict];
   ```

   Constraints can also be added using the MATLAB for loop approach

   ```
   F=[0<= P(1,1) <= 2];
   for i = 2:n-1
       F = [F，P(i,1) <= P(2,i) - P(i,i)];
   end
   ```

#### 5.1.3 Application Example 1: Combinatorial Optimization

1. ##### Standard Markowitz mean-variance optimization

   The problem with a standard Markowitz mean-variance portfolio is how to choose portfolio weights that minimize the variance of the portfolio while allowing the portfolio to achieve a specified expected return. Let's start by choosing 10 stocks from the SSE 50 stock pool to test.

   ```matlab
   load data2
   data=log(data(:,1:10));
   [m,nj=size(data);
   ret=data(2:end,:)-data(l:end-1，:); % 计算对数收益率序列
   S = cov(ret); % 协方差矩阵
   mu = mean(ret，1) * 252; % 期望年化收益
   ```
   
   We introduce a vector w to model each asset position, restricting it to take values in the range [0,1], and with expected returns equal to the average historical returns of all assets:.
   
   ```matlab
   w = sdpvar(n,1);
   mutarget  mean(mu);
   F = [sum(w)==1, w >=0，mu*w = = mutarget];
   optimize(F,w'*s*w)
   x=value(w) 
   ```
   
   The following results are obtained for the mean-variance optimization problem.
   
   ```matlab
   ans=
       1至8列
           0.0819    0.0031  0.1231  0.0741   0.2514 0.1074  0.1436 0.0012
       9至10列
           0.1998    0.0144
   
   >> sum(x)
   ans = 1
   ```
   
   One approach to optimization is to limit the variance and maximize the expected return. Let's maximize the return while limiting the variance to less than the variance of a portfolio with equal positions in all assets (at this point the model becomes a quadratically constrained problem, and thus requires a solver with QCQP or SOCP capabilities such as `SeDuMi`, SDPT3GUROBI, MOSEK or CPLEX) .
   
   ```matlab
   F = [sum(w) == l，w>=0];
   F = F+[w'*S*w < sum(sum(S))/n~2];
   Optimize(F,-mu*w)
   x=value(w)
   ```
   
   The following runtime result is obtained.
   
   ```matlab
   >> X
   ans = 
       1至8列
         0.0067  0.0000  0.1763  0.1452  0.2584  0.1947  0.1539  0.0000
       9至10列
         0.0648  0.0000
   
   ```
   
   Another approach is to maximize the Sharpe ratio `wTu/(VwTSw)`, i.e., the excess return per unit of standard deviation. At first glance, this equation appears to be nonconvex. But in fact, it can be easily converted to a quadratic equation Our goal is to maximize `1/VwTSw`, i.e., minimize `wTSw`:.
   
   ```matlab
   w = sdpvar(n,1);
   F = [sum(w)== 1，w>=0];
   optimize(F,w'*S*w)
   X=value(w)
   ```
   
   Results for maximizing the Sharpe ratio are obtained.
   
   ```
   ans  = 
       1至8列
         0.1314  0.0199  0.0752  0.0083  0.2417  0.0274  0.1303  0.0009
       9至10列
         0.3025  0.0624
   ```
   
   

2. ##### Constrained portfolio optimization

​       In real life, we are often asked to allow only a limited number of investments. In `yalmip` this is easily done using the `nnz` operator. In the following example let's limit the number of investments to 4. Note that it is extremely difficult to solve a portfolio with such a limited number of investments: `yalmip`, `nnz` operator.

```matlab
mutarget = mean (mu);
F= [sum(w)== 1，1>=w>=0，mu*w == mutarget];
E=F +innz(w) <= 4];
optimize(F,w'*S*w)
value(w)
```

The following runtime result is obtained.

```matlab
ans =
    1至8列
       0    0    0    0.3158    0.2382    0.19940
    09至10列
       0.2467    0

```

In order to achieve a balanced portfolio, a constraint on the minimum and maximum positions may be useful. We can implement this constraint using the following model, where we aim to achieve a portfolio where no single asset accounts for more than 80% of the portfolio, but at the same time no asset has a position of less than 10%.

```matlab
w = semivar(n,1);
E = [0.l<= w<= 0.8];F = [F,sum(w)== 1,mu*w == mutarget];
optimize(F,w'*S*w)
x=value(w)
```

The following runtime result is obtained.

```matlab
ans =
    1至8列
       0.1005    0    0.1140    0.1001    0.2453    0.1002    0.1371
    9至10列
       0.2028    0
```

The example in the Portfolio Optimization textbook is the efficient frontier in a Markowitz portfolio. It refers to the position on the "return-variance" two-dimensional graph where the portfolio that minimizes variance at a given level of return is projected. In this scenario, the optimization task can be accomplished efficiently by using the optimizer command with a few minor changes to the settings. This command allows us to create an object that returns the return target as input and the variance and portfolio allocation as output. By using the optimizer command instead of explicitly setting up a new problem and calling the tuning function, program overhead can be greatly reduced.

```matlab
w = sdpvar(n,1);
sdpvar mutarget
F = [sum(w) == 1，w>=0,mu*w == mutarget];
Portfolio = optimizer(F,w'*S*w,sdpsettings('verbose',0),mutarget,[w'*S*w;w]);
```

The effective front (see Fig. 5-1) can now be easily drawn.![](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/frotier.png)

#### 5.1.4Application Example 2: Constructing Portfolios with Mean-Response Properties

We assume that the time series S1,2n of n assets conforms to the following first-order vector autoregressive process:![image-20231109144210856](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144210856.png)



where 4 is a square matrix of n*n and Z is Gaussian distributed white noise

Taking the value n=1, equation (5-1) can be changed to.![image-20231109144319486](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144319486.png)



i.e.![image-20231109144411134](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144411134.png)



Box & Tiao in their paper "A canonical analysis of multiple time series" suggest that the mean reversion of a smooth series can be measured by the following equation.![image-20231109144433935](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144433935.png)



Consider constructing a portfolio $P_t=x^TS_t$ with the above assets, where x is the weight of each asset. According to equation (5-1) we get.![image-20231109144456494](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144456494.png)



Then, the mean-reverting nature of this portfolio can be measured by.![image-20231109144521388](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144521388.png)



where G is the covariance matrix of the asset series, $G=E[SS^T]$ . That is, the problem of how to compose a portfolio with maximum mean reversion is equivalent to solving the following eigenvalue problem.![image-20231109144552731](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144552731.png)



Request:![image-20231109144615385](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144615385.png)



restrictive condition：![image-20231109144634925](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109144634925.png)



The Card constraint is the requirement that there are less than k nonzero items in the asset portfolio. We still use the SSE 50 stock dataset from the previous section (22 stocks remain after deducting stocks that have been listed for less than 10 years), and limit the non-zero weights of the portfolio to less than 5. The MATLAB code is as follows.

```matlab
count=0;
G2=G1;
G2=A*G2*A'+K;
[R,p] = chol(G2);
while(p>0)
count=count+1;
 G2=G2-delta*(G2-A*G2*A'-K) ;
 disp(count);   
[R,p] = chol(G2);    
end

beta=norm(G1-G2);

G=G2;
theta=ones(n,1)/n;
fval=-1e10;
fval_old=0;
count=0;
while abs(fval_old-fval)>=0.01&&count<=10000
    count=count+1;
     fval_old=fval;
     theta_old=theta;
     theta=theta./sum(abs(theta));
lbs=-1*ones(1,n);
ubs=ones(1,n);
 b=theta;
 problem = createOptimProblem('fmincon',...
                    'objective',@(x)mtheta(x,A,G,v),...
                     'lb',lbs,'ub',ubs,'x0',b,'options',...
                    optimoptions(@fmincon,'Algorithm','interior-point','Display','off'));
                % [x,fval] = fmincon(problem);
                gs = GlobalSearch('Display','off');
                [theta,fval] = run(gs,problem);
   disp(count);
   disp(fval);
end
theta=theta./sum(abs(theta));

y=s*theta;
y2=ss*theta;
figure(1)
plot(y)
figure(2)
plot(y2)
figure(3)
plot(sss*theta)


y3=(y2-mean(y))/std(y);
figure(4)
plot(y3)
figure(5)
plot((y-mean(y))/std(y))

```

The code runs with the following information:

![image-20231109124403208](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109124403208.png)

The weights of the asset portfolio are obtained as follows:

![image-20231109124449317](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109124449317.png)

The generated combination curves are shown in Fig. 5-2, and it can be seen that the combined curves have obvious mean-reverting properties.
![](https://raw.githubusercontent.com/zqwuming/blogimage/img/img/image-20231109124633793.png)

