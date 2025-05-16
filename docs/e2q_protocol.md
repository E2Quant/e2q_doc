# E2Q 协议说明

## E2Q 协议  

> 协议采用 (network byte order) 结构


### Init 初始化订阅 symbol

* 格式

| Name         | Offset | Length | Value   | Notes            |
| :------------- | -------- | -------- | --------- | --------------------------------------------------------------------------------------- |
| Message Type | 0      | 1      | 'I'     | Init Type                        |
| stock        | 1      | 10      | Alpha   | 股票名称                         |
| cficode      | 11     | 4      | Integer | cfi code                            |
| type         | 15     | 1      | Alpha | symbol type ['i':index, 't':trade]     |
| TickTime     | 16      | 4      | Integer | 每一笔报价的间隔时间, 回测的时候使用的, <br/>免得策略超过这个报价的间隔时间就有点麻烦了 |
| Aligned      | 20      | 1      | Alpha |  当前的状态 ['U': 后面还有数据, <br/>'P': 当前一个时间对齐完成]|

* C++ 类
```
struct SystemInitMessage : public BaseMessage {
    char Stock[E2QSTOCK_LENGTH] = {0};
    std::uint32_t CfiCode = 0;
    char Itype = InitType::INDEX;
    std::uint32_t OfferTime = 0;
}; /* ----------  end of struct SystemInitMessage  ---------- */

typedef struct SystemInitMessage SystemInitMessage;

```

### Exit 系统退出

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      | 'E'     | Exit process                                |

* C++ 类
```

```

### SUSPEND 暂停交易

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      | 'S'     | suspend order                                |

* C++ 类

```

```

### 除权分红

* 格式

| Name         | Offset | Length | Value   | Notes                             |
| :------------- | -------- | -------- | --------- | ------------------------- |
| Message Type | 0      | 1      | 'X'     | Exit process                   |
| cficode      | 1      | 4      | Integer |  cfi code  |
| year         | 5      | 2      | Integer |  year   |
| month        | 7      | 2      | Integer |  month  |
| day          | 9      | 2      | Integer |  day  |
| category     | 11     | 2      | Integer |  category  |
| fenhong      | 13     | 4      | Integer |  fenhong  |
| songzhuangu  | 17     | 4      | Integer |  songzhuangu  |
| outstanding  | 21     | 4      | Integer |  outstanding  |
| outstandend  | 25     | 4      | Integer |  outstandend  |
| mrketCaping  | 29     | 4      | Integer |  mrketCaping  |
| Aligned      | 33     | 1      | Alpha   |  当前的状态 ['U': 后面还有数据,<br/> 'P': 当前一个时间对齐完成]|

* C++ 类

```
struct StockAXdxrMessage : public BaseMessage {
    std::uint32_t CfiCode = 0;
    std::uint16_t year = 0;
    std::uint16_t month = 0;
    std::uint16_t day = 0;
    std::uint16_t category = 0;
    std::uint32_t fenhong = 0;
    std::uint32_t songzhuangu = 0;
    std::uint32_t outstanding = 0;
    std::uint32_t outstandend = 0;
    std::uint32_t mrketCaping = 0;
    std::uint16_t uint = 0;  // 10 送，还是 100 送
}; /* ----------  end of struct StockAXdxrMessage  ---------- */

typedef struct StockAXdxrMessage StockAXdxrMessage;

```

### TICK 行情报价

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      |   'T'           | Exit process                                |
| cficode      | 1      | 4      |   Integer       |  cfi code  |
| unix_time    | 5      | 8      |   Integer 64    | unix_time                                |
| frame        | 13      | 2      |   Integer  16   | frame                                |
| side         | 15     | 1      |   Alpha         | side [ 'B' : Buy Order, 'S' : Sell Order ]          |
| price        | 16     | 6      |   Integer 64    | price           |
| qty          | 22     | 6      |   Integer 64    | qty           |
| number       | 28     | 6      |   Integer 32   | number           |
| Aligned      | 34     | 1      | Alpha |  当前的状态 ['U': 后面还有数据,<br/> 'P': 当前一个时间对齐完成]|

* C++ 类

```
// if qty == 0   // 涨跌停 不撮合交易
struct MarketTickMessage : public BaseMessage {
    std::uint32_t CfiCode = 0;
    std::uint64_t unix_time = 0;
    std::uint16_t frame = 0;
    char side = 'B';          // BID OR ASK  change e2::Side
    std::uint64_t price = 0;  // last price
    std::uint64_t qty = 0;
    std::uint32_t number = 0;
}; /* ----------  end of struct MarketTickMessage  ---------- */

typedef struct MarketTickMessage MarketTickMessage;

```

