---
title: gcd和lcm和裴蜀定理
categories: 
- 算法
- 数论
tags: 
- 算法
---

# gcd(辗转相除法)

我们将两数中较大的那一个看作是被除数A，将较小的那一个看作是除数B，二者相除的商记作C，余数记作D。这样我们就可以得到一个等式：A = B×C + D。而辗转相除法的所要用到的原理则是：(A , B) = (B , D)。

（m，n)表示m%n,直到（ ， ）=0，则最后的余数为两个数的最大公因数。

cpp里有库函数可以直接调,也可以手搓一个

```cpp
int gcd(int a, int b){
    return b ? gcd(b, a % b) : a;
}
```

# lcm( 最小公倍数)

a 乘 b 除以 gcd(a,b) = lcm

# 裴蜀定理

属于gcd的拓展

## 定义

a,b为整数,则有整数x和y,使得ax + by = d*gcd(a,b)

<!-- more -->

d为正整数(最小为1)

## 推论

a,b**互质**的**充分必要条件**是存在整数x,y 使 **ax+by=1**

## 例题

洛谷p4549

https://www.luogu.com.cn/problem/P4549

力扣1250

https://leetcode.cn/problems/check-if-it-is-a-good-array/description/