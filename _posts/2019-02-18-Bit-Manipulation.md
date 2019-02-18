---
layout: post
title: "Bit Manipulation"
date: 2019-02-18 14:33:07 +0800
comments: true
categories: bit-manipulation leetcode
---



>  一直对二进制的操作都不是很顺畅，感觉有点生涩，可以看懂，但是碰到问题总是没法很熟练的运用起来。位操作很高效。解决某种类型的问题也很奇特。



Java 中常用的几个位操作



1. `&`
2. `|`
3. `~`
4. `&`
5. `<<`
6. `>>`
7. `>>>`



- 按位与 （AND）`&` 

```shell
0&0 = 0
0&1 = 0
1&0 = 0
1&1 = 1
```

> 对应每一个比特位操作，只有两个都为1结果才是1，否则为 0。
> 将任一数值 x 与 0 执行按位与操作，其结果都为 0。将任一数值 x 与 -1 执行按位与操作，其结果都为 x。

- 按位或（OR）`|`

```shell
0|0 = 0
0|1 = 1
1|0 = 1
1|1 = 1
```

> 对应每一个比特位操作，只要其中有一个是1结果为1，否则为0。

- 按位非 （NOT）`~`

```shell
~0 = 1
~1 = 0
```

> 对应每一个比特位操作，1变成0，0变成1.
> 对任一数值 x 进行按位非操作的结果为 -(x + 1)。例如，~5 结果为 -6。

- 按位异或 `^` 

```shell
0 ^ 1 = 1
0 ^ 0 = 0
1 ^ 0 = 1
1 ^ 1 = 0
```

> 对应每一个比特位操作，两个操作的数中有且只有一个1结果1，否则为0.



**几个常用的技巧**

- 判断一个数是否是偶数。判断这个数X按位`&`1 如果结果为0，则是偶数，否则为奇数

```shell
X & 1 == 0 

xxxx xxx0
0000 0001
是偶数

xxxx xxx1
0000 0001
是奇数

```



- 判断一个数是否是2的幂。如果这个数X与X-1按位`^`结果为0 、则是2的幂，否则不是。

```shell
1000
0111
====
0000
```



引用

> [Your guide to Bit Manipulation](https://codeburst.io/your-guide-to-bit-manipulation-48e7692f314a)
>
> [Bitwise operation - Wikipedia](https://zh.wikipedia.org/wiki/%E4%BD%8D%E6%93%8D%E4%BD%9C)

> [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Summary](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Summary)