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

我们令$f[i]=\sum_{j=1}^q dp[last[q]]$。其中$q$为字符集大小

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

