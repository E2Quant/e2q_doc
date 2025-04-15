# E2Q Protocol

### message type

* 0 -- index
* 1 -- base symbol data


### Init


| Name         | Offset | Length | Value   | Notes                                                                                 |
| :------------- | -------- | -------- | --------- | --------------------------------------------------------------------------------------- |
| Message Type | 0      | 1      | 'I'     | Init Type                                                                             |
| stock        | 1      | 10      | Alpha   | 股票名称                                                                             |
| cficode      | 11     | 4      | Integer | cfi code                                                                              |
| type         | 15     | 1      | Alpha | symbol type ['i':index, 't':trade]                                                        |
| TickTime     | 16      | 4      | Integer | 每一笔报价的间隔时间, 回测的时候使用的, <br/>免得策略超过这个报价的间隔时间就有点麻烦了 |
| Aligned      | 20      | 1      | Alpha |  当前的状态 ['U': 后面还有数据, <br/>'P': 当前一个时间对齐完成]|


### Exit


| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      | 'E'     | Exit process                                |


### SUSPEND


| Name         | Offset | Length | Value   | Notes                                   |
| :------------- | -------- | -------- | --------- | ------------------------------------------ |
| Message Type | 0      | 1      | 'S'     | suspend order                                |


### Xdxr


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


### TICK



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

```
LOG
```

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
