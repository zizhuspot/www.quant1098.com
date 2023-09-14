---
title: aioquant的异步MySQL模块
date: 2023-09-14 16:07:00
categories:
  - aioquant
tags:
  - 数字货币
  - 股票
  - 量化工具
  - 狂飙
  - digiccy
  - quant1098.com
  - trade
  - aioquant
description: 'aioquant是一个异步的量化框架，交易员可以在上面进行高频量化交易'
cover: https://s2.loli.net/2023/09/14/ewZtRvLqoH7F8Up.jpg
---

## 关于AIOQuant

### 简介

`AIOQuant` 是一套使用 `Python` 语言开发的 `异步事件驱动` 的 `量化交易` / `做市` 系统，它被设计为适应中高频策略的交易系统， 底层封装了操作系统的 `aio*库` 实现异步事件循环，业务层封装了 `RabbitMQ消息队列` 实现异步事件驱动，再加上 `Python` 语言的简单易用， 它非常适用于数字货币的高频策略和做市策略开发。

`AIOQuant` 同时也被设计为一套完全解耦的量化交易系统，其主要模块包括 `行情系统模块`、`资产系统模块`、`交易系统模块`、`风控系统模块`、`存储系统模块`， 各个模块都可以任意拆卸和组合使用，甚至采用不同的开发语言设计重构，模块之间通过 `RabbitMQ消息队列` 相互驱动，所以不同模块还可以部署在不同的进程， 或不同服务器。

### 功能

`AIOQuant` 提供了简单而强大的功能：

