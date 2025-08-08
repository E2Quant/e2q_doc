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


###  3.订单历史记录

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

###  4.订单的仓位情况

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

###  5.参数的收益值

* **e2q_profit**

> **说明:**
    回测过程中的各个策略的参数收益情况.


> 示例:
```
E2Q=> SELECT * FROM "e2q_profit" limit 2;
-[ RECORD 1 ]+-------------
name         | mode_ma
argv         | 15/39
version      | 1.3.0
quantid      | 1036343292
init_cash    | 200000.00298
profit       | 198860.003
postion      | 0.2
verid        | 1
targetcompid | CLIENT2851
-[ RECORD 2 ]+-------------
name         | mode_vma
argv         | 5/13
version      | 1.3.0
quantid      | 1036343281
init_cash    | 200000.00298
profit       | 199148.003
postion      | 0.2
verid        | 1
targetcompid | CLIENT2852

```
---

###  6.风险(MVO)

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

###  7.收益曲线(包含风险)

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

### 8.个股的风险仓位分布

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

###  9.股票池

* **e2q_symbol_pool**

> **说明:**
    股票池列表.


> 示例:
```
E2Q=>  select * from e2q_symbol_pool limit 2;
-[ RECORD 1 ]--+-----------
quantid        | 1036343281
count          | 2
verid          | 1
stock          | 600048
trader_number  | 2
trading_number | 0
-[ RECORD 2 ]--+-----------
quantid        | 1036343282
count          | 6
verid          | 1
stock          | 600048
trader_number  | 5
trading_number | 1

```

---

###  10.正在交易中

* **e2q_trading**

> **说明:**
    正在交易进行中的订单.


> 示例:
```
E2Q=> select * from e2q_trading limit 2;
-[ RECORD 1 ]-----------
id         | 1571
verid      | 5
symbol     | 294522
stock      | 600028
open_price | 4.12
open_qty   | 435
open_time  | 2022/11/02
ticket     | 1036344727
amount     | 179220
quantid    | 1036345708
name       | mode_adxvma
argv       | 25/65
-[ RECORD 2 ]-----------
id         | 1952
verid      | 6
symbol     | 294523
stock      | 601398
open_price | 4.32
open_qty   | 46
open_time  | 2022/12/28
ticket     | 1036345108
amount     | 19872
quantid    | 1036346309
name       | mode_adxvma
argv       | 20/52

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

###  2.各个账号的账号的资金变动概要(日)

* **quant_account_credit**

> **说明:**
    各个账号的账号的资金变动概要(日).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_account_credit(1) limit 2;
-[ RECORD 1 ]+-----------------------
credit       | 1000000
targetcompid | CLIENT2850
pday         | 2022-07-27 00:00:00+08
-[ RECORD 2 ]+-----------------------
credit       | 980968
targetcompid | CLIENT2850
pday         | 2022-08-03 00:00:00+08

```

---

###  3.各个账号的资金波动(最大/小的百分比)

* **quant_account_credit_bands**

> **说明:**
    各个账号的资金波动(最大/小的百分比).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_account_credit_bands(1) limit 2;
-[ RECORD 1 ]-+-----------------------
btargetcompid | CLIENT2852
bmax_credit   | 1000252
bmin_credit   | 990243
binit_credit  | 1000000
bmpday        | 2022-08-16 00:00:00+08
-[ RECORD 2 ]-+-----------------------
btargetcompid | CLIENT2850
bmax_credit   | 1000000
bmin_credit   | 899328
binit_credit  | 1000000
bmpday        | 2022-07-27 00:00:00+08

```

---

###  4.各个账号的收益曲线(日)

* **quant_account_credit_day**

> **说明:**
    各个账号的收益曲线(日).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_account_credit_day(1) limit 2;
-[ RECORD 1 ]-+-----------------------
dcredit       | 1000000
dtargetcompid | CLIENT2850
dpday         | 2022-07-27 00:00:00+08
dreturn_value | 0
-[ RECORD 2 ]-+-----------------------
dcredit       | 980968
dtargetcompid | CLIENT2850
dpday         | 2022-08-03 00:00:00+08
dreturn_value | -1.9032

```

---

###  5.各个账号的收益曲线(月)

* **quant_account_credit_month**

> **说明:**
    各个账号的收益曲线(月).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_account_credit_month(1) limit 2;
-[ RECORD 1 ]-+-----------
dcredit       | 1000000
dtargetcompid | CLIENT2850
dpday         | 2022-07
dreturn_value | 0
-[ RECORD 2 ]-+-----------
dcredit       | 899328
dtargetcompid | CLIENT2850
dpday         | 2022-08
dreturn_value | -10.0672

