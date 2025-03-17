# E2L 语言

## 变量类型
- 整数
- 字符串
> 价格是整数字段，提供了相关的精度。精度是在编译代码的时候 CMakeLists.txt 中设置, 精度设置为：*10000*

```
# CMakeLists.txt
# number deci for expression
add_definitions(-DNUMBER_DECI=10000)
```

比如一个 symbol 的价格为: 5.89 ,那么在 e2l 中是: 58900

## 代码注释: *#*

```
# 
# 此处是注释。。。
#
test = 10;
c = abc(5);  # 注释
b = c + myunion.a;
str = "hello!"; ## 字符串

```

## 引入别的文件

```
import <file.e2>
```

 

## union 结构

>常量，不可变

```
union myunion{
    a=1;
    union inner_union{
      b=1;
    }
}
```

## 函数
```
func abc(a){
    b=10 + a; 
    return b; 
}
```

## 条件语法

```
###
##  if else
###
func label(a){
    b=1;
    if(a>=5){
        b=2;
    }else{
        b=5;
    }
    return b;
}

b=9;
echo(b);

s=label(2);
echo(s);

###
##  for
###
func abc(a){
    c=a;
    for(b=a;b<10;b++){
        c=b+a;
    }

    return c;
}

s= abc(2);
echo(s);

###
##  while
###
func uik(a){
b=2;
 while ( a < 5 ){
    a= a+b;
    echo(a);
}
return a;
}

b=uik(1);
echo(b);

c = abc(5);  
c++;

b = c + myunion.a + myunion.inner_union.b;
echo(b);

e = 10;
k=label(9);
echo(k);

s= afor(2);
```

## 外部函数
```
echo(10);

```