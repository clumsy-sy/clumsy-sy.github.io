---
title: "字符串Hash"
date: 2021-07-16
draft: false
mathjax: true
categories:
    - Algorithm
---

# 字符串 Hash

## 字符串 Hash 的思想

Hash 的核心思想在于，将输入映射到一个值域较小、可以方便比较的范围。而在字符串中主要是为了方便比较，而且为了减少 **Hash 冲突**，一般映射的范围较大。

我们定义一个把 **字符串映射到整数** 的函数 $f$ ，这个 $f$ 称为是 Hash 函数。我们希望这个函数可以方便地帮我们判断两个字符串是否相等， 有两点很重要。

1. 在 Hash 函数值不一样的时候，两个字符串一定不一样；
2. 在 Hash 函数值一样的时候，两个字符串不一定一样（但有大概率一样，且我们当然希望它们总是一样的）。

**Hash** 函数值一样时字符串却不一样,  我们称为 **哈希冲突**。

## 如何 Hash

一般采用多项式的方法，对于一个长度为 $\large{l}$ 的字符串 $s $ ，我们可以定义 

$$
 f(s) =  \sum_{i = 1}^{l} s[i] \times b^{i - 1}\pmod m
$$

例如：对于字符串 $xyz$ ，其多项式为 $xb^{2} + yb^{1} + zb^{0}$ ，所以对字符串 Hash 的另一种理解就是将字符串转化为 $b$ 进制数。

有以下几点值得注意：

* 如果要从 $1$ 开始存储字符串，我们可以使用 `scanf("%s", s + 1)` 来读取输入。
* 对于 $b$ 的取值，我们一般取 $131$ 或者 $13331$（经验值，字符串哈希冲突较少）
* 对于模数 $M$，我们可以取一个大指数，如 `1e9 + 7,998244353,212370440130137957ll` 或者使用 `unsigned long long` 自动对 $2 ^{64}$ 取模。
* 对于减少哈希冲突，我们还可以通过双 Hash ，也就是分别用两个不同的 $base$ 和 $M$ 来计算。

 Hash 复杂度为 $O(N)$，查询某段子串的 Hash 复杂度为 $O(1)$ 。

子串查询原理：
$$
\begin{aligned}
hash[1] &= s[1] b^{0} \\\\\\\\
hash[2] &= s[1] b^{1} + s[2]^{0}\\\\\\\\
hash[3] &= s[1] b^{2} + s[2]^{1} + s[3]^{0}\\\\\\\\
hash[4] &= s[1] b^{3} + s[2]^{3} + s[3]^{1} + s[4]^{0}\\\\\\\\
hash[5] &= s[1] b^{4} + s[2]^{3} + s[3]^{2} + s[4]^{1} + s[5]^{0}
\end{aligned}
$$

假如我们要查询子串 $[3,5]$ ，我们会发现 $hash[5]$ 与 $hash[2]$，如果不看 b 的幂次，正好是答案，所以我们将 $hash[2]$ 的幂次每一位补齐到与 $hash[5]$ 一样。
$$
\begin{aligned}
hash[2] \times b^{3} &= s[1]b^{4} + s[2]b^{3}\\\\
hash[5] &= s[1] b^{4} + s[2]^{3} + s[3]^{2} + s[4]^{1} + s[5]^{0}
\end{aligned}
$$
此时将两者相减所得的值正好是 子串的 hash 值。

```cpp
typedef unsigned long long ull;
const ull base = 131, M = 1e5 + 7, mod = 1e9 + 7;
char s[M];
ull myhash[M], power[M]; //myhash 里面存算到某位时的 Hash 值，power 存 base 的幂次（子串查询才需要）

ull get_hash(char *s) {
    ull value = 0;
    power[0] = 1;
    for (int i = 1; s[i]; i++) {
        value = (value * base + s[i]) % mod;
        myhash[i] = value;
        power[i] = power[i - 1] * base % mod;
    }
    return value;//返回最终字符串的 Hash 值
}
//计算某段的 Hash 值
ull get_sub_hash(int l, int r) {
    return (myhash[r] - (myhash[l - 1] * power[r - l + 1]) % mod + mod) % mod;
}
```
