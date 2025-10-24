# E2L 语言 API

## 账户信息

* **FAccountBalance**
> 返回账号可用金额.

> *函数原型*
```
FAccountBalance();
```
> *说明*

- 参数：无.
- 返回值: 当前 fix session 的账户金额

> 示例:
```
  balance = FAccountBalance();
  echo(balance);
```

---

* **FAccountMargin**
> 返回账户的当前订单的保证金.

> *函数原型*
```
FAccountMargin();
```
> *说明*

- 参数：无.
- 返回值: 当前 fix session 的当前订单的保证金

> 示例:
```
  margin = FAccountMargin();
  echo(margin);
```
---

* **FAccountProfit**
> 返回账户的收益值.

> *函数原型*
```
FAccountProfit();
```
> *说明*

- 参数：无.
- 返回值: 当前 fix session 的当前订单的收益值

> 示例:
```
  profit = FAccountProfit();
  echo(profit);
```
---

* **FAccountEquity**
> 返回账户的资产净值.

> *函数原型*
```
FAccountEquity();
```
> *说明*

- 参数：无.
- 返回值: 当前 fix session 的当前资产净值

> 示例:
```
  equity = FAccountEquity();
  echo(equity);
```

---

* **FThreadPosition**
> 返回账户通过多线程分配仓位测试策略.

> *函数原型*
```
FThreadPosition(thread_number, postion);
```
> *说明*

- 参数：thread_number 数值.
    - postion 仓位数值
- 返回值: 当前 fix session 的当前资产净值

> 示例:
```
  # 设置各个线的仓位
##  平分不同的线程
    all = 100;
    # 线程数量
    thread_num = 3;
    pos =  all / thread_num;
   # 取整数 fpos = 33.0;
    fpos = FFloor(pos);
    
    for (a=0;a<thread_num;a++) {
        FThreadPosition(a, fpos);
    }

```

---

## 初始化函数

* **FIsInit**
> 返回 状态结果: UInitOk 结构.

> *函数原型*
```
FIsInit();
```
> *说明*

- 参数：无.
- 返回值: 返回结构 UInitOk 代表当前的状态

> 结构体 **UInitOk**
```
union UInitOk {
    # 策略可以进入分析
    I_OK = 1;

    # 策略初始化中
    I_Proc = 0; 
}

```

> 示例:
```
    isInit = FIsInit();
    if (isInit == UInitOk.I_Proc) {
        config();     
    } 
```

---

* **FFix**
> 设置 fix 配置.

> *函数原型*
```
FFix(path);
```
> *说明*

- 参数：path 配置路径.
- 返回值: 无.

> 示例:

```
    scfg="/opt/e2q/cfg/executor.cfg";
    FFix(scfg);
```
---

* **FMkType**
> 设置 kfaka 数据格式.

> *函数原型*
```
FMkType(tick_bar);
```
> *说明*

- 参数：tick_bar UMKType 结构体.
- 返回值: 无.

> 结构体 **UMKType**
```
union UMKType {
    Mk_Csv = 0;
    Mk_Kafka = 1;
    Mk_Tick = 2;
    Mk_Bar = 3;
}
```

> 示例:

```
    FMkType(UMKType.Mk_Bar);  
```
---

* **FMkkf**
> 设置 kfaka brokers.

> *函数原型*
```
FMkkf(source);
```
> *说明*

- 参数：source brokers 的字符串.
- 返回值: 无.

> 示例:

```
    sbroker="kafkaserver:9092";
    FMkkf(_sbroker);
```
---

* **FTopicTick**
> 设置 kfaka topic 主要是报价使用.

> *函数原型*
```
FTopicTick(topic);
```
> *说明*

- 参数：topic 字符串.
- 返回值: 无.

> 示例:

```
    topic="fix-events";
    FTopicTick(topic);
```
---

* **FTopicLog**
> 设置 kfaka topic 主要是 LOG 使用.

> *函数原型*
```
FTopicLog(topic);
```
> *说明*

- 参数：topic 字符串.
- 返回值: 无.

> 示例:

```
    topic="e2l-log";
    FTopicLog(topic);
```
---

* **FTFrame**
> 设置报价周期，可以设置多个，但不能低过 kafka 过来价格的周期.
> 比如 kafka 的报价如果是 5 分钟的话，就不能设置 1 分钟的周期，只能大于等于 5 分钟周期
> 在初始化的时候使用  FIsInit 状态等于 UInitOk.I_Proc

> *函数原型*
```
FTFrame(frame);
```
> *说明*

- 参数：frame 是 UTimeFrames 结构体.
- 返回值: 无.

> 结构体 **UTimeFrames**
```
union UTimeFrames {
    Period_Current = 0;
    Period_D1 = 1440;
    Period_H1 = 60;
    Period_H4 = 240;
    Period_M = 5;
    Period_M1 = 1;
    Period_M15 = 15;
    Period_M30 = 30;
    Period_MN1 = 43200;
    Period_W1 = 10080;
}
```

> 示例:

```
    # FIsInit 状态等于 UInitOk.I_Proc
    tframe =  UTimeFrames.Period_D1 ; 
    FTFrame(tframe);

    # FIsInit 状态等于 UInitOk.I_OK
    code =  CfiCode() ; 

    ## 如果有多个周期，可以在这儿一起取得
    timeframe = UTimeFrames.Period_D1 ; 
    fclose = FClose(code, timeframe, 0);

    # 打印价格，精度为 3 位小数点
    deci = 3;
    FPrintDeci(fclose, deci);

```

---

