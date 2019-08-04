# CF886E Maximum Element

[原题链接](http://codeforces.com/problemset/problem/886/E)

[洛谷链接](https://www.luogu.org/problem/CF886E)

本文公式引自mjy的课件

这里涉及到一个很有意思的trick：对于只关心相对大小的题目，我们可以只考虑相对大小。

我们先定义$f_i$为1~i的排列中有多少个是完成循环之后没有退出的（即合法序列）

那么我们可以考虑转移：$f_{j-1} \times\left(\begin{array}{c}{i-1} \\ {i-j}\end{array}\right) \times(i-j) !$

这里的含义是枚举最大值$j$（$j\in [i-k+1,i]$），那么对于最大值前面的元素一共有$f_{j-1}$种排法。这种做法的精髓在于，将$[1,n]$中任意$j-1$个数分配给前$j-1$个位置，排列方法都只有$f_{j-1}$种。（因为我们只要求相对大小，想想就知道为什么）

理解了前一部分，后面两项就很好理解了。

我们可以化一下这个式子：
$$
=f_{j-1} \times(i-1) ! \times \frac{1}{(j-1) !}
$$
再整理一下：
$$
\begin{array}{l}{f_{i}=\sum_{j=i-k+1}^{i} \frac{f_{j-1}}{(j-1) !} \times(i-1) !} \\ {=(i-1) ! \sum_{j=i-k}^{i-1} \frac{f_{j}}{j !}}\end{array}
$$
很显然，我们对$(i-1) !$和$\frac{f_{j}}{j !}$分别维护一个前缀和就好啦～

最后，因为题目中规定，即使中途退出也可能是正确的。所以我们再用相似的方法做一下就好了：
$$
a n s=n !-\sum_{i=1}^{n} f_{i-1} \times\left(\begin{array}{c}{n-1} \\ {n-i}\end{array}\right) \times(n-i) !
$$

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<cmath>
#define maxn (int)(1e6+100)
#define mod (int)(1e9+7)
#define ll long long
using namespace std;
int n,k;
ll inv[maxn],fact[maxn],pre[maxn],dp[maxn];
ll ksm(ll x,ll times) {
    ll cur=x,ans=1;
    while(times) {
        if(times&1)ans*=cur,ans%=mod;
        cur*=cur;cur%=mod;times>>=1;
    }
    return ans;
}
ll c(ll tot,ll chose) {
    return (fact[tot]*(ksm((fact[chose]*fact[tot-chose])%mod,mod-2)%mod))%mod;
}

int main() {
    scanf("%d%d",&n,&k);
    pre[0]=fact[1]=fact[0]=1;
    for(int i=2;i<maxn;i++){fact[i]=fact[i-1]*i;fact[i]%=mod;}
    for(int i=1;i<maxn;i++){inv[i]=ksm(i,mod-2);}
    //cout<<">>"<<(inv[3]*9)%mod<<endl;
    dp[1]=1;pre[1]=1;dp[0]=1;
    for(int i=1;i<=k;i++)dp[i]=fact[i],pre[i]=pre[i-1]+dp[i]*ksm(fact[i],mod-2),pre[i]%=mod;
    for(int i=k+1;i<=n;i++) {
        dp[i]=(fact[i-1]*(pre[i-1]-pre[i-k-1]+mod)%mod)%mod;
        pre[i]=pre[i-1]+dp[i]*ksm(fact[i],mod-2) % mod;pre[i]%=mod;
    }
    ll ans=0;//=fact[n]-;
    for(int i=1;i<=n;i++) {
        ans-=(((dp[i-1]*c(n-1,n-i))%mod)*fact[n-i])%mod,ans += mod;ans%=mod;
    }
    ans+=fact[n];ans%=mod;ans+=mod;ans%=mod;
    printf("%lld",ans);
    return 0;
}
```

