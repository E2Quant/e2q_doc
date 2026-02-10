# E2Q 协议说明

## E2Q 协议  

> 协议采用 (network byte order) 结构


### Init 初始化订阅 symbol

* 格式

| Name         | Offset | Length | Value   | Notes                          |
| :----------- | ------ | ------ | ------- | ---------------  |
| Message Type | 0      | 1      | 'I'     | Init Type                       |
| stock        | 1      | 10     | Alpha   | 股票名称                         |
| cficode      | 11     | 4      | Integer | cfi code                            |
| type         | 15     | 1      | Alpha   | symbol type ['i':index, 't':trade]     |
| TickTime     | 16     | 4      | Integer | 每一笔报价的间隔时间, 回测的时候使用的, <br/>免得策略超过这个报价的间隔时间就有点麻烦了 |
| unix_time    | 20     | 8      | Inte 64 | 默认为上市时间                                |
| Aligned      | 28     | 1      | Alpha   |  当前的状态 ['U': 后面还有数据, <br/>'P': 当前一个时间对齐完成]|

* C++ 类
```

struct BaseMessage {
    char MsgType;
    char Aligned;
}; /* ----------  end of struct BaseMessage  ---------- */


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

###  MARKET  交易市场

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type   | 0        | 1        | 'M'       | market                               |
| Action         | 1        | 1        | 'L' or 'D'  | 'L' to list(Going Public), 'D' is Delisting  |
| cficode        | 2        | 4        |   Integer       |  cfi code  |
| unix_time    | 20      | 8      |   Integer 64    | 上市或退市时间                                |

* C++ 类

```

```

### 除权分红

* 格式

| Name         | Offset | Length | Value   | Notes                             |
| :------------- | -------- | -------- | --------- | ------------------------- |
| Message Type | 0      | 1      | 'X'     |     除权分红               |
| cficode      | 1      | 4      | Integer |  cfi code  |
| year         | 5      | 2      | Integer |  year   |
| month        | 7      | 2      | Integer |  month  |
| day          | 9      | 2      | Integer |  day  |
| category     | 11     | 2      | Integer |  category  |
| fenhong      | 13     | 4      | Integer |  fenhong  |
| songzhuangu  | 17     | 4      | Integer |  songzhuangu  |
| split        | 21     | 4      | Integer | split      |
| outstanding  | 25     | 4      | Integer | outstanding|
| outstandend  | 26     | 4      | Integer | outstandend|
| mrketCaping  | 33     | 4      | Integer | mrketCaping|
| Aligned      | 37     | 1      | Alpha   |  当前的状态 ['U': 后面还有数据,<br/> 'P': 当前一个时间对齐完成]|

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
| Message Type | 0      | 1      |   'T'           | Tick process                                |
| cficode      | 1      | 4      |   Integer       |  cfi code  |
| unix_time    | 5      | 8      |   Integer 64    | unix_time                                |
| frame        | 13      | 2      |   Integer  16   | frame                                |
| side         | 15     | 1      |   Alpha         | side [ 'B' : Buy Order, 'S' : Sell Order ]          |
| price        | 16     | 6      |   Integer 64    | price           |
| qty          | 22     | 6      |   Integer 64    | qty           |
| number       | 28     | 6      |   Integer 32   | number           |
| match        | 34     | 1      |   Alpha   | 'n','y' 当前一笔报价是否可作为撮合价格           |
| Aligned      | 35     | 1      | Alpha |  当前的状态 ['U': 后面还有数据,<br/> 'P': 当前一个时间对齐完成]|

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

### 独立撮合价

* 格式

| Name         | Offset | Length | Value     | Notes       |
| :----------- | ------ | ------ | --------- | --------- |
| Message Type | 0      | 1      | 'D'       | Deal      |
| stock        | 1      | 10     | Alpha     | 股票名称      |
| side         | 11     | 1      | Alpha     | 'B', 'S'  |
| dprice       | 12     | 6      | Integer64 | 成交均价     |
| dqty         | 18     | 6      | Integer64 | qty       |
| commission   | 24     | 6      | Integer64 | commission       |
| tamount      | 30     | 6      | Integer64 | 成交额   |
| tdate        | 36     | 6      | Integer32 | trade date       |
| ttime        | 42     | 6      | Integer32 | trade time       |
| unix_time    | 48     | 8      | Integer64 | unix_time |
| ticket       | 54     | 8      | Integer64 | 当前一笔的ticket      |
| unique_size  | 62     | 6      | Integer16 | size   |
| unique_id    | 68     | 256    | Alpha     | unique value   |
| Aligned      | 324    | 1      | Alpha     | Aligned_t |


* python

```python
class BaseMessage:
    '''
    整数的精度
    '''
    number_deci = 10000.0

class MsgType:
    INIT = b'I'
    XDXR = b'X'
    SUSPEND = b'S'
    TICK = b'T'
    CUSTOM = b'C'
    MARKETING = b'M'
    EXIT = b'E'
    LOG = b'L'
    DEAL = b'D'


class Aligned:
    # 进行中
    UNDER = b'U'
    # 完成
    PULL = b'P'

