# 代码讲解

## OMS Script 代码
> 代码入口
```
#--------
# Name:function
#   Parameters:
# - arg1: xxx
# - arg2: xxx
# -> return 
# Description: 
#  
#--------	
func main_oms(arg0, arg1) {
    isInit = FIsInit();
    bid = 1;
    _ret = -1;

    if (isInit == UInitOk.I_Proc) {
        config();     
    } 

    if(isInit == UInitOk.I_OK){ 

        if (arg0 == UOMSRisk.I_OMS) {
            _ret = hfq(arg1);

        }else{
            _ret = risk();

            if( arg1 == bid){
                FGiveaway();
            }

        }
    }

    return _ret;

}
#----- func end

#-------------------
# script oms start
#-------------------

#__arg0 ask or sell
#__arg1 bid or buy

ret = main_oms(__arg0, __arg1);
```

### **main_oms**
> 函数 是所有进程的入口，其中参数: <br/>
```
__arg0, __arg1
```

> 在 oms 端

```
isInit = FIsInit();
isInit == UInitOk.I_Proc
```
> __arg1 是 1 ，没有作用

```
isInit == UInitOk.I_OK
```
> __arg1 是 ask 或者 bid 值

## HFQ 计算复权数据

### 系统内置代码

> 以下函数都是系统内置 API 函数，需要通过 kafka 发送分红及转股的数据才会有问题，详细情况请查看各个 API 函数手册
```
hprice = FExDivPrice(cfi_code);
size = FExDividendSize(cfi_code);
cash = FExDividendCash(cfi_code,idx);
share = FExDividendShare(cfi_code,idx);
```

## Kafka 设置
> 设置 kafka 的地址

```
 _sbroker="kafkaserver:9092";
FMkkf(_sbroker);
```

> 设置 接收 报价的 topic

```
_stopic="fix-events";
FTopicTick(_stopic);
```

> 设置 日志 topic

```
_ltopic="e2l-log";
FTopicLog(_ltopic);
```

> 设置当前报价是从BAR 中转换过来的

```
 FMkType(UMKType.Mk_Bar);  
```