* **FDefTFrame**
> 返回 当前默认的周期值

> *函数原型*
```
FDefTFrame();
```
> *说明*

- 参数：无.
- 返回值: 返回 UTimeFrames 结构体.

> 示例:

```
    tf = FDefTFrame();
```

---

* **FIndex**
> 报价是不是以 index 为准.

> *函数原型*
```
FIndex();
```
> *说明*

- 参数：无.
- 返回值: 返回 UOffers 结构体.

> 结构体 **UOffers**
```
union UOffers {
    # 对齐 index 指数报价时间
    OF_Index = 0;
    # 每一笔 ticket 都报价一次
    OF_Tick = 1;
}
```

> 示例:

```
   isIndex = FIndex();
```

---

* **FOffers**
> 报价对齐方式.

> *函数原型*
```
FOffers(tick);
```
> *说明*

- 参数： tick 是 UOffers 结构体.
- 返回值:无.

> 示例:

```
   FOffers(UOffers.OF_Index);
```
---

* **FOfferTime**
> 设置每笔报价时间间隔, 不低于 50 豪秒.

> *函数原型*
```
FOfferTime(time);
```
> *说明*

- 参数： time 整数.
- 返回值:无.

> 示例:

```
    time = 100;
    FOfferTime(time);
```
---

* **FCommission**
> 设置佣金 及 币种.

> *函数原型*
```
FCommission(cms, ccy);
```
> *说明*

- 参数： cms 数值,  ccy字符串.
- 返回值:无.

> 示例:

```
    # 0.01
    _ncms = 100;
    _sccy="rmb";
    
    FCommission(_ncms, hccy);
```

---

* **FQuantId**
> 设置策略ID.

> *函数原型*
```
FQuantId(qid);
```
> *说明*

- 参数： qid 自定义的一个数值.
- 返回值:无.

> 示例:

```
    qid = 102;
    FQuantId(qid);
```
---

* **FGenerateQuantId**
> 自动生成策略ID.

> *函数原型*
```
FGenerateQuantId();
```
> *说明*

- 参数： 无.
- 返回值: 无.

> 示例:

```
    FGenerateQuantId();
```
---

* **FCurrentQuantId**
> 返回当前策略ID.

> *函数原型*
```
FCurrentQuantId();
```
> *说明*

- 参数： 无.
- 返回值: 策略ID 数值.

> 示例:

```
  qid=  FCurrentQuantId();
```

---

* **FQuantVersion**
> 设置当前策略版本号.

> *函数原型*
```
FQuantVersion(version);
```
> *说明*

- 参数： version 字符串.
- 返回值: 无.

> 示例:

```
  version = "1.3.12";
  FQuantVersion(version);
```

---

* **FVersionId**
> 返回当前策略版本号id.

> *函数原型*
```
FVersionId();
```
> *说明*

- 参数： 无.
- 返回值: ID 数值.

> 示例:

```

  vid = FVersionId();
```

---

* **FTradeTime**
> 设置交易时间.

> *函数原型*
```
FTradeTime(open_hour,open_minute,close_hour,close_minute);
```
> *说明*

- 参数： open_hour,open_minute,close_hour,close_minute 数值
- 返回值: 无.

> 示例:

```
    #----------
    # sz sh
    #  '上午 09:30——11:30'
    #  '下午 13:00——15:00'
    #
    #----------

    FTradeTime(9,30,11,30);
    FTradeTime(13,0,15,0);

    # BTC
    #  24 小时 交易的多周期时间
    #
    FTradeTime(0,0,23,59);  
```
---

* **FTradeMode**
> 设置交易模式.

> *函数原型*
```
FTradeMode(mode);
```
> *说明*

- 参数： mode 是 USymbolTradeMode 结构体
- 返回值: 无.

> 结构体 **USymbolTradeMode**
```
union USymbolTradeMode {
   #该符号的交易已禁用
    M_Disabled = 0;
    #仅允许多头仓位
    M_LongOnly = 1;
    #仅允许空头仓位
    M_ShortOnly = 2;
    #仅允许平仓操作
    M_CloseOnly = 3;
    #无贸易限制
    M_Full = 4;
}
```

> 示例:

```
    #--------------------
    #   做多做空
    #--------------------

    FTradeMode(USymbolTradeMode.M_LongOnly);
```

---

* **FGmt**
> 设置交易时间为 GMT.

> *函数原型*
```
FGmt();
```
> *说明*

- 参数： 无.
- 返回值: 无.

> 示例:

```
   FGmt();
```

---

* **FLotAndShare**
> 设置每一手是多少份股票

> *函数原型*
```
FLotAndShare(lot);
```
> *说明*

- 参数： lot 数值.
- 返回值: 无.

> 示例:

```
    # https://api.huobi.pro/v1/settings/common/market-symbols?symbols=btcusdt
    # "minoa": 0.0001
    # 0.0001;    
    lot = 1;
    FLotAndShare(lot);
```
---

* **FCurrentLS**
> 返回每一手是多少份股票

> *函数原型*
```
FCurrentLS();
```
> *说明*

- 参数： 无.
- 返回值: 返回 lot 数值.

> 示例:

```
   lot = FLotAndShare(lot);
```

---

* **FWhois**
> 返回当前是哪一个属性

> *函数原型*
```
FWhois();
```
> *说明*

- 参数： 无.
- 返回值: 返回 UOMSRisk 结构体.

> 结构体 **USymbolTradeMode**
```
union UOMSRisk{ 
    # OMS 是柜台撮合中心
    I_OMS = 0;    
    # 代理商
    I_BROKER = 1;
    # 策略者
    I_EA = 2;
} 
```