### 自定义数据

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      |   'C'           | Exit process                                |
| cficode      | 1      | 4      |   Integer       |  cfi code  |
| index         | 5     | 2      |   Integer 16    | value deci                                |
| size         | 7      | 2      |   Integer 16    | value deci                                |
| type        | 9    | 1      |   Alpha         |   Integer 16 = '6' , Integer 32 = '2', Integer 64 = '4' | 
| value          | 10      | 2,4,8 (...)      |   Integer 16,32,64    | data list                           |
| Aligned        | list size    | 1      |   Alpha         | 当前的状态 ['U': 后面还有数据,<br/> 'P': 当前一个时间对齐完成]       |

* C++ 类

```
enum CmType {
    UINT16 = '6',
    UINT32 = '2',
    UINT64 = '4'
}; /* ----------  end of enum CmType  ---------- */

typedef enum CmType CmType;

struct CustomMessage : public BaseMessage {
    std::uint32_t CfiCode = 0;
    std::uint16_t index = 0;  // 可以用作 dict 的索引
    std::uint16_t size = 0;
    char type;  // CmType
}; /* ----------  end of struct CustomMessage  ---------- */

typedef struct CustomMessage CustomMessage;

```

* E2L 代码

```
idx = 0;
size = FCustomDataSize(cficode, idx);
echo(size);

```

### LOG 日志

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      |   'L'           | Exit process                                |
| logt          | 1      | 2      |  Alpha    |  LogType_t   BASE = 'B', <br/>LINE = 'L', PRO = 'P', TIME = 'T'|
| numt          | 1      | 3      |  Alpha   |  NumberType_t    NEGATIVE = 'N', <br/>// -1121  POSITIVE = 'P'   // 2323 |
| val          | 4      | 8      |   Integer 64    |  variable value  |
| deci         | 12      | 2      |   Integer 16    | value deci                                |
| loc          | 14      | 4      |   Integer 32      | code line                                |
| ticket_now   | 18    | 8     |   Integer 64      | code line                                |
| pid          | 26      | 4      |   Integer 32    | code line                                |
| vname_len    | 30     | 2      |   Integer 16    | variable name length |
| path_len     | 32     | 2      |   Integer 16    | code path length           |
| alpha        | 34    | 256      |   Alpha         | code path + variable name length ,<br/> if > 256   alpha[:256]          |

* C++ 类
```
struct E2LScriptLogMessage {
    char MsgType;
    char logt;
    char numt;
    std::uint64_t value;
    std::uint16_t deci = 0;
    std::uint32_t loc = 0;
    std::uint64_t ticket_now = 0;
    std::uint32_t pid = 0;
    std::uint16_t vname_len = 0;
    std::uint16_t path_len = 0;
    char alpha[DYNAMIC_ALPHA] = {0};
}; /* ----------  end of struct E2LScriptLogMessage  ---------- */

typedef struct E2LScriptLogMessage E2LScriptLogMessage;
```


## 行情报价API 

#### c++ (部分代码)

```
/*
 * ===  FUNCTION  =============================
 *
 *         Name:  DataFormat::init
 *  ->  void *
 *  Parameters:
 *  - size_t  arg
 *  Description:
 *   初始化订阅行情
 * ============================================
 */
void DataFormat::init(char* ptr, std::uint32_t cficode, std::string symbol,
                      char type)
{
    using namespace e2q;

    // (network byte order) 结构的转换
    e2q::SystemInitMessage sim;

    if (ptr == nullptr) {
        return;
    }

    std::size_t stock_len = fldsiz(SystemInitMessage, Stock);

    sim.MsgType = e2l_pro_t::INIT;
    stock_len--;

    sim.CfiCode = cficode;
    sim.Itype = type;
    sim.Aligned = aligned_t::UNDER;
    sim.OfferTime = 0;

    if (type == 'i') {
        sim.Aligned = aligned_t::PULL;
        sim.OfferTime = std::uint32_t(_tick_sleep_time * _deci);
    }

    std::size_t idx = 0;

    *(ptr + idx) = sim.MsgType;
    idx++;

    memcpy((ptr + idx), symbol.c_str(), stock_len);

    idx += stock_len;
    idx += serialize_uint_t((ptr + idx), sim.CfiCode);

    *(ptr + idx) = sim.Itype;
    idx++;

    idx += serialize_uint_t((ptr + idx), sim.OfferTime);
    *(ptr + idx) = sim.Aligned;

} /* -----  end of function DataFormat::init  ----- */

```


### python (代码案例)

* 行情订阅及退出

