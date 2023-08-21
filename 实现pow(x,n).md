---
title: 实现pow(x, n)
date: 2023-08-21 04:17:30
tags: [leetcode,algorithm,ARTS]
---

leetcode第[50题](https://leetcode.cn/problems/powx-n/)，难度中等。

*实现 pow(x, n) ，即计算 x 的整数 n 次幂函数（即，xn ）。*

1. 直接用递归
```
class Solution {
public:
    double myPow(double x, int n) {
        if (n > 0) {
            return x * myPow(x, n - 1);
        } else if (n == 0) {
            return 1.0;
        } else {
            return 1.0 / myPow(x, -n);
        }
    }
};
```
毫无疑问，在n非常大时堆栈溢出了。

![stack overflow](实现pow(x,n)/stack_overflow.png)

2. 优化递归次数

可以将指数分成两半，求出一半的值后，一半的值乘以一半的值即为全部的值，要注意整数溢出的问题。
```
class Solution {
public:
    double myPow(double x, int n) {
        if (n == 0) {
            return 1.0;
        } else if (n == 1) {
            return x;
        } else if (n == -1) {
            return 1/x;
        }

        double ret = 0.0;
        const int exponent = n > 0 ? n : -(n == -2147483648 ? -2147483647 : n);
        if (exponent % 2 == 0) {
            ret = myPow(x, exponent / 2);
            ret *= ret;
        } else {
            ret = myPow(x, (exponent-1) / 2);
            ret = ret * ret * x;
        }
        
        if (n == -2147483648) {
            ret *= x;
        }

        return n >= 0 ? ret : 1/ret;        
    }
};
```
 ![减少递归次数](实现pow(x,n)/recuce_recursives.png)