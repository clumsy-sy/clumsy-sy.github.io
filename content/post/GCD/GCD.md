---
title: "GCD & EXGCD"
date: 2021-07-20
draft: false
mathjax: true
categories:
    - Math
    - Algorithm
---
# GCD & EXGCD

GCD （Greatest Common Divisor）即最大公约数。

## 欧几里得算法

我们已知两个数 $a$ ，$b$ ；假设 $a > b$，有两种情况：

1. $b \mid a$​ ，即 $\gcd (a, b) = b$。
2. $a = b\times k + a\bmod b$​​​​​​ ，此时我们要证明 $\gcd(a, b) = \gcd(b, a\bmod b)$​​​​ 。

设 
$$
a = b\times k + c，(c = a \bmod b)
$$
设 $d \mid a,\ d\mid b$​​​ ；则
$$
\dfrac{c}{d} = \dfrac{a}{d} - k\dfrac{b}{d}
$$
因为 $\dfrac{a}{d} $​​  和 $\dfrac{bk}{d}$​​​ 都为整数，所以 $\dfrac{c}{d}$​​​ 也是整数，即 $d \mid c$​​ 。所以对于所有的  $a, b$​​​ 的公约数，都是 $a\bmod b$​​ 的约数。

重新设 $d \mid b, d \mid c$
$$
\dfrac{a}{d} = k\dfrac{b}{d} + \dfrac{c}{d}
$$
因为 $\dfrac{c}{d} $​​​​​​  和 $\dfrac{bk}{d}$​​​​​​ 都为整数，所以 $\dfrac{a}{d}$​​​​​​ 也是整数，即 $d \mid a$​​​​​​ 。所以对于所有的  $b, a \bmod b$​​ 的公约数，都是 $a$​​​​​​​ 的约数。

两式的公约数是相同的，那么他们的最大公约数也相同。

得到了 $\gcd(a, b) = \gcd(b, a\bmod b)$ 。

```cpp
int gcd(int a, int b){
    return b ? gcd(b, a % b) : a;
}
```

对应的 LCM 也很简单

```cpp
int lcm(int a, int b){
    return (ll)a * b / gcd(a, b);
}
```

## 扩展欧几里得

EXGCD（Extended Euclidean algorithm）扩展欧几里得算法。常用于求 $ax + by = \gcd(a, b)$​​​ 的通解。

设
$$
\begin{aligned}
ax_{1} + by_{1} &= \gcd(a, b)\\\\
bx_{2} + (a \bmod b) y_{2} &= \gcd(b, a \bmod b)
\end{aligned}
$$
前面已经证明  $\gcd(a, b) = \gcd(b, a\bmod b)$

且
$$
a \bmod b = a - \left \lfloor \dfrac{a}{b}  \right \rfloor \times b
$$
得
$$
\begin{aligned}
ax_{1}+by_{1}&=bx_{2}+(a - \left \lfloor \dfrac{a}{b} \right \rfloor \times b)y_{2}\\\\
ax_{1}+by_{1}&=ay_{2}+b(x_{2} - \left \lfloor \dfrac{a}{b} \right \rfloor \times y_{2}) 
\end{aligned}
$$
得
$$
\begin{aligned}
x_{1} &= y_{2}\\\\
y_{1} &= x_{2} - \left \lfloor \dfrac{a}{b} \right \rfloor \times y_{2}
\end{aligned}
$$

```cpp
int exgcd(int a, int b, int &x, int &y) {
    if(!b) {
        x = 1, y = 0;
        return a;
    }
    int g = exgcd(b, a % b, y, x);
    y -=  a / b * x;
    return g;
}

int exgcd(int a, int b, int &x, int &y) {
  int x1 = 1, x2 = 0, x3 = 0, x4 = 1;
  while (b != 0) {
    int c = a / b;
    tie(x1, x2, x3, x4, a, b) = 
    make_tuple(x3, x4, x1 - x3 * c, x2 - x4 * c, b, a - b * c);
  }
  x = x1, y = x2;
  return a;
}
```