```
class BaseMessage:
    '''
    整数的精度
    '''
    number_deci = 10000.0


class DataFormatProto(BaseMessage):
    '''
    Q = uint64
    I = uint32
    H = uint16
    s = Alpha
    [x]s = char[x]
    fmt = cIH8sIH
    '''

    def __init__(self, symId=0, tick_time=0.05):
        # 自己定义 id
        self._symId = symId
        # 当前一笔 ticket 报价有多少个 symbol
        self._tick_num = 0
        self._tick_data = ""

        # 间隔报价的时间
        self._tick_sleep_time = tick_time
        # 转成整数
        self._number_deci = BaseMessage.number_deci

        self.init_data = SystemInitMessage()

    def IndexCfiCode(self):
        '''
        指数代码
        '''
        return 0

    def thash(self):
        '''
        cfi code index
        '''
        self._symId = self._symId + 1
        return self._symId

    def add_symbol(self, sym):
        '''
        订阅 symbol
        
        '''
        symId = self.thash()

        self.init_data.Stock(sym, symId)
        data = self.init_data.toString()
        # logger.info(symId)
        return (symId, data)

    def Index(self, index_code):
        '''
        转成 二进制 char
        '''
        offer_time = int(self._tick_sleep_time * self._number_deci)
        # print(offer_time)
        self.init_data.Index(index_code, offer_time)
        data = self.init_data.toString()
        return data

    def pExit(self):
        '''
        
        退出
        '''
        data = struct.pack("!c", MsgType.EXIT)
        return data
```

## HUB A Book API

#### c++ (部分代码)

```
/*
 * ================================
 *        Class:  BaseMatcher
 *  Description: matcher 基类，不管是A Book (上游柜台) 或 B Book(backtest)
 * ================================
 */
class BaseMatcher {
public:
    /* =============  LIFECYCLE     =================== */
    BaseMatcher() = default; /* constructor */
    virtual ~BaseMatcher() = default;

    /* =============  ACCESSORS     =================== */

    /* =============  MUTATORS      =================== */
    virtual void InitSequence() = 0;
    virtual SeqType sequence(const FIX::SessionID &, e2::Side) = 0;

    // match
    virtual std::vector<OrderLots> matcher(std::string symbol, e2::Int_e now,
                                           e2::Int_e price,
                                           e2::Int_e adjprice) = 0;
    virtual bool insert(OrderItem *) = 0;
    virtual void display() = 0;
    virtual e2::Int_e CheckClose(SeqType ticket, const std::string &,
                                 e2::Int_e lots) = 0;
    virtual int AddBotTicket(SeqType ticket, e2::OrdType ordType, e2::Side side,
                             double bot_qty, e2::Int_e symbol) = 0;

    virtual bool OrdTypePending() = 0;
    virtual void TopLevelPrice(const std::string &symbol, SeqType) = 0;

    // broker
    virtual double Equity(const FIX::SessionID &, std::size_t,
                          const char status) = 0;
    // virtual void SettlInst(OrderLots &) = 0;
    virtual void freeMargin(const FIX::SessionID &, std::size_t, double) = 0;
    virtual bool Margin(const FIX::SessionID &, std::size_t, double, long) = 0;
    virtual double CheckMargin(const FIX::SessionID &, double, long) = 0;
    virtual double traders(const FIX::SessionID &, double) = 0;

    virtual void ExdrChange(SeqType, SeqType ticket, double cash, double qty,
                            std::size_t ctime) = 0;

    virtual void exist() = 0;
    /* =============  OPERATORS     =================== */

protected:
    /* =============  METHODS       =================== */

    /* =============  DATA MEMBERS  =================== */

private:
    /* =============  METHODS       =================== */

    /* =============  DATA MEMBERS  =================== */

}; /* -----  end of class BaseMatcher  ----- */

```

### 给 [E2Language](/e2l) 语言调用的 API 

```
/*
 * ===  FUNCTION  =============================
 *
 *         Name:  XXXMatcher
 *  ->  void *
 *  Parameters:
 *  - size_t  arg
 *  Description:
 *
 * ============================================
 */
void XXXMatcher(const char* config)
{
    if (GlobalMatcher == nullptr) {
        GlobalMatcher = std::make_shared<XXXMatch>(config);
    }

} /* -----  end of function XXXMatcher  ----- */
/*
 * ===  FUNCTION  =============================
 *
 *         Name:  CallE2XXX
 *  ->  void *
 *  Parameters:
 *  - size_t  arg
 *  Description:
 *
 * ============================================
 */
void CallE2XXX(std::vector<e2q::E2lFun_t>& funs)
{
    AddFunExt(funs, E2XXX, xxxMatcher, 1, "XXXMatcher", E2L_NORETURN, "(config);");
} /* -----  end of function CallE2XXX  ----- */

```

### E2LANGUAGE 调用案例

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
func ABook() {
    FBrokerBook(UBookType.ABook);  


    xxx_config = "/opt/TraderXXX/config/XXXSTrade.ini";
    XXXMatcher(xxx_config);
}
#----- func end


```