> 示例:

```
   me = FWhois();
    if(me == UOMSRisk.I_OMS){
        echo(me);
    }
```

## 交易函数

---

* **FOrderTicket**
> 返回 FOrderSelect 选择的订单号

> *函数原型*
```
FOrderTicket();
```
> *说明*

- 参数： 无.
- 返回值: 返回 订单号 数值.

> 示例:

```
    ticket = FOrderTicket();
    echo(ticket); 
```
---

* **FOrderSelect**
> 该功能选择一个订单进行进一步处理。

> *函数原型*
```
FOrderSelect(index,select,pool);
```
> *说明*

- 参数： index 是 索引 或 ticket 值,订单索引或订单票取决于第二个参数。
- 参数: select,pool 是 USelectFlag 结构体.
    - select: 选择标志。它可以是下列任意值：
                SELECT_BY_POS - 订单池中的索引，
                SE​​LECT_BY_TICKET - 索引为订单号。
    - pool: MODE_TRADES  MODE_HISTORY
- 返回值: 返回 UBool 结构体.

> 结构体 **USelectFlag**
```
union USelectFlag {
    F_ByPos = 0;
    F_ByTicket = 1;
    P_History = 3;
    P_Trade = 2;
}
```

> 结构体 **UBool**
```
union UBool {
    B_FALSE = 1;
    B_TRUE = 0;
}
```

> 示例:

```
    trade_num = 3;
    for(i=0; i<trade_num; i++){

        b = FOrderSelect(i, USelectFlag.F_ByPos, USelectFlag.P_Trade);      

        if(b==UBool.B_TRUE){
            ticket = FOrderTicket();
            echo(ticket);
        }        
    }

```
---

* **FOrderComment**
> 记录当前一笔订单  

> *函数原型*
```
FOrderComment(ticket,side, cmt);
```
> *说明*

- 参数： ticket 订单号 数值.
    - side: 是 USide 结构体.
    - cmt: 是 UOrderEvent 结构体.
- 返回值: 无.

> 结构体 **USide**
```
union USide {
    Os_Buy = 1;
    Os_Sell = 2;
}
```

> 结构体 **UOrderEvent**
```
union UOrderEvent {
    Oe_Compleate = 0;
    Oe_StopLoss = 1;
    Oe_TakeProfit =2;
};

```

> 示例:

```
    ticket = 100;
    flag = UOrderEvent.Oe_Compleate;
    side = USide.Os_Sell; 
    FOrderComment(ticket, side, flag);
```
---

* **FOrderClose**
> 关闭已开立的订单。

> *函数原型*
```
FOrderClose(ticket,lot,stoppx,slippage);
```
> *说明*

- 参数： ticket 订单票的唯一编号.
    - lot 手数.
    - stoppx 价格.
    - slippage 最大价格滑点的点值.
- 返回值: 返回 UBool 结构体.

> 示例:

```
 
    FOrderClose(ticket,lot,stoppx,slippage);
```
---

* **FOrderSend**
> 主要功能用于开市或下挂单.

> *函数原型*
```
FOrderSend(cficode,side,qty,price,slippage,ordtype);
```
> *说明*

- 参数： cficode 交易品种 cfi .
    - side: 是 USide 结构体.
    - qty 手数.
    - price 价格.
    - slippage 最大价格滑点的点值.
    - ordtype 是 OrdType 结构体
- 返回值: 返回 UBool 结构体.

> 结构体 **OrdType**
```
union UOrdType {
    Ot_Limit = 2;
    Ot_Market = 1;
    Ot_Stop = 3;
    Ot_Stop_limit = 4;
}

```

> 示例:

```
    code =  CfiCode( ) ;
    # long or short
    cmd = USide.Os_Buy;
    qty = 100;
    timeframe = UTimeFrames.Period_D1 ; 
    price = FClose(code, timeframe, 0);
    slippage = 0;
    ot =  UOrdType.Ot_Market; 

    FOrderSend(code , cmd, qty, price,  slippage, ot);

```

---

* **FOrdersTotal**
> 返回未平仓的订单数量

> *函数原型*
```
FOrdersTotal();
```
> *说明*

- 参数： 无.
- 返回值: 返回 订单 数值.

> 示例:

```
    trade_num = FOrdersTotal(); 
```
---

* **FOrdersHistoryTotal**
> 返回历史订单数量

> *函数原型*
```
FOrdersHistoryTotal();
```
> *说明*

- 参数： 无.
- 返回值: 返回 订单 数值.

> 示例:

```
    trade_num = FOrdersHistoryTotal(); 
```
---

* **FOrderLots**
> 返回当前订单交易手数量

> *函数原型*
```
FOrderLots(ticket);
```
> *说明*

- 参数： ticket 订单号.
- 返回值: 返回 订单手数 数值.

> 示例:

```
    lots = FOrderLots(ticket);
```
---

* **FOrderOpenPrice**
> 返回当前订单交易成交价

> *函数原型*
```
FOrderOpenPrice(ticket,bool);
```
> *说明*

- 参数： ticket 订单号.
    - bool 是 e2::Bool 结构体，UBool.B_FALSE 现价 UBool.B_TURE 复权价
- 返回值: 返回 订单 成交价.

> 示例:

```
    b = UBool.B_FALSE;
    price = FOrderOpenPrice(ticket, b);
```

## 分析仪

---

* **FAnalysis**
> 返回当前订单交易成交价