- 基于 [Python Asyncio](https://docs.python.org/3/library/asyncio.html) 原生异步事件循环，处理更简洁，效率更高；
- 跨平台（Windows、Mac、Linux），可任意私有化部署；
- 任意交易所的交易方式（现货、合约）统一，相同策略只需要区别不同配置，即可无缝切换任意交易所；
- 所有交易所的行情统一，并通过事件订阅的形式，回调触发策略执行不同指令；
- 支持任意多个策略协同运行；
- 支持任意多个策略分布式运行；
- 毫秒级延迟（10毫秒内，一般瓶颈在网络延迟）；
- 提供任务、监控、存储、事件发布等一系列高级功能；
- 定制化Docker容器，分布式配置、部署运行；
- 量化交易Web管理系统，通过管理工具，轻松实现对策略、风控、资产、服务器等进程或资源的动态管理；

## 关于MySQL异步模块

本身AIOQuant 数据库支持是mongo数据库 一个文档型的数据库，但是mysql毕竟用的人比较多，还有一些低频数据存MySQL问题也不大，所以添加了 MySQL模块支持，方便调用，为了统一格式，采用了跟mongo数据库统一的调用模式，即在init_mysql函数里面初始化连接池，然后由一个mysql类MySQLBase进行方法实现，同时每个函数加上装饰器来解决报错问题。增强代码的健壮性。

## 相关代码

创建异步的MySQL链接需要使用异步`mysql`库 `aiomysql`， 因此 第一步需要安装这个库：

```shell
pip install aiomysql
```

然后使用初始化函数进行链接：

```python
async def init_mysql(host, port, user, password, database, max_conn_time=3, max_reconn_tries=3):
    global MYSQL_POOL
    for i in range(max_reconn_tries):
        try:
            MYSQL_POOL = await aiomysql.create_pool(host=host, port=port, user=user, password=password, db=database,connect_timeout=max_conn_time)
            logger.info('Create MySQL connection pool.')
            break
        except aiomysql.OperationalError as e:
            logger.info(f'MySQL connect failed: {e}')
            if i == max_reconn_tries - 1:
                logger.info(f'Reach max reconnection tries {max_reconn_tries}, exit.')
                exit(1)
            else:
                logger.info(f'Trying to reconnect...{i+1}')
    else:
        logger.info('MySQL reconnected!')
```

还有需要写一个装饰器，来解决网络环境报错问题：

```python
def exceptions_handled(func):
    """ try... except装饰器"""
    @wraps(func)
    async def wrapper(self, *args, **kwargs):
        try:
            return await func(self, *args, **kwargs)
        except Exception as e:
            logger.error(f"Error in {func.__name__}, error info:{e}", caller=self)
            return None, e

    return wrapper
```

然后需要在类开始的时候引入连接池，和设置自动关闭链接

```python
def __init__(self):
    self.pool = MYSQL_POOL

def __aexit__(self, exc_type, exc_val, exc_tb):
    self.pool.close()
    self.pool.wait_closed()
```

再然后 创立`select`函数和`execute`函数 来处理一些常规必须操作，比如建立链接，接收返回结果之类的。

```python
# 执行select函数
async def select(self, sql, args=None, size=None):
    with await self.pool as conn:
        cur = await conn.cursor(aiomysql.DictCursor)
        await cur.execute(sql.replace('?', '?s'), args or ())
        if size:
            rs = await cur.fetchmany(size)
        else:
            rs = await cur.fetchall()
        return rs, cur

# 执行insert update delete 函数
async def execute(self, sql, args=None):
    with await self.pool as conn:
        try:
            cur = await conn.cursor()
            await cur.execute(sql.replace('?', '%s'), args)
            affetced = cur.rowcount
            await conn.commit()
            await cur.close()
        except BaseException as e:
            raise
        return affetced
```

最后的工作就变得异常简单，比如创建表得函数 长这样

```python
@exceptions_handled
async def create_table(self, table_name, sql=None):
    """创建指定表'"""
    query = sql.format(table_name)
    await self.execute(query)
    return True, None
```

可以看到 实际代码就只有三行。当然 sql语句长这样子

```python
sql = """create table {} (id int(8) AUTO_INCREMENT, time Datetime COMMENT '时间', open decimal(10,8) COMMENT '开盘价', high decimal(10,8) COMMENT '最高价', low decimal(10,8) COMMENT '最低价', close decimal(10,8) COMMENT '收盘价', volume DOUBLE COMMENT '计价货币交易量/基础货币交易量', quote_base_asset_volume DOUBLE COMMENT '成交量', num_of_trades INT COMMENT '交易次数', Taker_buy_base_asset_volume DOUBLE COMMENT 'Taker买入的基础资产量', Taker_buy_quote_asset_volume DOUBLE COMMENT 'Taker买入的计价资产量', PRIMARY KEY (id))"""
```

看起来比较乱 其实如果对齐得话，也不是特别难以识别，长这样子：

![2023-09-14_161438.png](https://s2.loli.net/2023/09/14/qml2CRMDc9dU3Ab.png)

返回数据库表名列表的函数， 跟传统的同步库代码写起来没什么不同

```python
@exceptions_handled
async def get_table_names(self):
    """ 返回数据表名称列表"""
    query = 'SHOW TABLES'
    tables, cur = await self.select(query)
    if tables:
        table_name_key = list(tables[0].keys())[0]
        table_names = [item[table_name_key] for item in tables]
        return table_names, None
    else:
        return None, None
```

插入数据函数 长这样

```python
    @exceptions_handled
    async def insert_data(self, table_name, data):
        """插入数据"""
        columns = ', '.join(data.keys())
        values = tuple(data.values())
        query = f"INSERT INTO {table_name} ({columns}) VALUES {values}"
        await self.execute(query)
        return True, Non
```

这些普通的函数就不多说了，有一个插入DataFrame格式数据写的算长的， 也不过30行代码，其余的太短没啥可说的

```python
@exceptions_handled
async def update_data_by_df(self, table_name, df):
    """ 在数据库中加入DataFrame格式数据"""
    batch_size = 9999  # 设置批量插入数据大小
    # 获取DataFrame的元数据
    cols = list(df.columns)

    # 构造INSERT语句
    insert_sql = f"INSERT INTO {table_name} ({','.join(cols)}) VALUES "
    # 分批读取DataFrame并执行写入
    for i in range(0, df.shape[0], batch_size):
        df_batch = df.iloc[i:i + batch_size]
        values = []
        for row in df_batch.values:
            row_str = []
            for v in row:
                if isinstance(v, datetime):
                    row_str.append(f"'{v.strftime('%Y-%m-%d %H:%M:%S')}'")
                elif isinstance(v, int):
                    row_str.append(str(v))
                elif isinstance(v, float):
                    row_str.append(f"{round(v, 2)}")
                else:
                    row_str.append(str(v))
            values.append(f"({','.join(row_str)})")

        values_sql = ",".join(values)
        query = insert_sql + values_sql
        # 执行写入
        await self.execute(query)
    logger.info("update DateFrame data is success", caller=self)
    return True, None
```

执行sql语句的通用函数

```python
@exceptions_handled
async def query_data(self, table_name, columns, condition=''):
    """查询的封装,接收表名、字段名、条件参数"""
    columns = ', '.join(columns) if columns else '*'
    query = f"SELECT {columns} FROM {table_name}"
    if condition:
        query += f" WHERE {condition}"
    result, cur = await self.select(query)
    return result, None
```

剩下的就不多说了， 自己添加各种方法就好了



## 结束语

在`aioquant`这个高性能的异步框架上添加`MySQL`库是一件意义不算太大的事情，因为已经有`mongo`库默认启用，觉得性能不够还可以用时序数据库`InfluxDB` 和`Kdb+` 	全看你自己的个人需要。如果能力和金钱够就用快的时序数据库，如果是回测分析 那么通用的`MySQL`也不是不行。毕竟我们是来钓鱼的，不是来当钓鱼仙人，专业给鱼喂饭的。

另外， 这个函数还是有些不足，欢迎浏览讨论。

![](https://s2.loli.net/2023/09/14/aiTGmv57fjJLZdh.webp)
