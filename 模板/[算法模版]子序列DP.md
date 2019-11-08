# [算法模版]子序列DP

如何求本质不同子序列个数？

## 朴素DP

复杂度为$O(nq)$。其中$q$为字符集大小。

$dp[i]$代表以第$i$个数结尾的本质不同子序列个数。注意，这里对于每一个字符，只计算**上一个相同字符**带来的贡献。如果全部计算的话会算重复。

最后统计答案的时候也只统计每个字符最后一次出现的位置的答案。

例题：[【线上训练13】子序列](http://www.zhengruioi.com/problem/1186) 中的50分部分分

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
using namespace std;
const int maxn=2e6+100,mod=998244353;
typedef long long ll;
char s[2][maxn],ori[maxn];
int n,len=1;
int last[30];
ll dp[maxn];
void init() {
    scanf("%s",ori+1);
    s[1][1]=ori[1];
    for(int i=2;i<=n;i++) {
        int md=i&1;
        for(int j=1;j<=len;j++) {
            s[md][2*j-1]=ori[i];
            s[md][2*j]=s[md^1][j];
        }
        s[md][2*len+1]=ori[i];
        len=2*len+1;
    }
}
int main() {
    scanf("%d",&n);
    init();
    scanf("%s",s[n&1]+1);len=strlen(s[n&1]+1);
    for(int i=1;i<=len;i++) {
        int ty=s[n&1][i]-'a';
        dp[i]=1;
        for(int j=0;j<26;j++) {
            if(last[j]){
                dp[i]+=dp[last[j]];
            }
        }
        dp[i]%=mod;
        last[ty]=i;
    }
    ll ans=0;
    for(int i=0;i<26;i++) {
        if(last[i])ans+=dp[last[i]];
        ans%=mod;
    }
    ans%=mod;
    printf("%lld",ans);
}
```

## 优化DP

我们令$f[i]=\sum_{j=1}^q dp[last[c[j]]]$。其中$q$为字符集大小，$c$字符集。

转移的时候有两种情况：

1. 当前字符未出现过（第一个）。那么令$dp[i]=f[i-1]+1$，则$f[i]=2\times f[i-1]+1$。
2. 当前字符在原来出现过。那么令$dp[i]=f[i-1]+1$。因为只记录最后一次，所以在给$f$加上这一次的$dp$值之后还要删去上一次的$dp$值。$f[i]=2\times f[i-1]-dp[last[i]]+1$。因为$dp[last[i]]=f[last[i]-1]+1$，所以$f[i]=2\times f[i-1]-f[last[i]-1]$

其实本质就是利用子序列DP每次增加的都是$\sum dp[每一个字符]$这个特性，每次转移将$\sum dp[每一个字符]$用$O(1)$复杂度起来，做到$O(1)$的转移。

算法复杂度$O(n)$

例题：[FZU-2129](https://vjudge.net/problem/FZU-2129)

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;
int n,a[1000500],f[1000500],last[1000500],ans=0;
const int mod=1e9+7;
int main() {
    ios::sync_with_stdio(0);
    while(cin>>n) {
        for(int i=1;i<=n;i++)cin>>a[i];ans=0;
        memset(f,0,sizeof(f));memset(last,0,sizeof(last));
        for(int i=1;i<=n;i++) {
            if(!last[a[i]])f[i]=(2ll*f[i-1]+1)%mod;
            else f[i]=(2ll*f[i-1]-f[last[a[i]]-1])%mod;
            last[a[i]]=i;
        }
        ans=(f[n]%mod+mod)%mod;
        cout<<ans<<endl;
    }
    return 0;
}
```

## 矩阵优化

我们可以发现，朴素DP的DP状态可以写成矩阵形式：
$$
\begin{bmatrix}dp[i][a]
\\ dp[i][b]
\\ dp[i][c]
\\ ...
\\ ...
\\ ...
\\ dp[i][z]
\\1
\end{bmatrix}
$$
这里我们为了方便的进行矩阵转移添加了一维，$dp[i][c]$指对于前$i$个字符，最后一个字符是$c$的子序列个数。

那么字符串第$i$位的字符$s[i]$的状态可以这样转移：$dp[i][s[i]]=\sum dp[i-1][任意字符]+1$。

其他情况这样转移：$dp[i][c]=dp[i-1][c]$

我们发现这样的转移是可以构造出转移矩阵的。那么对于字符"c"的转移矩阵应该是这样的：
$$
\begin{bmatrix}
 1&0 &0  &0  &0  &0  &0 &0\\ 
 0&1  &0  &0  &0  &0  &0 &0\\ 
 1&1 &1  &1  &1  &1  &1 &1\\ 
 0&0  &0  &1  &0  &0  &0 &0\\ 
  0&0  &0  &0  &1  &0  &0 &0\\ 
 0&0  &0  &0  &0  &1  &0 &0\\ 
&&&...\\
 0&0  &0  &0  &0  &0  &0 &1
\end{bmatrix}
$$
就是在单位矩阵上，将字符"c"对应的那一行（第三行）全部设置为1。

转移矩阵的最后一列比较特殊，我们使用最后一列来达到“+1”的效果。

___

构造出每个字符对应的转移矩阵后。我们每次只需要把状态矩阵乘上第$i$个字符$s[i]$的转移矩阵，就能得到新的转移矩阵。

### 普通情况下的合并子序列状态

上面这个矩阵最主要的作用就是拿来合并两个字符串的子序列状态。因为一个子序列状态是一个状态矩阵乘上所有转移矩阵的乘积。所以如果我们分别知道字符串$s$和$t$的转移矩阵乘积，我们就能轻松的得出$st$的转移矩阵乘积，从而得到字符串$st$的本质不同子序列个数。

但是这样显然是亏本的。一次合并的复杂度为$O(len\times q^3)$。其中$q$为字符集大小。直接$O(n)$DP一次不知道比它高到哪里去了。

### 特殊情况下的合并子序列状态

但是这种矩阵合并还是有一些用处的。当新的序列出现某种规律时（例如新串=旧串*2），我们就可以“重复使用”这个矩阵。显然是赚了的。