class DealMatchMessage(BaseMessage):
    """"""

    def __init__(self):
        """Constructor for """
        self._mt = MsgType()
        self._al = Aligned()

        # 转成整数
        self._number_deci = BaseMessage.number_deci

        self.msgtype = self._mt.DEAL
        self._stock = ""
        self._side = b''
        self._dprice = 0
        self._dqty = 0
        self._commision = 0
        self._tamount = 0
        self._tdate = 0
        self._ttime = 0
        self._unix_time = int(time.time())
        self._ticket = 0
        self._unique_size = 0
        self._unique_id = ""

        self.anligned = self._al.UNDER

    def Stock(self, symbol):

        if not isinstance(symbol, bytearray):
            self._stock = symbol.encode('utf-8')
        else:
            self._stock = symbol

    def data(self, side, price, qty, commision, amount, tdate, ttime, unix_time,
             ticket, unique):
        self._side = side
        self._dprice = int(price * self._number_deci)
        self._dqty = qty
        self._commision = commision
        self._tamount = amount
        self._tdate = tdate
        self._ttime = ttime
        self._unix_time = unix_time
        self._ticket = ticket
        self._unique_size = len(unique)
        self._unique_id = unique

        if not isinstance(unique, bytearray):
            self._unique_id = unique.encode('utf-8')
        else:
            self._unique_id = unique

    def toString(self):
        """

        """
        uinx_time_64 = struct.pack("!Q", self._unix_time)
        tick_64 = struct.pack("!Q", self._ticket)

        price_64 = struct.pack("!Q", int(self._dprice))
        price_64 = price_64[2:]

        qty_64 = struct.pack("!Q", int(self._dqty))
        qty_64 = qty_64[2:]

        commi_64 = struct.pack("!Q", int(self._commision * self._number_deci))
        commi_64 = commi_64[2:]

        amount_64 = struct.pack("!Q", int(self._tamount * self._number_deci))
        amount_64 = amount_64[2:]

        fmt = '!c9sc6s6s6s6sII8s8sH' + str(self._unique_size) + 'sc'

        data = struct.pack(fmt,
                           self.msgtype, self._stock,
                           self._side, price_64, qty_64, commi_64, amount_64,
                           self._tdate, self._ttime,
                           uinx_time_64, tick_64, self._unique_size,
                           self._unique_id,
                           self.anligned)

        return data
```

### 自定义数据

* 格式

| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      |   'C'           | Custom process                                |
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
| Message Type | 0      | 1      |   'L'           | Log process                                |
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

### 给 [E2Language](https://github.com/E2Quant/e2) 语言调用的 API 

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

## E2Q Protocol 生成历史记录文件 (e2b)
### Python 案例

```python

    def BuildE2B(self, stock, symId, df_price, frame):
        '''
        生成 e2b
        '''
        if not os.path.isdir(self._e2b_dir):
            os.makedirs(self._e2b_dir)

        path_name = self._e2b_dir + stock + "_" + str(symId) + ".e2b"
        mtickm = MarketTickMessage()
        columns = ["open", "low", "high", "close", "volume"]
        number = 0
        for index, index_row in df_price.iterrows():
            unix_time = self.__unixtime(index.value)
            index_bar = index_row[columns].values
            for idx in range(4):
                qty = index_bar[-1]
                price = index_bar[idx]

                mtickm.UinxTime(unix_time)
                mtickm.Stock(frame, qty, price, number, symId)
                bin_data = mtickm.toString()

                with open(path_name, "ab+") as bin_files:
                    bin_files.write(bin_data)

            number += 1

    def __parse(self, bdata):
        '''
            解释二进制数据
        '''
        data = {}

        idx = 0
        _MsgType = struct.unpack_from('!c', bdata, idx)
        idx += 1
        data['msgtype'] = _MsgType[0]

        _CfiCode = struct.unpack_from('!I', bdata, idx)
        _CfiCode = _CfiCode[0]
        data['cficode'] = _CfiCode
        idx += 4

        _ticket_now = struct.unpack_from('!Q', bdata, idx)
        _ticket_now = _ticket_now[0]
        idx += 8
        data['ticket_now'] = _ticket_now

        _frame = struct.unpack_from('!H', bdata, idx)
        _frame = _frame[0]
        data['frame'] = _frame
        idx += 2

        _side = struct.unpack_from('!c', bdata, idx)
        data['side'] = _side[0]
        idx += 1

        price_64 = struct.unpack_from('!6s', bdata, idx)
        b64 = b'\x00\x00' + price_64[0]
        price = struct.unpack_from('!Q', b64)
        data['price'] = price[0]
        idx += 6

        qty_64 = struct.unpack_from('!6s', bdata, idx)
        b64 = b'\x00\x00' + qty_64[0]
        qty = struct.unpack_from('!Q', b64)
        data['qty'] = qty[0]
        idx += 6

        _number = struct.unpack_from('!I', bdata, idx)
        _number = _number[0]
        data['number'] = _number
        idx += 4

        logger.info(data)

    def ReadE2B(self, stock, symId):
        path_name = self._e2b_dir + stock + "_" + str(symId) + ".e2b"
        tick_len = 33
        next_seek = tick_len

        with open(path_name, "rb") as bin_files:
            while True:
                data = bin_files.read(tick_len)
                if len(data) == 0:
                    break
                bin_files.seek(next_seek)

                self.__parse(data)
                next_seek += tick_len

```