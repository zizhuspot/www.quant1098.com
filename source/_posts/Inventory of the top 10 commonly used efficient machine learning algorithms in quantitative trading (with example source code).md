---
title: Inventory of the top 10 commonly used efficient machine learning algorithms in quantitative trading
date: 2023-10-15 16:07:00
categories:
  - Quantitative Trading
tags:
  - machine learning
  - algorithms
  - Quantitative tool
  - quantitative trading
  - commonly
  - quant1098.com
  - Inventory
  - AIOQuant
description: In the field of financial investment and quantitative trading, machine learning algorithms are becoming more and more popular and prevalent, because we humans can generally find linear relationships between cause and effect through observation, while non-linear relationships are not strong enough, so we have to rely on these machine learning algorithms to help us make smarter investment and trading decisions.
cover: https://s2.loli.net/2023/10/15/hqtSFLIyuJjHnfi.webp
---

In the field of financial investment and quantitative trading, machine learning algorithms are becoming more and more popular and prevalent, because we humans can generally find linear relationships between cause and effect through observation, while non-linear relationships are not strong enough, so we have to rely on these machine learning algorithms to help us make smarter investment and trading decisions.

**In this article, we chatter in the financial investment and quantitative trading, commonly used and the effect is not bad 10 major machine learning algorithms, will probably talk about their basic working principles, usage and code cases **, mainly for quantitative newcomers to play the role of "algorithms list", the article is limited in length, it is difficult to cover everything! The main purpose of the article is to provide a "list of algorithms" for the new quantitative students, so it is difficult to cover everything.

## **I, linear regression (Linear regression)**

Linear regression should be said to be one of the most commonly used statistical models, high school contact with the least squares OLS belongs to linear regression, you see, this is also considered to be early contact with the case of machine learning.

