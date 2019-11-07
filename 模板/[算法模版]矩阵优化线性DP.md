# [算法模版]矩阵优化线性DP

## 前言

在很多时候，我们的DP方程都是线性转移的。这时候我们就可以使用矩阵进行转移加速，做到$log$级别复杂度求解答案。

## 具体实现

首先，如果要使用矩阵加速线性DP，我们的相关状态必须限制在$P$内。因为这样矩阵优化转移的时间是$P^3\log_2 n$。

---

我们首先构造出一个$P\times 1$的状态矩阵，代表初始状态（这很好构造）。

在构造一个$P\times P$的**转移矩阵**的时候，我们的第$P$行就代表第$P$个状态是如何构造的。第$P$行的第$K$列的系数就代表第$P$个状态里面包含了多少个$K$状态。

## 常数优化

因为**转移矩阵**中有很多的0，所以我们可以利用这点来进行优化。

我们原来的矩阵乘法是这样写的：

```cpp
matrix operator*(const matrix &add) {
    struct matrix res;
    for(int i=1;i<=125;i++) {
        for(int j=1;j<=125;j++) {
            for (int k=1;k<=125;k++) {
                res.a[i][j]=(res.a[i][j]+1ll*a[k][j]*add.a[i][k])%mod;
            }
        }
    }
    return res;
}
```

但是我们发现`add.a[i][k]`只与$i$和$k$相关，所以如果我们交换$k$和$j$的循环顺序。若$add[i][k]=0$，我们就可以直接`continue`，因为就算进入循环，加上的东西只会是0。这里`continue`掉的话可以节省一个$P$的复杂度。

```cpp
matrix operator*(const matrix &add) {
    struct matrix res;
    for (int i = 1; i <= 125; i++) {
        for (int k = 1; k <= 125; k++) {
            if (!add.a[i][k])continue;
            for (int j = 1; j <= 125; j++) {
                res.a[i][j] = (res.a[i][j] + 1ll * a[k][j] * add.a[i][k]) % mod;
            }
        }
    }
    return res;
}
```

改成这样就跑的飞快了。

### 一道例题

[排队](http://auoj.net/oj/contest/570/problems/1)

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <cmath>
using namespace std;
int a, b, c, d, e;
long long n;
const int mod = 1000000007;
struct matrix {
    int a[126][126];
    matrix() { memset(a, 0, sizeof(a)); }
    matrix operator*(const matrix &add) {
        struct matrix res;
        for (int i = 1; i <= 125; i++) {
            for (int k = 1; k <= 125; k++) {
                if (!add.a[i][k])
                    continue;
                for (int j = 1; j <= 125; j++) {
                    res.a[i][j] = (res.a[i][j] + 1ll * a[k][j] * add.a[i][k]) % mod;
                }
            }
        }
        return res;
    }
} s, add, re, st;
matrix ksm(matrix x, long long t) {
    matrix res = st;
    for (; t; t >>= 1, x = x * x) {
        if (t & 1)
            res = res * x;
    }
    return res;
}
inline int get(int team, int num) { return (team - 1) * 4 + num; }
int main() {
    scanf("%lld%d%d%d%d%d", &n, &a, &b, &c, &d, &e);
    for (int i = 0; i < 126; i++) st.a[i][i] = 1;
    s.a[get(31, 1)][1] = e;
    s.a[get(31, 2)][1] = 1;
    s.a[get(31, 3)][1] = d;
    s.a[get(31, 4)][1] = 1;
    for (int i = 1; i <= 30; i++) {  //继承上一组
        add.a[get(i, 1)][get(i + 1, 1)] = 1;
        add.a[get(i, 2)][get(i + 1, 2)] = 1;
        add.a[get(i, 3)][get(i + 1, 3)] = 1;
        add.a[get(i, 4)][get(i + 1, 4)] = 1;
    }
    add.a[get(31, 1)][get(32 - c, 1)] = 1;
    add.a[get(31, 1)][get(32 - c, 2)] = e;
    add.a[get(31, 1)][get(32 - b, 3)] = 1;
    add.a[get(31, 1)][get(32 - b, 4)] = e;

    add.a[get(31, 3)][get(32 - b, 1)] = 1;
    add.a[get(31, 3)][get(32 - b, 2)] = d;
    add.a[get(31, 3)][get(32 - a, 3)] = 1;
    add.a[get(31, 3)][get(32 - a, 4)] = d;

    add.a[get(31, 2)][get(32 - c, 2)] = 1;
    add.a[get(31, 2)][get(32 - b, 4)] = 1;

    add.a[get(31, 4)][get(32 - b, 2)] = 1;
    add.a[get(31, 4)][get(32 - a, 4)] = 1;
    re = ksm(add, n);
    s = s * re;
    long long ans = 0;
    ans = s.a[get(31, 1)][1] + s.a[get(31, 3)][1];
    ans %= mod;
    printf("%lld", ans);
    return 0;
}
```



