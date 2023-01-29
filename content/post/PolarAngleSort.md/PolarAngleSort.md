---
title: "PolarAngleSort"
date: 2021-09-30T15:35:31+08:00
draft: false
mathjax: true
categories:
    - Algorithm
---

# 极角排序

## 方法 1 : 直接计算极角

极坐标与直角坐标转换公式有 $\tan \theta = \dfrac{y}{x}$ ，在 `<cmath>` 中有函数 `atan(y, x)`  ，可以直接计算 `(x, y)` 的极角，值域是 $(-\pi,\pi]$ ，可以直接使用。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long double ld;
const int M = 1e5 + 7;
struct Point {
    ld x, y;
    Point(const ld &x = 0, const ld &y = 0) : x(x), y(y) {}
}p[M];
int main() {
    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++) {
        int x, y;
        scanf("%d%d", &x, &y);
        p[i] = Point(x, y);
    }

    sort(p + 1, p + n + 1, [&](auto p1, auto p2) {
        return atan2(p1.y, p1.x) < atan2(p2.y, p2.x);
    });
    for (int i = 1; i <= n; ++i)cout << '(' << p[i].x << ',' << p[i].y << ',' << atan2(p[i].y, p[i].x) <<')' << endl;
    return 0;
}
```

这种极角排序，有一下几点需要注意：

1. 排序完之后是按照 (三（不含负 $x$ 轴）、四、一、二) 的象限顺序。

2. `atan(y,x)` 是存在精度误差的可能会被卡。

## 方法二 : 叉积 + 所在象限排序

通过两向量象限关系 + 叉积关系来排序

```cpp
#include<iostream>
#include<cmath>
#include<algorithm>
using namespace std;
typedef long double ld;
const int M = 1e5 + 7;
struct Point {
    ld x, y;
    int id;
    Point(const ld &x = 0, const ld &y = 0) :x(x), y(y){}
    Point operator-(const Point& b){return Point(x - b.x, y - b.y);}
    ld get_length() {return sqrt(x * x + y * y);}
}p[M];
ld cross(Point a, Point b) {
    return a.x * b.y - a.y * b.x;
}
int qua(Point p) { return (p.y < 0) << 1 | (p.x < 0) ^ (p.y < 0);}    // 求象限(0, 1, 2, 3)
bool cmp(Point a, Point b) { // cmp 写法
    return qua(a) < qua(b) || qua(a) == qua(b) && (cross(a, b) > 0.0);
}
bool cmp2(const Point &a, const Point &b)//先按象限排序，再按极角排序，再按远近排序 
{
    if (a.y == 0 && b.y == 0 && a.x*b.x <= 0)return a.x>b.x;
    if (a.y == 0 && a.x >= 0 && b.y != 0)return true;
    if (b.y == 0 && b.x >= 0 && a.y != 0)return false;
    if (b.y*a.y <= 0)return a.y>b.y;
    return cross(a,b) > 0 || (cross(a,b) == 0 && a.y > b.y);
}
int main() {
    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++) {
        int x, y;
        scanf("%d%d", &x, &y);
        p[i] = Point(x, y);
    }
    Point c = Point(0, 0); // 原点（可更改）
    sort(p + 1, p + n + 1, [&](auto v1, auto v2) {
        return qua(v1 - c) < qua(v2 - c) || qua(v1 - c) == qua(v2 - c) && (cross(v1 - c, v2 - c) > 0.0);
    });
    for (int i = 1; i <= n; ++i)cout << '(' << p[i].x << ',' << p[i].y <<')' << endl;
    return 0;
}
```

注意点：

1. 在点为整数的情况下精度较高，速度可能比上面的方法快。

2. 排序后结果是按照（一（含正 $x$ 轴）、二、三、四）坐标轴排列。

## 例题 1 ：[C. Nearest vectors](https://codeforces.com/contest/598/problem/C)

模板题，极角排序后求相邻两向量的夹角即可，最后输出最小对的坐标。

```cpp
#include<iostream>
#include<cmath>
#include<algorithm>
using namespace std;
typedef long double ld;
const int M = 1e5 + 7;
const ld PI = acos(-1);
struct Point {
    ld x, y;
    int id;
    Point(const ld &x = 0, const ld &y = 0, const int &id = 0) :x(x), y(y), id(id) {}
    Point operator-(const Point& b){return Point(x - b.x, y - b.y);}
    ld get_length() {return sqrt(x * x + y * y);}
}p[M];
ld dot(Point a, Point b) {
    return a.x * b.x + a.y * b.y;
}
ld cross(Point a, Point b) {
    return a.x * b.y - a.y * b.x;
}
double get_angle(Point a, Point b) { // 求两向量夹角
    return acos(dot(a, b) / a.get_length() / b.get_length()); // 弧度 -> (* 180 / PI) 角度
}
ld theta(Point p) { return atan2(p.y, p.x);} 
int qua(Point p) { return (p.y < 0) << 1 | (p.x < 0) ^ (p.y < 0);}    // 求象限
bool cmp(Point a, Point b) {
    return qua(a) < qua(b) || qua(a) == qua(b) && (cross(a, b) > 0.0);
}
int main() {
    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++) {
        int x, y;
        scanf("%d%d", &x, &y);
        p[i] = Point(x, y, i);
    }
    sort(p + 1, p + n + 1, cmp);
    p[n + 1] = p[1];
    pair<int, int> ans = {1, 2};
    ld mmax = 2 * PI + 1;
    for(int i = 1; i <= n; i ++) {
        ld now = theta(p[i+1]) - theta(p[i]);
        // 其实只是为了处理 两向量一个在第二象限，一个在第三象限的情况
        // 为了处理 atan 的边界
        if(now < 0) now += 2 * PI;
        if(now < mmax) {
            mmax = now;
            ans = {p[i].id, p[i+1].id}; 
        }
    }
    printf("%d %d\n", ans.first, ans.second);
    return 0;
}
```

## 例题 2 ：[[USACO10OPEN]Triangle Counting G - 洛谷](https://www.luogu.com.cn/problem/P2992)

题意：给出平面上的很多点，求包含原点的三角形的个数。

思路：先求出一共可以组成多少种三角形，使用组合数 $\binom{n}{3} $，再减去不满足条件的三角形，可以发现，对于任何一个不满足条件的三角形必然存在，其中**两**个顶点与原点组成的直线后其余两个点观点都在同侧。在极角排序后，我们可以遍历每个点（看作以原点为起点向量），求出与这个点夹角小于 $\pi$ 的个数，也就是减去 $\binom{n}{2}$ ，就是答案了，这里可以使用二分或者双指针算法，每次只需要减去一边的不满足条件的即可。

```cpp
#include <algorithm>
#include <cmath>
#include <iostream>
using namespace std;
typedef long double ld;
typedef long long ll;
typedef pair<ld, int> PLI;
const int M = 1e5 + 7;
const ld PI = acos(-1);
struct Point {
    ll x, y;
    Point(ll x = 0, ll y = 0) : x(x), y(y) {}
} p[M];
PLI angle[M];
bool check(int a, int b) {
    return p[a].x * p[b].y >= p[a].y * p[b].x; //逆时针旋转小于 PI (本质上是叉积)
}
int main() {
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        p[i] = Point(x, y);
        angle[i] = {atan2(y, x), i};
    }
    sort(angle + 1, angle + 1 + n);  // 极角排序
    ll ans = 1LL * n * (n - 1) * (n - 2) / 6;
    ll now = 0; // 当前满足条件的点的个数
    for (int l = 1, r = 2; l <= n; l++) {
        while (check(angle[l].second, angle[r].second)) {
            now++;
            r = r % n + 1; // r ++，外加取模，一举两得
        }
        ans -= now * (now - 1) / 2;
        now--;
    }
    printf("%lld\n", ans);
    return 0;
}
```

## 例题 3 : [[POI2008]TRO-Triangles - 洛谷](https://www.luogu.com.cn/problem/P3476#sub)

题意：给定平面上的一些点，求这些点能组成的所有三角形的面积之和，三点同一直线上面积算作 $0$ 。

分析：

题目的数据范围是 $n \le 3000$，由组合数可知，暴力作法的时间复杂度为 $O(n^3)$ 是过不了的，所以我们对这么多的点进行分析。

已知三点 $A(x_a,y_a),B(x_b,y_b),C(x_c,y_c)$ 

则它们所围成的三角形的面积可以这么算 ：

$$
\begin{aligned}
S &= \dfrac{1}{2}\overrightarrow{AB} \times \overrightarrow{AC} \\\\
&= \dfrac{1}{2}(x_b - x_a, y_b - y_a) \times (x_c - x_a, y_c - y_a) \\\\
&= \dfrac{1}{2}(x_b-x_a)(y_c-y_a) - (y_b - y_a)(x_c - x_a) \\\\
&= \dfrac{1}{2}(x_b y_c + x_a y_a - x_a y_c - x_b y_a) - (y_b x_c + y_a x_a - y_b x_a -y_a x_c) \\\\
&= \dfrac{1}{2}x_a(y_b - y_c) + y_a(x_c - x_b)
\end{aligned}
$$

所以我们可以先对所有点根据 $x$ ，$y$ （从小到大）双关键字排序，然后确定第一个点，也就算遍历一遍每个点，然后对后面的点极角排序，这样的话叉积结果可以直接是正数，然后遍历两个点的时间复杂度太高，所以我们就遍历其中一个点，另一个点则可以使用后缀和代替。

$$
S = \dfrac{1}{2}x_a(y_b - \sum y_c) + y_a(\sum x_c - x_b)
$$

```cpp
#include <algorithm>
#include <cmath>
#include <iostream>
using namespace std;
typedef long double ld;
typedef long long ll;
typedef pair<int, int> PII;
const int M = 3e3 + 7;
struct Point {
    int x, y;
    Point(int x = 0, int y = 0) : x(x), y(y) {}
} p[M], add[M];
bool cmp(Point a, Point b) {
    return a.x == b.x ? a.y < b.y : a.x < b.x;
}
bool cmp_angle(Point a, Point b) {
    return 1LL * a.x * b.y > 1LL * a.y * b.x;
}
int main() {
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        p[i] = Point(x, y);
    }
    ll ans = 0;
    sort(p + 1, p + n + 1, cmp); // 对各个点按照 x 坐标排序
    for(int i = 1; i <= n; ++i) {
        int cnt = 0;
        for(int j = i + 1; j <= n; j ++) { // 求到当前点的向量
            add[cnt].x = p[j].x - p[i].x; 
            add[cnt].y = p[j].y - p[i].y;
            cnt ++;
        }
        ll sumx = 0, sumy = 0;
        sort(add, add + cnt, cmp_angle); // 对这些项目极角排序（这样面积都为整数）
        for(int j = 0; j < cnt; j ++) sumx += add[j].x , sumy += add[j].y; // 求后缀和
        for(int j = 0; j < cnt; j ++) { // 遍历第二个点
            sumx -= add[j].x, sumy -= add[j].y; // 第三个点的和
            ans += sumy * add[j].x - sumx * add[j].y;
        }
    }
    printf("%lld.%lld", ans/2, (ans & 1) * 5); // 小数处理
    return 0;
}
```
