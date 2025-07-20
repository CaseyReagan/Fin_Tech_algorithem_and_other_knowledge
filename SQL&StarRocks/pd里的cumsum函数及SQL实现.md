# Pandas里的Cumsum函数及用SQL实现
本文借鉴：1.https://cloud.tencent.com/developer/article/1971956
   
### （一）背景
想要查看证券账户的持仓情况，包括该账户在某一种类产品下最大持仓量时，因为有开仓平仓的加加减减，就需要按时间顺序来排列后做一个累计求和，取最终结果的最大值。
      
</br>
   
### （二）Pandas里的累计求和函数Cumsum
Cumsum是pandas的累加函数，用来求列的累加值。
```python
DataFrame.cumsum(axis=None, skipna=True, args, kwargs)
  
# 参数作用：

# axis：index或者轴的名字
# skipna：排除NA/null值
```
以前面的df为例，group列有A、B、C三组，year列有多个年份。我们只知道当年度的值value_1、value_2，现在求group分组下的累计值，比如A、2014之前的累计值，可以用cumsum函数来实现。
   
当然仅用cumsum函数没办法对groups (A, B, C)进行区分，所以需要结合分组函数groupby分别对(A, B, C)进行值的累加。
```python
df['cumsum_2'] = df[['value_2','group']].groupby('group').cumsum()
```
|group|	year|  new_col	 |value_1|	value_2| cumsum_2|
| ------- | ------- | ------- | ------- | ------- | ------- |
| A	| 2010	| 0.332581	 |2 |   3|	 3 |
| A	| 2011	| 0.122312	 |3 |   3|	 6 |
| B	| 2012	| 0.551692	 |5 |   1|	 1 |
| A	| 2013	| -1.515015	 |9 |   4|	 1 |
| B	| 2014	| 0.078729	 |6 |   1|	 2 |
| B	| 2015	| -0.375737	 |7 |   9|	 1 |
| C	| 2016	| -1.15901	 |4 |   2|	 2 |
| A	| 2017	| -0.161005	 |8 |   5|	 1 |
| C	| 2018	| 0.275472	 |4 |   6|	 8 |
| C	| 2019	| 0.113718	 |0 |   9|	 1 |
   
</br>
   
   
### （三）在SQL里实现cumsum
SQL中的 SUM() OVER(PARTITION BY) 完全可以实现类似 Pandas 的 cumsum() 累计求和功能。
例如表：   
| month	  | region  |revenue  |
| ------- | ------- | ------- |
| 2023-01 | 华东	 |100|
| 2023-01 | 华南	 |150|
| 2023-02 | 华东	 |200|
| 2023-02 | 华南	 |180|
| 2023-03 | 华东	 |120|
| 2023-03 | 华南	 |160|

```sql
SELECT 
    month, 
    region, 
    revenue,
    SUM(revenue) OVER(ORDER BY month) AS total_cumsum
FROM sales_data;

```

|month	    |region	|revenue |total_cumsum|
| -------   | ------- | ------- | ------- |
|2023-01	| 华东 |  100	 |   100|
|2023-01	| 华南 |  150	 |   250|	
|2023-02	| 华东 |  200	 |   450|	
|2023-02	| 华南 |  180	 |   630|

这是不分组直接累计的。下面是一种分组累计。加上PARTITION BY。
（类似 df.groupby('region')['revenue'].cumsum()）‌
```sql

SELECT 
    month, 
    region, 
    revenue,
    SUM(revenue) OVER(PARTITION BY region ORDER BY month) AS region_cumsum
FROM sales_data;

```

|month	 |  region	| revenue	|region_cumsum|
| -------   | ------- | ------- | ------- |
|2023-01 |   华东	 | 100	|    100 |
|2023-02 |   华东	 | 200	|    300 |	
|2023-03 |   华东	 | 120	|    420 |
|2023-01 |   华南	 | 150	|    150 |
|2023-02 |   华南	 | 180	|    330 |	

   
</br>
   
### （四）类似累加函数的累乘函数cumprod
效果类似，对指定列开始累乘
```python
df["age"].cumprod()

# 022 1528 213200 3343200 48236800
```