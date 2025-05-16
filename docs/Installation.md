# 安装

## 需求外部系统

> [数据库 Postgresql](https://www.postgresql.org/docs/current/index.html)

> [Kafka](https://kafka.apache.org/quickstart)

## 从源码安装
> 以下安装将以 debian 为准
```
$ cat /etc/os-release 
PRETTY_NAME="Debian GNU/Linux 12 (bookworm)"
NAME="Debian GNU/Linux"
VERSION_ID="12"
VERSION="12 (bookworm)"
VERSION_CODENAME=bookworm
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"

```

### e2l script 语言安装
> 依赖包有以下这些

```
apt-get update
apt install build-essential cmake clang clangd clang-format \
            clang-tidy clang-tools  zlib1g-dev zlib1g libzstd1 libzstd-dev bison flex
```

> llvm 可能要作以下连接

``` 
ln -s /usr/include/llvm-14/llvm/ /usr/include/llvm 
ln -s /usr/include/llvm-c-14/llvm-c/ /usr/include/llvm-c
```

> 开始安装
```
$ git clone https://github.com/E2Quant/e2.git
$ cd e2
$ mkdir build
$ cd build
$ cmake  -DUSE_CCACHE=ON -DLIB=ON  ../
$ make && make install
```
 
### e2q 安装
> 依赖包有以下这些, 在 e2l 基础上面加入以下这些

```
sudo apt-get install -y libzstd1 libzstd-dev libquickfix-dev libquickfix17 \
                         uuid uuid-dev librdkafka++1 librdkafka-dev librdkafka1 \
                          pkg-config  libpq-dev  libpq5
```

```
$ git clone https://github.com/E2Quant/e2q.git
$ cd e2q

/// 如果需要下载不同的 hub  的话，可以放在这儿
$ cd hub
$ git clone https://github.com/E2Quant/e2q_hub.git

$ mkdir build
$ cd build
$ cmake -DUSE_CCACHE=ON -DKAFKALOG=ON  ../

// 如果下载了不同的 hub 模块的话
$ cmake -DDEBUG=ON -DUSE_CCACHE=ON -DKAFKALOG=ON -DHUB_etalib=ON -DHUB_E2Zts=ON ../

$ make 

/// 编译完成之后，可以测试一下
$ echo "echo(1);" > hello.e2
$ ./e2q -o hello.e2 
10000 [echo varname:   line:1, file:hello.e2]
6280->[main.cpp::only_run line.85] ret:1269273120

```