> *函数原型*
```
FAnalysis(qid, analy);
```
> *说明*

- 参数： qid 策略 id.
    - analy 是 UAnaly 结构体
- 返回值: 返回 数值.

> 结构体 **UAnaly**
```
union UAnaly {
    AvgDrawdown = 16;
    AvgDrawdownDuration = 18;
    AvgTrade = 22;
    AvgTradeDuration = 24;
    BestTrade = 20;
    BuyAndHold = 9;
    CalmarRatio = 14;
    Cash = 1;
    Curve = 29;
    Duration = 4;
    End = 3;
    EquityFinal = 6;
    EquityPeak = 7;
    Expectancy = 26;
    ExposureTime = 5;
    Id = 0;
    KellyCriterion = 28;
    MaxDrawdown = 15;
    MaxDrawdownDuration = 17;
    MaxTradeDuration = 23;
    ProfitFactor = 25;
    Return = 8;
    ReturnAnn = 10;
    SQN = 27;
    SharpeRatio = 12;
    SortinoRatio = 13;
    Start = 2;
    VolatilityAnn = 11;
    WinRate = 19;
    WorstTrade = 21;
}
```

> 示例:

```
    # 查看当前一策略的最大回撒
    nmdd = FAnalysis(qid, UAnaly.MaxDrawdown );
```

---

* **FAnalse**
> 记录数据, 和 FAnalseArgv，FAnalseDB 一起使用

> *函数原型*
```
FAnalse(id, name);
```
> *说明*

- 参数： id 自定义的数值.
    - name 是 字符串
- 返回值: 无.

> 示例:

```
    ha_name = "mode_ha";
    arg_id = 109;
    period = 90;
    FAnalse( arg_id, ha_name);
    FAnalseArgv(arg_id, period);
    FAnalseDB(); 

```
---

* **FAnalseArgv**
> 记录数据, 和 FAnalse，FAnalseDB 一起使用

> *函数原型*
```
FAnalseArgv(id, args);
```
> *说明*

- 参数： id 自定义的数值.
    - args 是 数值
- 返回值: 无.

> 示例:

```
   
    arg_id = 109;
    period = 90;
 
    FAnalseArgv(arg_id, period);
   
```
---

* **FAnalseDB**
> 记录数据, 和 FAnalse，FAnalseArgv 一起使用

> *函数原型*
```
FAnalseDB();
```
> *说明*

- 参数： 无.
- 返回值: 无.

> 示例:

```
    FAnalseDB();
```
---

* **FAnalseLog**
> 记录数据

> *函数原型*
```
FAnalseLog(key, value, idx,time);
```
> *说明*

- 参数： key 自定义数值
    - value 数值
    - idx 自定义数值
    - time 时间
- 返回值: 无.

> 示例:

```
    key = 192;
    code = CfiCode( ) ;
    timeframe =  CurrentTFrame() ;
    FBar(code , timeframe, 0); 
    time = FBarSeries(UBarType.MODE_TIME);

    pid = FProcessId();
    run = FProcessRuns();

    pids = (pid + 1) * 100;

    runs = (run + 1) * 10;

    id = alt_id +  pids + runs + thread_id;

    FAnalseLog(key, value, id , time);

```

## 经纪商

---

* **FSetCash**
> 设置现金

> *函数原型*
```
FSetCash(cash);
```
> *说明*

- 参数： cash 数值.
- 返回值: 无.

> 示例:

```
   # 100 万，最少值 1 万
    cash = 100;
    FSetCash(cash);  
```
---

* **FGetCash**
> 查询当前现金

> *函数原型*
```
FGetCash( );
```
> *说明*

- 参数： 无.
- 返回值: 返回 cash 数值.

> 示例:

```
    cash = FGetCash();
  
```

---

* **FSettlInst**
> 设置交易模式

> *函数原型*
```
FSettlInst( mode );
```
> *说明*

- 参数： mode 是 USettleInstMode 结构体
- 返回值: 无.

> 结构体 **USettleInstMode**
```
union USettleInstMode {
    S_Settle = 0;
    S_Settle_Balance = 1;
    S_Observer = 2;
};  
```

> 示例:

```
    # 设置成观察者模式
#    FSettlInst(USettleInstMode.S_Observer);
# 结算模式，但余额不再增加
    FSettlInst(USettleInstMode.S_Settle);
  
```

---

* **FSettlInst**
> 设置 撮合模式

> *函数原型*
```
FSettlInst( mode );
```
> *说明*

- 参数： mode 是 UTimeInForce 结构体
- 返回值: 无.

> 结构体 **UTimeInForce**
```
union UTimeInForce {
    Tif_At_The_Close = 7;
    Tif_At_The_Opening = 2;
    Tif_Day = 0;
    Tif_Fill_Or_Kill = 4;
    Tif_Good_Till_Cancel = 1;
    Tif_Good_Till_Crossing = 5;
    Tif_Good_Till_Date = 6;
    Tif_Immediate_Or_Cancel = 3;
}
```

> 示例:

```
#default day
# ioc 
    FTimeInForce(UTimeInForce.Tif_Immediate_Or_Cancel); 
  
```

---

* **FMatchEventInit**
> 设置 撮合 时间点

> *函数原型*
```
FMatchEventInit( mode );
```
> *说明*

- 参数： mode 是 UMatchEvent 结构体
- 返回值: 无.

> 结构体 **UMatchEvent**
```
union UMatchEvent {
    ME_OrderIn = 0;
    ME_Open = 1;
    ME_Close = 2;
}
```

> 示例:

```
    FMatchEventInit(UMatchEvent.ME_Close);    
#    FMatchEventInit(UMatchEvent.ME_OrderIn);
  
```

---

* **FExDivPrice**
> 查询 复权数据

> *函数原型*
```
FExDivPrice( cfi_code );
```
> *说明*

- 参数： cfi_code 数值
- 返回值: 价格 数值

> 示例:

```
 
  
```


---

* **FExDividendSize**
> 查询 复权数据

> *函数原型*
```
FExDividendSize( cfi_code );
```
> *说明*

- 参数： cfi_code 数值
- 返回值:   数值

> 示例:

```
 
  
```

---

* **FExDividendDate**
> 查询 复权数据

> *函数原型*
```
FExDividendDate( cfi_code, idx );
```
> *说明*

- 参数： cfi_code 数值
    - idx 数值，第几个值
- 返回值:   数值

> 示例:

```
```

---

* **FExDividendCash**
> 查询 复权数据

> *函数原型*
```
FExDividendCash( cfi_code, idx );
```
> *说明*

- 参数： cfi_code 数值
    - idx 数值，第几个值
- 返回值:   数值

> 示例:

```
```
---

* **FExDividendShare**
> 查询 复权数据

> *函数原型*
```
FExDividendShare( cfi_code, idx );
```
> *说明*

- 参数： cfi_code 数值
    - idx 数值，第几个值
- 返回值:   数值

> 示例:

```
    cfi_code = 1111; 
    hprice = FExDivPrice(cfi_code);
    if ( hprice == 0 ) {
        return 0;
    }
    size = FExDividendSize(cfi_code);
    if (size == 0) {

        return 0;
    }
    idx_init = size - 1;
    all_share = 0;

    for (idx = idx_init;idx>=0;idx--) {

        cash = FExDividendCash(cfi_code,idx);
        share = FExDividendShare(cfi_code,idx);

        if(share > 0){
            hprice += share * hprice + cash;
        }else{
            hprice += cash;
        }
    }

```
---

* **FBrokerBook**
> 设置 AB book, 本地回测策略，还是把订单抛给上游，这个可以进行现实的交易

> *函数原型*
```
FBrokerBook( booktype );
```
> *说明*

- 参数： booktype 是 UBookType 结构体
- 返回值:  无.

> 结构体 **UBookType**
```
union UBookType {
    ABook = 0;
    BBook = 1;

}
```

> 示例:

```
 FBrokerBook(UBookType.ABook);  
```

## 普通函数
---

* **FIsDebug**
> 打印 数据 

> *函数原型*
```
FIsDebug( bool );
```
> *说明*

- 参数： bool 是 e2::Bool 结构体
- 返回值: 无.

> 示例:

```
    # 正常打印
    FIsDebug(UBool.B_TRUE);
```

---

* **log**
> 打印 数据 

> *函数原型*
```
log( value );
```
> *说明*

- 参数： value 数值
- 返回值: 无.

> 示例:

```
    value = 102;
    log(value);
```

---

* **FPrintLine**
> 打印 数据 

> *函数原型*
```
FPrintLine( line );
```
> *说明*

- 参数： line 是 ULineState 结构体 
- 返回值: 无.

> 结构体 **ULineState**
```
union ULineState {
    L_Dotted = 0;
    L_Solid = 1;
}
```

> 示例:

```
    line = ULineState.L_Dotted;
    FPrintLine( line );
```

---

* **FPrintDeci**
> 打印 数据 ,带有精度

> *函数原型*
```
FPrintDeci( value,deci );
```
> *说明*

- 参数： value 数值
    - deci 精度
- 返回值: 无.

> 示例:

```
    code =  CfiCode() ; 
    timeframe =  CurrentTFrame() ;

    now_close = FClose(code , timeframe, 0);
    
    # 3 位小数
    deci = 3;
    FPrintDeci(now_close,deci);

```
---

* **FPrintTime**
> 输入时间戳，打印有时间格式的字符串

> *函数原型*
```
FPrintTime( unix_time);
```
> *说明*

- 参数： unix_time 数值, 时间戳
- 返回值: 无.

> 示例:

```
    code =  CfiCode() ; 
    tframe =  CurrentTFrame() ;

    time = FTime(code , tframe, 0);
    
    FPrintTime(time);
```

---

* **FStoreId**
> 自动获取一次性的缓存 ID

> *函数原型*
```
FStoreId( );
```
> *说明*

- 参数： 无.
- 返回值:  ID 数值

> 示例:

```     
```

---

* **FisStore**
> 检查 id 是不是初始化或者是不是存在

> *函数原型*
```
FisStore( id );
```
> *说明*

- 参数： ID 数值.
- 返回值:  返回 UBool 结构体.

> 示例:

```     
```

---

* **FFetch**
> 获取缓存数据

> *函数原型*
```
FFetch( id );
```
> *说明*

- 参数： ID 数值, FStoreId 生成的 ID 或者 自定义的 ID
- 返回值:  缓存中的值

> 示例:

```  
```

---

* **FStore**
> 获取缓存数据

> *函数原型*
```
FStore( id , value);
```
> *说明*

- 参数： ID 数值, FStoreId 生成的 ID 或者 自定义的 ID
    - value 数值，需要保存的数据
- 返回值:  无.

> 示例:

```
    id = FStoreId();

    value = FFetch(id);
    
    value += 1;
    
    FStore(id, value);

```
---

* **FTicketSize**
> 返回已运行的 ticket 次数

> *函数原型*
```
FTicketSize( );
```
> *说明*

- 参数： 无.
- 返回值:   数值

