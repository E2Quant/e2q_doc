# 数据库

## 表关系图

![SQL mind map](./images/e2q_postgresql.png "SQL")

---

## PG view 和 funcion

[e2q postgresql sql file](https://github.com/E2Quant/e2q/tree/main/cfg)

## view 部分说明


###  1.资金变化

* **e2q_cash**

> **说明:**
    初始化资金与当前资金的情况.


> 示例:
```
E2Q=> select * from e2q_cash;
-[ RECORD 1 ]---------------------
quantid   | 1032451500
name      | CLIENT874
stock     | 600519
verid     | 1
init_cash | 1000000
now_cash  | 1009794
diff_cash | 9794
diff_per  | 0.009794
day       | 2023-11-03 00:00:00+08
end_day   | 2024-07-22 00:00:00+08

```

---

###  2.资金变化

* **e2q_cash_se**

> **说明:**
    初始化资金与当前资金的情况.


> 示例:
```
E2Q=> select * from e2q_cash_se;
-[ RECORD 1 ]-------------------
quantid | 1032451498
name    | CLIENT874
stock   | 600030
tid     | 113
credit  | 1009794
verid   | 1
symbol  | 1
day     | 2024-07-22 00:00:00+08
stat    | end
-[ RECORD 2 ]-------------------
quantid | 1032451500
name    | CLIENT874
stock   | 600519
tid     | 1
credit  | 1000000
verid   | 1
symbol  | 3
day     | 2023-11-03 00:00:00+08
stat    | init

```
---

###  3.账户资金波动

* **e2q_fix_cash**

> **说明:**
    账户资金波动.


> 示例:
```
E2Q=> select * from e2q_fix_cash;
-[ RECORD 1 ]--+----------
targetcompid   | CLIENT874
max_credit     | 1016259
mix_credit     | 1000000
credit         | 1000000
pre_mix_credit | 0
pre_max_credit | 0.016259

```
---

###  4.订单历史记录

* **e2q_history**

> **说明:**
    回测过程中的所有订单历史记录.


> 示例:
```
E2Q=> select * from e2q_history limit 2;
-[ RECORD 1 ]+-----------------------
sid          | 2
verid        | 1
symobl       | 179593
stock        | 600519
buy_price    | 1811.24
buy_time     | 2023-11-03 00:00:00+08
stop_price   | 1812
stop_time    | 2023-11-06 00:00:00+08
sell_adjpx   | 11131.702
buy_adjpx    | 11127.425
profit       | 0.038
closetck     | 1032451500
LossOrProfit | StopLoss
cash         | 0
share        | 0
quantid      | 1032451500
bticket      | 1032451500
sticket      | 1032451502
position     | 0.9
amount       | 304
qty          | 4
-[ RECORD 2 ]+-----------------------
sid          | 7
verid        | 1
symobl       | 179593
stock        | 600519
buy_price    | 1791.17
buy_time     | 2023-11-07 00:00:00+08
stop_price   | 1798.34
stop_time    | 2023-11-08 00:00:00+08
sell_adjpx   | 11054.827
buy_adjpx    | 11014.476
profit       | 0.366
closetck     | 1032451506
LossOrProfit | StopLoss
cash         | 0
share        | 0
quantid      | 1032451500
bticket      | 1032451506
sticket      | 1032451508
position     | 0.9
amount       | 717
qty          | 1

```

---

###  5.订单的仓位情况

* **e2q_postion**

> **说明:**
    回测过程中的关于订单占当前仓位的情况.


> 示例:
```
E2Q=> select * from e2q_postion limit 2;
-[ RECORD 1 ]-------------------
verid   | 1
quantid | 1032451498
values  | 0.9
type    | 2
date    | 2024-07-22 00:00:00+08
rule    | mode_m_8/17/30/60
-[ RECORD 2 ]-------------------
verid   | 1
quantid | 1032451498
values  | 0.9
type    | 2
date    | 2024-07-18 00:00:00+08
rule    | mode_m_8/17/30/60


```

---

###  6.订单最原始的记录

* **e2q_primitive**

> **说明:**
    回测过程中的关于订单如何在数据表中记录的情况.


> 示例:
```
E2Q=> SELECT * FROM "e2q_primitive" limit 2;
-[ RECORD 1 ]---------------------
verid     | 1
id        | 1
stat      | 0
side      | 1
price     | 1814.89
adjpx     | 0
stoppx    | 0
cumqty    | 0
leavesqty | 0
openqty   | 0
qty       | 4
date      | 2023-11-03 00:00:00+08
argv      | 8/17/30/60
name      | mode_m
ticket    | 1032451500
closetck  | 0
amount    | 0
-[ RECORD 2 ]---------------------
verid     | 1
id        | 2
stat      | 2
side      | 1
price     | 1811.24
adjpx     | 11127.425
stoppx    | 0
cumqty    | 4
leavesqty | 0
openqty   | 0
qty       | 4
date      | 2023-11-03 00:00:00+08
argv      | 8/17/30/60
name      | mode_m
ticket    | 1032451500
closetck  | 0
amount    | 724496
```

---

###  7.参数的收益值

* **e2q_profit**

> **说明:**
    回测过程中的各个策略的参数收益情况.


> 示例:
```
E2Q=> SELECT * FROM "e2q_profit" limit 2;
-[ RECORD 1 ]+-----------
name         | mode_m
argv         | 8/17/30/60
version      | 1.3.0
quantid      | 1032451501
init_cash    | 1000000
profit       | 0
postion      | 0
verid        | 1
targetcompid |
-[ RECORD 2 ]+-----------
name         | mode_m
argv         | 8/17/30/60
version      | 1.3.0
quantid      | 1032451498
init_cash    | 1000000
profit       | 0
postion      | 0
verid        | 1
targetcompid | CLIENT874

```
---

###  8.风险(MVO)

* **e2q_risk_performance**

> **说明:**
    mean-variance optimization(MVO) 数据 .
    risk 相关的，都是采用[PyPortfolioOpt](
    https://pyportfolioopt.readthedocs.io/en/latest/UserGuide.html) 生成

> 示例:
```
E2Q=> SELECT * FROM e2q_risk_performance limit 2;
-[ RECORD 1 ]-------------------
risk    | Expected_annual_return
quantid | 1032131609
verid   | 1
values  | 0.3001
day     | 2024-07-30 00:00:00+08
-[ RECORD 2 ]-------------------
risk    | Annual_volatility
quantid | 1032131609
verid   | 1
values  | 38.2169
day     | 2024-07-30 00:00:00+08

```

---

###  9.收益曲线(包含风险)

* **e2q_risk_profit**

> **说明:**
     收益曲线

> 示例:
```
E2Q=> SELECT * FROM e2q_risk_profit limit 2;
-[ RECORD 1 ]-------------
symbol     | 1
ticket     | 1032131615
closetck   | 0
side       | 1
quantid    | 1032131609
ctime      | 1409673600000
amount     | 43875
buy_amount | 0
stock      | 600048
verid      | 1
profit     | 0
profit_pre | 0
-[ RECORD 2 ]-------------
symbol     | 1
ticket     | 1032131617
closetck   | 1032131615
side       | 2
quantid    | 1032131609
ctime      | 1410451200000
amount     | 42750
buy_amount | 43875
stock      | 600048
verid      | 1
profit     | -1125
profit_pre | -0.025

```
---

### 10.个股的风险仓位分布

* **e2q_risk_value**

> **说明:**
    个股的风险仓位分布

> 示例:
```
E2Q=> SELECT * FROM e2q_risk_value limit 2;
-[ RECORD 1 ]-------------------
quantid | 1032206436
values  | 0
day     | 2014-08-26 00:00:00+08
stock   | 601398
verid   | 5
-[ RECORD 2 ]-------------------
quantid | 1032206432
values  | 0
day     | 2014-08-26 00:00:00+08
stock   | 600048
verid   | 5

```

---

###  11.股票池

* **e2q_symbol_pool**

> **说明:**
    股票池列表.


> 示例:
```
E2Q=> select * from e2q_symbol_pool;
 verid | stock  | trader_number | trading_number
-------+--------+---------------+----------------
     1 | 600048 |             5 |              0
(1 row)

```

---

###  12.正在交易中

* **e2q_trading**

> **说明:**
    正在交易进行中的订单.


> 示例:
```
E2Q=> select * from e2q_trading;
 id | verid | symbol | stock | open_price | open_qty | open_time | ticket | amount | quantid | name | argv
----+-------+--------+-------+------------+----------+-----------+--------+--------+---------+------+------
(0 rows)


```

---

## function 部分说明

---

###  1.按版本查看不同账号资金情况

* **quant_account**

> **说明:**
    按版本查看不同账号资金情况.

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_account(1);
-[ RECORD 1 ]+----------
targetcompid | CLIENT845
sessionid    | 845
balance      | 925602.88
margin       | 0
all_cash     | 925602.88
mode         | mode_ha

```


---

###  2.资金波动

* **quant_bands**

> **说明:**
    资金波动.

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_bands(1) limit 2;
-[ RECORD 1 ]-------------------
idxs      | 1
quantid   | 1032131610
name      | mode_ha
argv      | 22
init_cash | 1000000
min_pro   | 827898.32
max_pro   | 1064416
min_diff  | -0.17210168000000006
max_diff  | 0.064416
-[ RECORD 2 ]-------------------
idxs      | 2
quantid   | 1032131612
name      | mode_ha
argv      | 32
init_cash | 1000000
min_pro   | 931100.4500000001
max_pro   | 1017375
min_diff  | -0.06889954999999993
max_diff  | 0.017375


```

---

###  3.资金情况

* **quant_profit**

> **说明:**
    资金的详细变化.

    - 参数: quantid 
    - 参数: credit


> 示例:
```
E2Q=> SELECT
    pday as "时间",
    profit_sum AS "资金",
    CASE
        WHEN pside = 1 THEN '开仓'
        ELSE '平仓'
    END AS "状态"
from "quant_profit" (
        (
            SELECT "quantid"
            from "analse"
            LIMIT 1
        ), (
            SELECT "credit"
            from "trade_report"
            ORDER BY id
            LIMIT 1
        )
    );
        时间         |  资金  | 状态
---------------------+--------+------
 2023-11-14 00:00:00 | 500000 | 开仓
 2023-11-16 00:00:00 | 498487 | 平仓
 2024-01-26 00:00:00 | 498487 | 开仓
 2024-03-04 00:00:00 | 483255 | 平仓
 2024-03-14 00:00:00 | 483255 | 开仓
 2024-03-22 00:00:00 | 474375 | 平仓
 2024-04-26 00:00:00 | 474375 | 开仓
 2024-05-27 00:00:00 | 495412 | 平仓
 2024-07-17 00:00:00 | 495412 | 开仓
 2024-07-29 00:00:00 | 477628 | 平仓
(10 rows)

```

---

###  4.模型列表(风险)

* **quant_profit_mvo**

> **说明:**
    按版本查看不同账号资金情况.

    - 参数: atype 

> 示例:
```
E2Q=> SELECT * FROM quant_profit_mvo(1);
-[ RECORD 1 ]+----------
targetcompid | CLIENT845
sessionid    | 845
balance      | 925602.88
margin       | 0
all_cash     | 925602.88
mode         | mode_ha

```

---

###  5.可从版本号取资金情况

* **quant_profit_one_verid**

> **说明:**
    资金的详细变化.
    
    - 参数: verid 
    - 参数: offset

> 示例:
```
E2Q=> SELECT * from quant_profit_one_verid (1,0) limit 2;
-[ RECORD 1 ]--------------------
vday        | 2015-06-12 00:00:00
vprofit_sum | 1000000
stat        | 开仓
pqid        | 1032131610
qverid      | 1
-[ RECORD 2 ]--------------------
vday        | 2015-06-29 00:00:00
vprofit_sum | 1064416
stat        | 平仓
pqid        | 1032131610
qverid      | 1


```

---

###  6.返回个股日线周期累计回报收益

* **quant_return**

> **说明:**
    返回个股日线周期累计回报收益.
    
    - 参数: cficode 

> 示例:
```
E2Q=> SELECT * from quant_return(179591) limit 5;
-[ RECORD 1 ]+--------------------
rstock       | 600048
pday         | 2014-06-04 00:00:00
price        | 89.1838
return_value | 0
-[ RECORD 2 ]+--------------------
rstock       | 600048
pday         | 2014-06-05 00:00:00
price        | 86.9694
return_value | -2.482962152319156
-[ RECORD 3 ]+--------------------
rstock       | 600048
pday         | 2014-06-06 00:00:00
price        | 88.077
return_value | -1.2410325642100994
-[ RECORD 4 ]+--------------------
rstock       | 600048
pday         | 2014-06-09 00:00:00
price        | 88.235
return_value | -1.0638703441656507
-[ RECORD 5 ]+--------------------
rstock       | 600048
pday         | 2014-06-10 00:00:00
price        | 91.3986
return_value | 2.483410664268619

```

###  7.个股及指数每天回报(%)

* **quant_return_day**

> **说明:**
    个股及指数每天回报(%).
    
    - 参数: cficode 

> 示例:
```
E2Q=> SELECT * from quant_return_day(179591) limit 5;
-[ RECORD 1 ]+----------------------
rstock       | 600048
pday         | 2014-06-04 00:00:00
price        | 89.1838
return_value | 0
-[ RECORD 2 ]+----------------------
rstock       | 600048
pday         | 2014-06-05 00:00:00
price        | 86.9694
return_value | -0.024829621523191563
-[ RECORD 3 ]+----------------------
rstock       | 600048
pday         | 2014-06-06 00:00:00
price        | 88.077
return_value | 0.012735513870395853
-[ RECORD 4 ]+----------------------
rstock       | 600048
pday         | 2014-06-09 00:00:00
price        | 88.235
return_value | 0.0017938848961704106
-[ RECORD 5 ]+----------------------
rstock       | 600048
pday         | 2014-06-10 00:00:00
price        | 91.3986
return_value | 0.03585425284750952

```

---

###  8.返回个股月回报

* **quant_return_fmonth**

> **说明:**
    返回个股月回报.
    
    - 参数: cficode 

> 示例:
```
E2Q=> SELECT * from quant_return_fmonth(179591) limit 5;
-[ RECORD 1 ]+--------------------
rstock       | 600048
pday         | 2014-06
price        | 89.1838
return_value | 0
-[ RECORD 2 ]+--------------------
rstock       | 600048
pday         | 2014-07
price        | 88.551
return_value | -0.7095459040767529
-[ RECORD 3 ]+--------------------
rstock       | 600048
pday         | 2014-08
price        | 105.1606
return_value | 18.757100428001944
-[ RECORD 4 ]+--------------------
rstock       | 600048
pday         | 2014-09
price        | 99.7822
return_value | -5.114463021321672
-[ RECORD 5 ]+--------------------
rstock       | 600048
pday         | 2014-10
price        | 97.8842
return_value | -1.9021428671646807

```

---

###  9.返回个股月累计回报

* **quant_return_month**

> **说明:**
    返回个股月累计回报.
    
    - 参数: cficode 

> 示例:
```
E2Q=> SELECT * from quant_return_month(179591) limit 5;
-[ RECORD 1 ]+--------------------
rstock       | 600048
pday         | 2014-06
price        | 89.1838
return_value | 0
-[ RECORD 2 ]+--------------------
rstock       | 600048
pday         | 2014-07
price        | 88.551
return_value | -0.7095459040767529
-[ RECORD 3 ]+--------------------
rstock       | 600048
pday         | 2014-08
price        | 105.1606
return_value | 17.91446428611474
-[ RECORD 4 ]+--------------------
rstock       | 600048
pday         | 2014-09
price        | 99.7822
return_value | 11.88377261341185
-[ RECORD 5 ]+--------------------
rstock       | 600048
pday         | 2014-10
price        | 97.8842
return_value | 9.755583413131086

```

---

###  10.资金波动(%)/日

* **quant_risk_profix**

> **说明:**
    资金波动(%)/日.
    
    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * from quant_risk_profix(1) limit 2;
-[ RECORD 1 ]--+-----------------------
rid            | 1
rday           | 2014-09-02 00:00:00+08
rcredit        | 1000000
rpcredit_first | 0
rpcredit_pre   | 0
-[ RECORD 2 ]--+-----------------------
rid            | 2
rday           | 2014-09-03 00:00:00+08
rcredit        | 1000000
rpcredit_first | 0
rpcredit_pre   | 0

```

---

###  11.资金波动(%)/月

* **quant_risk_profix_month**

> **说明:**
    资金波动(%)/月.
    
    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * from quant_risk_profix_month(1) limit 2;
-[ RECORD 1 ]--+---------
rid            | 1
rday           | 2014-09
rcredit        | 1000000
rpcredit_first | 0
rpcredit_pre   | 0
-[ RECORD 2 ]--+---------
rid            | 11
rday           | 2014-10
rcredit        | 1007148
rpcredit_first | 7148
rpcredit_pre   | 0.007148

```

---

## 数据可以采用 BI 分析

### metabase 数据分析


来源: [metabase](https://github.com/metabase/metabase)

![Metabase](./images/metabase.png "Metabase")