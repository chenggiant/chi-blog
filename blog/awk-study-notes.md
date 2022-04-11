---
title: AWK学习笔记
date: 2014-10-26
tags: blog
layout: post
---
# awk学习笔记

主要是按照[AWK 简明教程](http://coolshell.cn/articles/9070.html)大致学习了下最基本的用法而已。文章里的例子都很好，就不一一重复了。

### 过滤，指定分隔符和输出

```
awk  -F: '$1 ~ /root/ {print $1,$3,$6}' OFS="\t" /etc/passwd
```

- 匹配第一列里带root的 `$1 ~ /root/`
- 输出1，3，6列 `print $1, $3, $6`
- 指定分隔符 `-F:`, 指定tab分隔 `-F"\t"`

### 拆分文件

```
awk 'NR!=1{print $3, $4 > $6}' netstat.txt
```

- 不处理表头 `NR!=1`
- 按照第6列的值分割文件 `print > $6`

### 计算所有的C文件，CPP文件和H文件的文件大小总和

```
ls -l  *.cpp *.c *.h | awk '{sum+=$5} END {print sum}'
```

### 统计各个connection状态

```
awk 'NR!=1{a[$6]++;} END {for (i in a) print i ", " a[i];}' netstat.txt
```

### 实战1

文件 a.txt里面的数据如下：

CDIV|EQ0000000009687432|0.043100|SGD
CDIV|EQ0093389500001002|0.130000|CAD
CDIV|EQ0000000000809294|1.062000|THB
CDIV|EQ0140454700001000|0.100000|AUD
CDIV|EQ0000000001713818|0.104000|AUD
CDIV|EQ0000000009682851|0.021000|MYR
CDIV|EQ0011312900001000|0.650000|AUD
CDIV|EQ0000000004941362|0.011100|SGD
CDIV|EQ0000000009379087|0.020000|NZD
CDIV|EQ0012563100001000|0.450000|MYR
CDIV|EQ0012543000001000|0.500000|MYR

找出第4列有多少种货币，每个货币在这个文件里出现了多少次？

```
awk -F'|' '{print $4}' a.txt | sort | uniq | wc -l
awk -F'|' '{a[$4]++} END{for(i in a) print i, " ", a[i]}' a.txt
```

### 实战2

列出你使用次数最多的前10条命令：

```
history | awk '{CMD[$2]++;count++;} END { for (a in CMD )print CMD[a]" " CMD[a]/count*100 "% " a }' | grep -v "./" | column -c3 -s " " -t |sort -nr | nl | head -n10
```

- `CMD[$2]++` 是按照命令名称计数，`count++` 是计算命令总数
- `grep -v "./"` 是忽略带有 `./`的命令

### 补充

在上面我们可以看到一个END关键字。END的意思是“处理完所有的行的标识”，即然说到了END就有必要介绍一下BEGIN，这两个关键字意味着执行前和执行后的意思，语法如下：

- BEGIN {这里面放的是执行前的语句 }
- END {这里面放的是处理完所有的行后要执行的语句 }
- {这里面放的是处理每一行时要执行的语句}