> 示例:

```
    number = FTicketSize( );

```

## 数学函数

---

* **FArray**
> 创建一个内存数组

> *函数原型*
```
FArray( id, size );
```
> *说明*

- 参数： ID 数值, FStoreId 生成的 ID 或者 自定义的 ID
    - size 数值，数组大小
- 返回值:  无.

> 示例:

```
    
```

---

* **FArrayFixed**
> 固定数组的大小

> *函数原型*
```
FArrayFixed( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  无.

> 示例:

```
    
```

---

* **FArrayShare**
> 多个进程之间共享数组内容

> *函数原型*
```
FArrayShare( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  无.

> 示例:

```
    
```

---

* **FArrayAdd**
> 往数组添加数值, 最新的值添加在最后一个索引中

> *函数原型*
```
FArrayAdd( id , value);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
    - value 数值
- 返回值:  返回 UBool 结构体.

> 示例:

```
    func test(var){
        id = FStoreId();
        ## 数组大小
        size = 2;
        FArray(id, size);
        
        ret = 0;
        if(var > 5){
            ret = 1;
        }

        var += 2;

        FArrayAdd( id , ret); # 数组 index = 0
        FArrayAdd( id , var); # 数组 index = 1

        return id;
    }

    tid = test(2);

    ret = FArrayGet(tid, 0);
    if(ret == 0){
        echo(ret)
    }else{
        val = FArrayGet(tid, 1);
        echo(val);
    }
    
```

---

* **FArrayUpdate**
> 更新某一个索引的数值，但需要这个索引存在

> *函数原型*
```
FArrayUpdate( id， index, value);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
    - index, 数组是索引
    - value 数值
- 返回值:  返回 UBool 结构体.

> 示例:

```
```

---

* **FArrayGet**
> 获取数组某一个索引的数值, 最后一个 idx 索引是最新的值

> *函数原型*
```
FArrayGet( id， index);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
    - index, 数组是索引
- 返回值:  数值.

> 示例:

```
id = FStoreId();
FArray(id, 5);
FArrayAdd(id, tick_size);
idx = FArraySize(id);
idx = idx - 1;
echo(idx);
val = FArrayGet(id, idx);
echo(val);


```

---

* **FArrayFill**
> 完整填充某个数组

> *函数原型*
```
FArrayFill( id， value);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
    - value 数值.
- 返回值:  无.

> 示例:

```
```

---

* **FArrayLast**
> 取数组中的最后一个值

> *函数原型*
```
FArrayLast( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  value 数值.

> 示例:

```
```

---

* **FArrayLength**
> 取数组动态的长度

> *函数原型*
```
FArrayLength( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  value 数值.

> 示例:

```
```
---

* **FArraySize**
> 取数组当前的长度, 即现在添加了多少个值

> *函数原型*
```
FArraySize( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  value 数值.

> 示例:

```
```

---

* **FArrayMax**
> 取数组中最大的值

> *函数原型*
```
FArrayMax( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  value 数值.

> 示例:

```
```
---

* **FArrayMin**
> 取数组中最小的值

> *函数原型*
```
FArrayMin( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  value 数值.

> 示例:

```
```

---

* **FArraySum**
> 取数组中所有值的和

> *函数原型*
```
FArraySum( id);
```
> *说明*

- 参数： ID 数值, 和 FArray 同一个值
- 返回值:  value 数值.

> 示例:

```
```
---

* **FMax**
> 返回最大的值

> *函数原型*
```
FArraySum(value1, value2);
```
> *说明*

- 参数： value1 数值.
    - value2 数值.
- 返回值:  value 数值.

> 示例:

```
```

---

* **FMin**
> 返回最小的值

> *函数原型*
```
FArraySum(value1, value2);
```
> *说明*

- 参数： value1 数值.
    - value2 数值.
- 返回值:  value 数值.

> 示例:

```
```
---

* **FMaxs**
> 返回最大的值

> *函数原型*
```
FMaxs(value1, value2, value3);
```
> *说明*

- 参数： value1 数值.
    - value2 数值.
    - value3 数值.
- 返回值:  value 数值.

> 示例:

```
```
---

* **FMins**
> 返回最小的值

> *函数原型*
```
FMins(value1, value2, value3);
```
> *说明*

- 参数： value1 数值.
    - value2 数值.
    - value3 数值.
- 返回值:  value 数值.

> 示例:

```
```

---

* **FBetween**
> 返回 val 是不是在 start 至 end 中的值

> *函数原型*
```
FBetween(value, start, end);
```
> *说明*

- 参数： value 数值.
    - start 数值.
    - end 数值.
- 返回值: 返回 UBool 结构体.

> 示例:

```
```

---

* **FSqrt**
> 返回 value 的 sqrt

> *函数原型*
```
FSqrt(value );
```
> *说明*

- 参数： value 数值.
- 返回值: 数值.

> 示例:

```
```
---

* **FStdev**
> 返回 value 的 stdev

> *函数原型*
```
FStdev(value );
```
> *说明*

- 参数： value 数值.
- 返回值: 数值.

> 示例:

```
```

---

* **FArrayStdev**
> 返回 对数组进行  的 stdev

> *函数原型*
```
FArrayStdev(array_id );
```
> *说明*

- 参数： array_id 数值.
- 返回值: 数值.

> 示例:

```
```

---

* **FAbs**
> 返回 value 的 abs

> *函数原型*
```
FAbs(value );
```
> *说明*

- 参数： value 数值.
- 返回值: 数值.

> 示例:

```
```
---

* **FCeil**
> 返回 value 的 ceil

> *函数原型*
```
FCeil(value );
```
> *说明*

- 参数： value 数值.
- 返回值: 数值.

> 示例:

```
```
---

* **FFloor**
> 返回 value 的 floor

> *函数原型*
```
FFloor(value );
```
> *说明*

- 参数： value 数值.
- 返回值: 数值.

> 示例:

```
```

## 市场信息

---

* **FCFICode**
>  通过 idx 提取 cficode

> *函数原型*
```
FCFICode( idx );
```
> *说明*

- 参数： idx 数值.
- 返回值: cfi code  数值..

> 示例:

```
index = 0;
cfi_base = FCFICode(index); 

```

---

* **FSymbols**
> 返回目前有多少个交易品种

> *函数原型*
```
FSymbols(  );
```
> *说明*

- 参数： 无.
- 返回值: 数值.

> 示例:

```
```
---

* **FSymbols**
> 返回目前有多少个交易品种

> *函数原型*
```
FSymbols(  );
```
> *说明*

- 参数： 无.
- 返回值: 数值.

> 示例:

```
```

---

* **FSymbol**
> 初始化获取交易品种

> *函数原型*
```
FSymbol( cfi_code );
```
> *说明*

- 参数： cfi_id 数值.
- 返回值: 无.

> 示例:

```
```
---

* **FSettlement**
> 设置交易模式，默认 T + 1

> *函数原型*
```
FSettlement( val );
```
> *说明*

- 参数： val 数值.
- 返回值: 无.

> 示例:

```
    # T + 0
    FSettlement(0);
```

---

* **FOpen**
> 获取开盘价

> *函数原型*
```
FOpen(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FHigh**
> 获取最高价

> *函数原型*
```
FHigh(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FLow**
> 获取最低价

> *函数原型*
```
FLow(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FClose**
> 获取收盘价

> *函数原型*
```
FClose(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FVolume**
> 获取交易量

> *函数原型*
```
FVolume(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FTime**
> 获取当前时间

> *函数原型*
```
FTime(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FAdjClose**
> 获取 adj 报价

> *函数原型*
```
FAdjClose(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```

---

* **FOnOpen**
> 如果源数据是 on close 就得开启，系统计算周期是以开盘时间为准

> *函数原型*
```
FOnOpen();
```
> *说明*

- 参数：  无.
- 返回值: 无.

> 示例:

```
   
```

---

* **FBarSize**
> 获取当前周期已经有多少bar

> *函数原型*
```
FBarSize(cfi_id, timeframe);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
- 返回值: 数值.

> 示例:

```
   
```

---

* **FBarNumber**
> 获取当前周期整体能保存多长根柱子

> *函数原型*
```
FBarNumber(cfi_id, timeframe);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
- 返回值: 数值.

> 示例:

```
   
```

---

* **FBar**
> 获取 bar, 配合 FBarSeries 使用

> *函数原型*
```
FBar(cfi_id, timeframe, shift);
```
> *说明*

- 参数： cfi_id 数值.
    - timeframe 周期
    - shift 位移数值
- 返回值: 数值.

> 示例:

```
   
```
---

* **FBarSeries**
> 取已经 FBar 的数值

> *函数原型*
```
FBarSeries(bartype);
```
> *说明*

- 参数：bartype 是 UBarType 结构体.
- 返回值: 数值.


> 结构体 **UBarType**
```
union UBarType {
    MODE_CLOSE = 4;
    MODE_HIGH = 2;
    MODE_LOW = 3;
    MODE_OPEN = 1;
    MODE_TIME = 0;
    MODE_VOL = 5;
}
```

> 示例:

```
    index=1;
    code =  CfiCode( ) ;
    FBar(code , UTimeFrames.Period_H1, index);

    open = FBarSeries(UBarType.MODE_OPEN);
    high = FBarSeries(UBarType.MODE_HIGH);
    low = FBarSeries(UBarType.MODE_LOW);
    close = FBarSeries(UBarType.MODE_CLOSE);

    echo(open);
    echo(high);
    echo(low);
    echo(close);

```

---

* **FExdiDate**
> 获取复权时间

> *函数原型*
```
FExdiDate(cfi_id);
```
> *说明*

- 参数： cfi_id 数值.
- 返回值: 数值.

> 示例:

```
   
```
---

* **FExdiCash**
> 获取复权分红金额

> *函数原型*
```
FExdiCash(cfi_id);
```
> *说明*

- 参数： cfi_id 数值.
- 返回值: 数值.

> 示例:

```
   
```

---

* **FExdiShare**
> 获取复权分红手数

> *函数原型*
```
FExdiShare(cfi_id);
```
> *说明*

- 参数： cfi_id 数值.
- 返回值: 数值.

> 示例:

```
   
```

---

* **FCustomDataSize**
> 获取自定义数据的数组大小

> *函数原型*
```
FCustomDataSize(cfi_id, index);
```
> *说明*

- 参数： cfi_id 数值.
    - index 数值.
- 返回值: 数值.

> 示例:

```
    index = 0;
    size = FCustomDataSize(cficode, index);
    echo(size);
```

---

* **FCustomDataGet**
> 获取自定义数据的数组中 postion 的数值

> *函数原型*
```
FCustomDataGet(cfi_id, index, postion);
```
> *说明*

- 参数： cfi_id 数值.
    - index 数值.
    - postion 数值.
- 返回值: 数值.

> 示例:

```
    index = 0;
    postion = 1;
    value = FCustomDataGet(cficode, index, postion);
    echo(value);
```

---

* **FSymbolLockForEA**
> 如果有多个 cficode 的时候， 可以限制一个进程(账号),单独获取其中一个 cficode

> *函数原型*
```
FSymbolLockForEA();
```
> *说明*

- 参数： 无.
- 返回值: 无.

> 示例:

```
    # 用在 oms 端
    FSymbolLockForEA();
```

---

* **FDelisting**
>  获取 某个 cficode 的退市时间

> *函数原型*
```
FDelisting(cfi_id);
```
> *说明*

- 参数： cfi_id 数值.
- 返回值: 数值.

> 示例:

```
    cfi_code = 179594;
    dtime = FDelisting(cfi_code);
```

---

* **FIsSuspended**
>  暂停交易, 作用在整体

> *函数原型*
```
FIsSuspended();
```
> *说明*

- 参数： 无 .
- 返回值: 返回 UBool 结构体.

> 示例:

```
    ## 暂定交易 
    is_suspend = FIsSuspended();

```

---

* **FCustomDataNumber**
> 获取自定义数据的更新的次数

> *函数原型*
```
FCustomDataNumber(cfi_id, index);
```
> *说明*

- 参数： cfi_id 数值.
    - index 数值.
- 返回值: 数值.

> 示例:

```
    index = 0;
    size = FCustomDataNumber(cficode, index);
    echo(size);
```

## 虚拟交易机器人

---

* **FGiveaway**
> 设置交易机器人.<br/>
> BSE: A Minimal Simulation of a Limit-Order-Book Stock Exchange [https://arxiv.org/pdf/1809.06027](https://arxiv.org/pdf/1809.06027)  

> *函数原型*
```
FGiveaway(cfi_id);
```
> *说明*

- 参数： 无.
- 返回值: 无.

> 示例:

```
   
```

## 日期和时间

---

* **FTimeCurrent**
> 返回当前报价时间.

> *函数原型*
```
FTimeCurrent( );
```
> *说明*

- 参数： 无.
- 返回值: 数值.

> 示例:

```
   
```

---

* **FTimeLocal**
> 返回当前机器时间.

> *函数原型*
```
FTimeLocal( );
```
> *说明*

- 参数： 无.
- 返回值: 数值.

> 示例:

```
   
```

---

* **FClock**
> 返回当前机器豪秒.

> *函数原型*
```
FClock( );
```
> *说明*

- 参数： 无.
- 返回值: 数值.

> 示例:

```
   
```

---

* **FYear**
> 转换时间返回: 年().

> *函数原型*
```
FYear( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   year = FYear(); # 2025
```

---

* **FMonth**
> 转换时间返回: 月 (1-12).

> *函数原型*
```
FMonth( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   month = FMonth();
```

---

* **FDay**
> 转换时间返回: 日 (1-31).

> *函数原型*
```
FDay( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   day = FDay();
```

---

* **FWeek**
> 转换时间返回: 星期值.

> *函数原型*
```
FWeek( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值(0-6, 0=周日).


> 示例:

```
   now = FTimeCurrent( );
   week = FWeek();
```

---

* **FYearWeek**
> 转换时间返回: 一年中的第几周.

> *函数原型*
```
FYearWeek( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   week = FYearWeek();
```

---

* **FHour**
> 转换时间返回: 小时.

> *函数原型*
```
FHour( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   hour = FHour();
```

---

* **FMinute**
> 转换时间返回: 分钟.

> *函数原型*
```
FMinute( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   minute = FMinute();
```

---

* **FSecond**
> 转换时间返回: 秒.

> *函数原型*
```
FSecond( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   second = FSecond();
```

---

* **Fmillisecond**
> 转换时间返回: 毫秒.

> *函数原型*
```
Fmillisecond( now );
```
> *说明*

- 参数： now 数值.
- 返回值: 数值.


> 示例:

```
   now = FTimeCurrent( );
   millisecond = Fmillisecond();
```

## 系统函数

---

* **FThread**
> 设置线程, 如果大于硬件线程，将以硬件线程为准.

> *函数原型*
```
FThread( number );
```
> *说明*

- 参数： 数值.
- 返回值: 无.

> 示例:

```
   
```

---

* **FProcessId**
> 返回当前进程 值.

> *函数原型*
```
FProcessId(  );
```
> *说明*

- 参数： 无. 
- 返回值: 数值.

> 示例:

```
   
```

---

* **FProcessRuns**
> 返回当前进程已经运行第几次， 次数来自 主进程的 -r 参数

> *函数原型*
```
FProcessRuns(  );
```
> *说明*

- 参数： 无. 
- 返回值: 数值.

> 示例:

```
   
```

---

* **FThreadId**
> 返回当前线程的 id

> *函数原型*
```
FThreadId(  );
```
> *说明*

- 参数： 无. 
- 返回值:  数值.

> 示例:

```
   
```

---

* **FTypeOf**
> 分析当前变量是函数还是变量

> *函数原型*
```
FTypeOf( name );
```
> *说明*

- 参数： 无. 
- 返回值:  返回 UBool 结构体.

> 示例:

```
   
```

---

* **FApiList**
> 列出 所有 内置API 列表

> *函数原型*
```
FApiList(  );
```
> *说明*

- 参数： 无. 
- 返回值:  无.

> 示例:

```
   
```

---

* **FSymbolUnion**
> 列出 所有 内置 结构体 列表

> *函数原型*
```
FSymbolUnion(  );
```
> *说明*

- 参数： 无. 
- 返回值:  无.

> 示例:

```
   
```