---
title: AIOQuant's asynchronous MySQL module
date: 2023-09-14 16:07:00
categories:
  - AIOQuant
tags:
  - Digital currency
  - Stock
  - Quantitative tool
  - Kuangbiao
  - digiccy
  - quant1098.com
  - trade
  - AIOQuant
description: 'aioquant is an asynchronous quantitative framework where traders can conduct high frequency quantitative trading'
cover: https://s2.loli.net/2023/09/14/ewZtRvLqoH7F8Up.jpg
---

## Introduction to AIOQuant

### 简介

`AIOQuant` is a set of `asynchronous event-driven` `quantitative trading` / `market making` system developed using `Python` language. It is designed to accommodate high-frequency trading strategies with its `aio* libraries` implementing asynchronous event loops at the lower level and `RabbitMQ message queues` enabling asynchronous event-driven at the business layer, plus the simplicity of `Python` language. It is well suited for developing high-frequency strategies and market making strategies for digital currencies. 

`AIOQuant` is also designed as a fully decoupled quantitative trading system, with main modules including `market data module`, `asset module`, `trading module`, `risk control module`, `storage module`. Each module can be detached and combined freely, or even redesigned and refactored using different languages. The modules interact with each other via `RabbitMQ message queues`, so they can even be deployed across different processes or servers.



`AIOQuant` provides simple yet powerful functionalities:
- Based on native Python Asyncio asynchronous event loop, processing is more concise and efficient.
-  Cross-platform (Windows, Mac, Linux), can be privately deployed. 
-   Unified trading methods (spot, futures) for any exchange. The same strategy only needs different configurations to seamlessly switch between exchanges.
-    Unified market data for all exchanges, triggering strategies to execute different orders through event subscription and callbacks.
-    Support running arbitrary number of strategies simultaneously. 
-    Support distributed running of arbitrary number of strategies.
-    Millisecond level latency (within 10ms, bottleneck is usually network latency).
-    Provide tasks, monitoring, storage, event publishing and other advanced functionalities. 
-    Customized Docker container, distributed configuration and deployment.
-    Quantitative trading web management system, enabling dynamic management of strategies, risk control, assets, servers and other processes or resources easily through the management tool. 

## **About the MySQL Asynchronous Module**

AIOQuant's database support is based on MongoDB, which is a  document-oriented database. However, MySQL is more widely used, and  using it for some low-frequency data storage is reasonable. Therefore, a MySQL module was added to support easy access. To unify the format, the MySQL module initializes a connection pool in  init_mysql like MongoDB, and implements methods through a MySQLBase  class. Each function is decorated to handle errors robustly.This enhances the code's resilience while providing a consistent interface  between MongoDB and MySQL. The asynchronous MySQL module improves  integration and leverages MySQL's popularity, while maintaining  AIOQuant's asynchronous event-driven architecture. 

## Related Code

Creating an asynchronous MySQL link requires the use of the asynchronous `mysql` library `aiomysql`, so the first step is to install this library:

```shell
pip install aiomysql
```

Then use the initialization function to link:

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

There's also a decorator that needs to be written to solve the problem of reporting errors in a networked environment:

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

Then you need to introduce connection pooling at the beginning of the class, and set up automatic link closure

```python
def __init__(self):
    self.pool = MYSQL_POOL

def __aexit__(self, exc_type, exc_val, exc_tb):
    self.pool.close()
    self.pool.wait_closed()
```

Then the `select` and `execute` functions are created to handle the usual must-have operations, such as creating links, receiving results, and so on.

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

The final work becomes very simple, for example, the function that creates the table looks like this

```python
@exceptions_handled
async def create_table(self, table_name, sql=None):
    """创建指定表'"""
    query = sql.format(table_name)
    await self.execute(query)
    return True, None
```

As you can see, there are only three lines of actual code. Of course, the sql statement looks like this

```python
sql = """create table {} (id int(8) AUTO_INCREMENT, time Datetime COMMENT '时间', open decimal(10,8) COMMENT '开盘价', high decimal(10,8) COMMENT '最高价', low decimal(10,8) COMMENT '最低价', close decimal(10,8) COMMENT '收盘价', volume DOUBLE COMMENT '计价货币交易量/基础货币交易量', quote_base_asset_volume DOUBLE COMMENT '成交量', num_of_trades INT COMMENT '交易次数', Taker_buy_base_asset_volume DOUBLE COMMENT 'Taker买入的基础资产量', Taker_buy_quote_asset_volume DOUBLE COMMENT 'Taker买入的计价资产量', PRIMARY KEY (id))"""
```

It looks a bit messy. Actually, it's not particularly hard to recognize if it's aligned properly, it looks like this:

![2023-09-14_161438.png](https://s2.loli.net/2023/09/14/qml2CRMDc9dU3Ab.png)

The function that returns a list of database table names is written in the same way as traditional synchronization library code.

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

The insert function looks like this.

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

These ordinary functions do not say much, there is an insert DataFrame format data written to count the long, but only 30 lines of code, the rest is too short not much to say!

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

Generic functions for executing sql statements

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

I won't tell you the rest. Just add your own methods.



## Conclusion

Adding a `MySQL` library to `aioquant`, a high-performance asynchronous framework, is not a big deal, since the `mongo` library is already enabled by default, and you can use the temporal databases `InfluxDB` and `Kdb+` if you feel that the performance is not enough for you to use them, it all depends on your own personal needs. If you have enough power and money, you can use a fast temporal database, and if you are analyzing backtests, then a general-purpose `MySQL` is not out of the question. After all, we are here to fish, not to be a fishing immortal, specialized in feeding the fish.

Besides, this function is still a bit insufficient, so feel free to browse and discuss.

![](https://s2.loli.net/2023/09/14/aiTGmv57fjJLZdh.webp)