```

---

###  6.各个账号的累计收益(%)

* **quant_account_credit_sum**

> **说明:**
    各个账号的累计收益(%).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_account_credit_sum(1) limit 2;
-[ RECORD 1 ]-+-----------------------
dcredit       | 1000000
dtargetcompid | CLIENT2850
dpday         | 2022-07-27 00:00:00+08
dreturn_value | 0
-[ RECORD 2 ]-+-----------------------
dcredit       | 980968
dtargetcompid | CLIENT2850
dpday         | 2022-08-03 00:00:00+08
dreturn_value | -1.9032

```

---

###  7.各个 QuantId 的资金波动(最大/小的百分比)

* **quant_bands**

> **说明:**
    各个 QuantId 的资金波动(最大/小的百分比).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_bands(1) limit 2;
-[ RECORD 1 ]----------------------
idxs      | 1
quantid   | 1036343299
name      | mode_adxvma
argv      | 5/13
init_cash | 200000.00298
min_pro   | 171021
max_pro   | 200000
min_diff  | -14.489501274106425
max_diff  | -1.4899999729002438e-06
-[ RECORD 2 ]----------------------
idxs      | 2
quantid   | 1036343301
name      | mode_adxvma
argv      | 15/39
init_cash | 200000.00298
min_pro   | 180968
max_pro   | 200000
min_diff  | -9.516001348211574
max_diff  | -1.4899999729002438e-06

```

---

###  8.订单交易时长比较(天)

* **quant_order_day**

> **说明:**
    各个 QuantId 的资金波动(最大/小的百分比).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_order_day(1) limit 2;
-[ RECORD 1 ]-----------
rquantid    | 1036343300
order_stock | 600048
order_day   | 7
-[ RECORD 2 ]-----------
rquantid    | 1036343299
order_stock | 600048
order_day   | 8

```

---

###  9.订单收益过程中涨跌波幅张度(%)

* **quant_order_tl**

> **说明:**
    订单收益过程中涨跌波幅张度(%).

    - 参数: version_id 

> 示例:
```
E2Q=> SELECT * FROM quant_order_tl(1) limit 2;
-[ RECORD 1 ]--------
rquantid | 1036343300
rvalue   | -5.42
rticket  | 1036343155
rvtype   | loss
-[ RECORD 2 ]--------
rquantid | 1036343300
rvalue   | -8.409
rticket  | 1036343155
rvtype   | order

```

---

###  10.策略 ID 资金的详细变化.

* **quant_profit**

> **说明:**
    订单收益过程中涨跌波幅张度(%).

    - 参数: quant_id 

> 示例:
```
E2Q=> SELECT * FROM quant_profit(1036343292) limit 2;
-[ RECORD 1 ]-------------------
qid        | 1036343292
margin     | 9140
rticket    | 243
profits    | 0
pday       | 2024-01-12 00:00:00
pside      | 1
profit_x   | 200000
profit_sum | 200000
-[ RECORD 2 ]-------------------
qid        | 1036343292
margin     | 0
rticket    | 272
profits    | 8000
pday       | 2024-04-16 00:00:00
pside      | 2
profit_x   | -1140
profit_sum | 198860

```


---

###  11.资金的详细变化.

* **quant_profit**

> **说明:**
    资金的详细变化.

    - 参数: verid 
    - 参数: offset 

> 示例:
```
E2Q=>  SELECT * FROM quant_profit_one_verid(1,0) limit 2;
-[ RECORD 1 ]--------------------
vday        | 2022-07-27 00:00:00
vprofit_sum | 200000
stat        | 开仓
pqid        | 1036343299
qverid      | 1
-[ RECORD 2 ]--------------------
vday        | 2022-08-04 00:00:00
vprofit_sum | 180344
stat        | 平仓
pqid        | 1036343299
qverid      | 1


```

###  12.个股及指数每天回报(%)

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

###  13.返回个股月回报

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

###  14.返回个股月累计回报

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

###  15.返回个股日线周期累计回报收益

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
###  16.各个 QuantId 止赢止损平仓(数量)比

* **quant_take_loss**

> **说明:**
    各个 QuantId 止赢止损平仓(数量)比.
    
    - 参数: verid 

> 示例:
```
E2Q=> SELECT * from quant_take_loss(1) limit 2;
-[ RECORD 1 ]-------
type    | 100
stat    | 策略平仓
quantid | 1036343281
number  | 3
-[ RECORD 2 ]-------
type    | 100
stat    | 策略平仓
quantid | 1036343282
number  | 7

```

---

## 数据可以采用 BI 分析

### metabase 数据分析


来源: [metabase](https://github.com/metabase/metabase)

![Metabase](./images/metabase.png "Metabase")