**Linear regression predicts the value of a dependent variable based on one or more independent variables, and is called "linear" because it assumes that the relationship between the dependent variable and the independent variables is linear**, in the form of y=a0+a1*x1+a2*x2+... +an*xn, where x is the independent variable, y is the dependent variable, a is the regression coefficient, and a0 is also known as the intercept.![](https://s2.loli.net/2023/10/30/uopitZr1eKhVELc.jpg)

In the field of financial investment and quantitative trading, linear regression is often used to model and forecast financial time series/cross-sectional data, such as security prices, factor returns, exchange rates, and interest rates, to name a few. It can be used to identify relationships between different variables/factors and make predictions about future values based on these relationships.

The main advantage of linear regression is its simplicity and interpretability. Believe it or not, our common models such as Capital Asset Pricing Model (CAPM), Arbitrage Pricing Model (APT), and Fama-French three-factor model, etc. are all expressed in this form.

In addition to their wide range of applications, linear regression models are very easy to implement, are relatively fast to train even on large datasets, and can handle missing data and categorical variables through the use of dummy variables.

It is now very easy to use these machine learning algorithms, which are packaged libraries (e.g., scikit-learn) that can be called directly, so here's a look at the example source code for linear regression.

```python
import numpy as np
from sklearn.linear_model import LinearRegression

# 构建数据样本
X = np.array([[1, 2], [3, 4], [5, 6], [7, 8]])
y = np.array([1, 2, 3, 4])
# 创建线性回归模型
model = LinearRegression()
# 用数据样本训练模型
model.fit(X, y)
# 用训练好的模型去预测样本
predictions = model.predict(X)
# 打印输出
print('预测：', predictions)print('系数：', model.coef_)
print('截距：', model.intercept_)
```

result:

```python
预测：[1. 2. 3. 4.]
系数：[0.25 0.25]
截距：0.24999999999999956
```

This code fits a linear regression model to the data in the X array, the y array as the target variable (dependent variable), you can see that X has 4 sample points, each sample has 2 features, it will automatically learn 2 regression coefficients and 1 intercept, the final training model for y = 0.25 + 0.25x1 + 0.25x2.

Special note here is that, although the above code is simple, but ** now the use of machine learning libraries are basically the same process: first from the library (sklearn) in the import of the required model (LinearRegression), and then import the data for training (fit), and then finally you can use the trained model on the new data for prediction (predict), you can use the trained model to predict the new data (predict). If you feel the need, you can check the key parameters of the model (coef_ and intercept_ ). ** The modeling process for all the algorithmic models below follows this three-pronged process and will not be repeated.

However, in general, the data set used to train the model is called the training set, to the trained model prediction data set is called the test set, the two data sets are generally not overlapping, no intersection of data, we are lazy here, please note the distinction.

## **Two, logistic regression (Logistic regression) **

Do not see this algorithm inside the word "regression" (regression), thought it is used to complete the regression task as linear regression, ** in fact, it is mainly used to complete the classification task. It is usually used to predict the probability of a binary outcome based on one or more independent variables. **

Its model structure is y=1/(1+exp(a0+a1*x1+a2*x2+... +an*xn)), that is, on the basis of linear regression, set a layer of Sigmoid function y=1/(1+exp(x)), no matter what the range of values among exp(*), it can limit y to between 0 and 1, and complete the binary classification task well.

In the field of financial investment and quantitative trading, logistic regression is often used to categorize tasks, such as whether the price of securities is rising or falling, whether there is fraud in the financial statements, or whether the net profit margin of the enterprise is exceeding the increase.

![](https://s2.loli.net/2023/10/30/UJnyZMI1DRLtiHE.png)

The main advantage of logistic regression, like linear regression, is that it is relatively simple to implement and interpret, and is very fast to train even on large data sets. In addition, logistic regression is "probabilistic" in nature, which means that it can output predictions in the form of probabilities rather than just binary predictions, which is very useful in quantitative trading to measure the level of uncertainty or risk involved.

Let's take a look at an example of logistic regression, as shown below. The whole process is the same as a linear regression model, except that for the classification task, the label value of y is changed to 0 or 1 to indicate the category.

```python
import numpy as np
from sklearn.linear_model import LogisticRegression
X = np.array([[1, 2], [3, 4], [5, 6], [7, 8]])
y = np.array([0, 1, 0, 1])
model = LogisticRegression()
model.fit(X, y)
predictions = model.predict(X)
print(predictions)
```

## **Third, decision trees (Decision trees) **

** decision tree is called "decision" tree, as the name suggests, can be based on the characteristics of the data set for prediction, it is through the data to build a tree-like decision-making model to work, each branch represents a different decision or result. **

![](https://s2.loli.net/2023/10/30/ni8rOAscgzS5wlH.png)

Decision trees are commonly used for classification tasks in the field of financial investment and quantitative trading. The main advantage of decision trees, besides their relative simplicity and strong interpretation, is that crucially they are able to handle complex data sets and recognize non-linear relationships between features and target variables. However, if the decision tree is not properly pruned, it may suffer from overfitting, which reduces its generalization ability.

The use case for decision trees is shown below, the process is the same as the two algorithms above, just note that it is a classification task.

```python
import numpy as np
from sklearn.tree import DecisionTreeClassifier
X = [[0, 0], [1, 1]]Y = [0, 1]

clf = DecisionTreeClassifier()

clf = clf.fit(X, Y)

print(clf.predict([[2., 2.]]))
```

By the way, you can also specify the type of criterion to be used for splitting the tree, e.g. "gini" or "entropy", and set other parameters such as the maximum depth of the tree or the minimum number of samples required for leaf nodes, as fully described in scikit-learn documentation A full description can be found in the scikit-learn documentation.

## **Fourth, Random forests (Random forests)**

Random forests are an extension of the decision trees just described above, based on the principle of integrated learning, used to make more powerful and reliable predictions. **It creates a set of decision trees and uses the average of the predictions made by each tree to make the final prediction, similar to the reality of a group of us voting, and then the minority follows the majority. **

![](https://s2.loli.net/2023/10/30/bpVWvZPY3ktCdNz.png)

Random forests are also generally used for classification tasks, generally have relatively high accuracy, and tend to have better generalization capabilities than individual decision trees. However, the interpretability of random forests is relatively poor compared to single decision trees because the prediction is based on the average of many trees rather than a single tree.

In use, after importing the RandomForestClassifier model from sklearn, you need to set the number of trees in the forest (n_estimators ), the maximum depth of the tree (max_depth) and random seeds (random_state), and the rest of the part, it is the same as the traditional three-pronged process.

```python
import numpy as np
from sklearn.ensemble import RandomForestClassifier

X_train, y_train, X_test, y_test = load_data()

model = RandomForestClassifier(n_estimators=100, max_depth=2, random_state=0)

model.fit(X_train, y_train)

predictions = model.predict(X_test)

print(predictions)
```

** It should be noted that load_data() is an abstract function, you need to implement it according to your own application, the main purpose is to be used to load the training set data (X_train) and labels (y_train), the test set data (X_test) and labels (y_test)**, the organization is the same as the three algorithms introduced above, and the three algorithms. If you don't currently have the exact modeling task yourself, you can follow my two previous articles ["Hands-On, Using Machine Learning Models, to Build Quantitative Timing Strategies"]  and ["Investment manager 3 weeks shall develop 4000 quantitative factors, hand teach you 4 lines of core code to deal with easily"]dad40d6cdb8482fdf1e6f94690f2494e&chksm=c21ba5e2f56c2cf48767632408133f74e3e23dbd0aac285e1f169d2d7f912516d178b04da22d&scene=21# wechat_redirect) in the Data Preparation section to construct the factorial data and tag the corresponding labels.

If also do not want to organize their own data, want to use right out of the box, then you can directly use sklearn machine learning library datasets module, which comes with a variety of machine learning tasks need to use the dataset, first of all, you can use make_regression or make_classification function to customize the generation of their own regression/classification dataset, and secondly you can also use make_regression function to generate the regression/classification dataset needed. First, you can use the make_regression or make_classification function to customize the regression/classification dataset you need, and second, you can also use ready-made datasets, such as the diabetes dataset for the regression task (load_diabetes) and the iris dataset for the classification task (load_iris).

Let's take the random forest classification task as an example, using the iris dataset (load_iris), and let's look at the specific data form.

```python
import numpy as np
import pandas as pd
from sklearn import datasets
iris = datasets.load_iris()  

data = iris['data'] 

label = iris['target'] 

feature = iris['feature_names'] 

df = pd.DataFrame(np.column_stack((data,label)), columns=np.append(feature,'label'))df.iloc[[0,1,60,61,120,121],:] 

```

![](https://s2.loli.net/2023/10/30/KzAHNkgBLbOWFhc.png)

iris contains data on 150 Iris flowers, where each sample has 4 features, namely sepal length, sepal width, petal length and petal width, as well as the corresponding category labels (labels 0, 1, and 2 correspond to the flower species Iris Setosa, Iris Versicolour, and Iris Virginica, respectively).

So, to use this dataset to train and test the model in a random forest, you only need to change this code in the example application

```python
X_train, y_train, X_test, y_test = load_data()
```

modify to

```python
从 sklearn 导入数据集
从 sklearn.model_selection 
导入 train_test_split

iris = datasets.load_iris()
X_train, X_test, y_train, y_test = train_test_split(iris.data, iris.target, test_size=0.2)
```

In order to facilitate the elaboration and demonstration, the following still uses the abstract function load_data() to represent the loading of the training set and test set data, and will not be repeated.

## V. K-Nearest Neighbors (KNN)

K Nearest Neighbors (K-Nearest Neighbors, KNN) is a machine learning algorithm commonly used for classification and regression tasks,** which presumably works by finding the K closest data points to a given data point and using the categories or values of those data points to make predictions. **I believe most people have heard the expression, "your income is the average of the income of 5 friends around you", KNN implements this "people are divided into groups, things are clustered" concept.

![](https://s2.loli.net/2023/10/30/dhYt2vEWuToLx3A.png)

In the field of financial investment and quantitative trading, KNN is usually used mostly for classification tasks, but can also be used for regression.One of the main advantages of KNN is that it is relatively simple to understand and easy to implement, and is less sensitive to missing values.** The biggest disadvantage is that it is more sensitive to the choice of K-values. **In addition to this, it may perform relatively crotch in cases where the number of features/factors is too large relative to the number of samples, and KNN prediction on large datasets may also require a relatively large amount of computation.

```python
import numpy as np
from sklearn.neighbors 
import KNeighborsClassifier

X_train, y_train, X_test, y_test = load_data()

model = KNeighborsClassifier(n_neighbors=5)

model.fit(X_train, y_train)

predictions = model.predict(X_test)

print(predictions)
```

## ** VI. K-means clustering algorithm (K-Means)**

Said KNN, on the way to say a few words about its relatives K-Means, from the name looks very similar, the underlying principle is also very similar, but dry is not the same job.

**K-Means is generally used for clustering (clustering) task, and KNN is generally used for classification (classification) task, although it is a word difference, but the use is not the same. ** We can first post the example code, you will find that it has a significant difference with other algorithm code.

```python
import numpy as np
from sklearn.cluster 
import KMeans

X = np.array([[1, 2], [1, 4], [1, 0], [4, 2], [4, 4], [4, 0]])

model = KMeans(n_clusters=2, random_state=0, n_init='auto')

model.fit(X)

predictions = model.predict([[0, 0], [4, 4]])

print(predictions)
```

See wood, ** other algorithms in general training model need to be input at the same time features/factors data X and labeling data y, and K-Means only need to input features/factors data X, the former training method is known as supervised learning (Supervised learning), the latter is known as unsupervised learning (Unsupervised learning) )**, the former is equivalent to let you self-study when both sent to you test questions, but also sent to you the reference answer, the latter is only sent to test questions, wooden answer.

What is the use of that? It is also to play a "group of people, things to cluster" the role of the division, so K-Means called clustering algorithm, for example, now there are more than 5,000 stocks in the A-share, Shenwan divided them into 31 industries, you feel that the division is not appropriate, you can set different attributes/factors and want to divide them into how many industries/concepts/styles/sectors (for example, the number of stocks in the A-share market). style/sector (for example, the number of clusters in the above example is 2), the K-Means algorithm can be the more than 5,000 stocks according to their own requirements to be divided out, in each cluster, the stocks inside you set the attributes/factors are certainly extremely similar to belong to your own industry/concept/style/sector. **Often times, you will marvel at how stocks that are not even close to each other can be so similar in these ways. **

![](https://s2.loli.net/2023/10/30/GqPsufLrozFtVid.png)

## VII. Support Vector Machines (SVMs)

**Support Vector Machines (Support Vector Machines, SVM) was originally designed to solve binary classification problems, and later extended to multi-classification problems **, such as the development of the broad market timing strategy "predict the rise and fall of the CSI 300 index" belongs to the typical binary classification problem, "up" is one category, "down" is another category, "up" is another category. For example, the previously developed broad market timing strategy "predicting the rise and fall of CSI 300" is a typical binary classification problem, where "rise" is one category and "fall" is another.

It linearly separates the two categories of samples by finding a maximum interval hyperplane, and ensuring that the distance from the nearest edge point of the two samples to this plane is the maximum, since the maximum interval hyperplane depends only on the edge points of the two categories, these points are called support vectors, which is the origin of the name of the "Support Vector Machine".

![](https://s2.loli.net/2023/10/30/7o8CzfJQDcHqThL.png)

If the data points are indivisible in the original space, the low-dimensional indivisible data is mapped to high-dimensional linearly divisible, and so one of the main strengths of SVM is the ability to deal with high-dimensional data and to recognize complex relationships between features and target variables.

However, compared to some other machine learning algorithms, SVMs can be more computationally intensive to train and are relatively less explanatory due to being based on complex optimization problems. By the way, in order to map the data from low-dimensional to high-dimensional, the selection of the kernel function in SVM is very critical, and the commonly used kernel functions are linear kernel, polynomial kernel, Gaussian kernel (RBF kernel) and Sigmoid kernel.

```python
import numpy as np
from sklearn.svm import SVC

X_train, y_train, X_test, y_test = load_data()

model = SVC(kernel='linear', C=1.0)

model.fit(X_train, y_train)

predictions = model.predict(X_test)

print(predictions)
```

## **VIII. Simple Bayes (Naive Bayes)**

Naive Bayes is a machine learning algorithm based on Bayes' theorem for prediction,** it is called "Naive" because it assumes that all features in the dataset are independent of each other**, which is certainly not the case in real-world data.

![](https://s2.loli.net/2023/10/30/ZceU27nRwmNa9Vh.png)

Despite this assumption, plain Bayesian algorithms usually work well and are frequently used in financial investments and quantitative trading. Simple Bayesian algorithms are often used for classification tasks, such as predicting whether an index will rise or fall on the second day, whether a financial news story is positive or negative, or whether a financial statement is adulterated.


One of the main advantages of the PBAS algorithm is its simplicity and ease of understanding, even many success articles and books often advocate "Bayesian" thinking. The model is also very fast to train, even on large datasets. Moreover, the simple Bayesian algorithm tends to perform well when the underlying data is affected by certain types of noise, or when the number of features is too large relative to the number of samples.

```python
import numpy as np
from sklearn.naive_bayes 
import GaussianNB

X_train, y_train, X_test, y_test = load_data()

model = GaussianNB()

model.fit(X_train, y_train)

predictions = model.predict(X_test)

print(predictions)
```

## **IX. Neural networks**

**Neural networks are machine learning algorithms inspired by the structure and function of the human brain, and the model structure consists of a large number of interconnected nodes (neurons) that mimic the mechanisms of the human brain for processing and transmitting information. **Neural networks are particularly well suited for tasks involving pattern recognition and prediction, and have been used in a wide variety of fields and scenarios with very significant results.

![](https://s2.loli.net/2023/10/30/3ZjiIAgUC7P16Qm.png)

Neural network models are very supportive in both regression and classification tasks, requiring only human determination of the model structure, and are then able to learn, iterate, and adapt to new data without explicit programming, they can also handle large and complex datasets, and are able to recognize non-linear relationships between features and target variables.

Compared to some other machine learning algorithms, neural networks can consume more computational resources for training and are much less interpretable due to the complex network structure involved, and are very prone to overfitting if not designed and trained properly.

```python
import numpy as np
from tensorflow.keras.models 
import Sequentialfrom tensorflow.keras.layers 
import Dense

X_train, y_train, X_test, y_test = load_data()

model = Sequential()model.add(Dense(8, input_dim=4, activation='sigmoid'))model.add(Dense(1, activation='sigmoid'))model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])

model.fit(X_train, y_train, epochs=20, batch_size=32)

predictions = model.predict(X_test)

print(predictions)
```

## **X. Ensemble learning**

Ensemble learning (Ensemble learning) does not specifically refer to a particular algorithm, but a large class of algorithms, or the idea or method of training and combining models. ** In machine learning, a single model may not perform so well when running, but at the same time the combination of multiple models, "three cobblers topping a genius", will become very powerful, this combination of multiple basic models/algorithms, known as integrated learning. **

Integrated learning is generally divided into two main categories: Bagging (bagging method) and Boosting (boosting method). Both methods combine multiple weak models together to form a strong model, but the most obvious difference between them is that ** multiple weak models in Bagging are trained individually in parallel and then combined together, while Boosting is trained serially, and the next level of weak models are trained based on the "residuals" of the previous level of weak models Boosting is serial training, the next level of weak model is based on the "residuals" of the previous level of weak model targeted training, used to improve the previous level of "shortcomings", and then finally combined together. **In fact, integrated learning there are more complex Stacking (Stacking method) and Cascading (Cascading method), if you are interested, you can go to explore it.



**Bagging model structure: *![](https://s2.loli.net/2023/10/30/iV2rNGK7APfHdwg.png)*

**Boosting model structure:**

![](https://s2.loli.net/2023/10/30/ljQc4U5C9N2oPkY.png)

Bagging integrated learning in fact, we have seen before, that is, the random forest, in the field of financial investment and quantitative trading, Bagging is the most used it.

Boosting integrated learning is more, common are: gradient boosting tree GBDT, adaptive boosting algorithm Adaboost, extreme gradient boosting algorithm XGBoost and lightweight gradient boosting algorithm LightGBM. before commonly used XGBoost combination of multifactor models, now more popular with LightGBM, then LightGBM as a example to show it.

```python
import numpy as np
import lightgbm as lgb

X_train, y_train, X_test, y_test = load_data()

gbm = lgb.train(params={'learning_rate': 0.05, 
                        'lambda_l1': 0.1, 
                        'lambda_l2': 0.2,
                        'max_depth': 3, 
                        'objective': 
                        'multiclass', 
                        'num_class': 3}, train_set=lgb.Dataset(X_train, label=y_train))       

predictions = gbm.predict(X_test)predictions = [list(v).index(max(v)) for v in predictions]
print(predictions)
```

Quantitative trading in the field of 10 commonly used machine learning algorithms on the inventory finished, I do not know if you have found that even more complex machine learning algorithms, the use of readily available machine learning algorithms library (such as sklearn), in accordance with the "three-pronged" process, dozens of twenty lines of code can be modeled, Ma Ma no longer need to worry about our algorithms! realization.

This is the purpose of this article, most of the time in the future, we only need to focus on quantitative task disassembly and data organization, is the classification task (such as predicting ** up or down **), or regression task (such as predicting ** up or down **), and then the features / factors of the data organized in accordance with this "machine learning algorithms list" of the index, according to the need to use the corresponding algorithms by code examples will be able to get it done, along the way, according to the map.
