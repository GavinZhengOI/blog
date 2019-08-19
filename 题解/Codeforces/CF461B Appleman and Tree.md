# CF461B Appleman and Tree

[传送门](https://www.luogu.org/problem/CF461B)

一道比较容易的树形DP。

考虑用$dp[i][1]$代表将$i$分配给$i$的子树内黑点的方案数，$dp[i][0]$代表将$i$分配给$i$的子树之外的黑点的方案数。

首先，考虑分给$i$的子树黑点的情况：$dp[i][1]=\sum _{p\in son_i} dp[p][0]\prod _{t\in son_i,t\neq p} (dp[t][1]+dp[t][0])$

再考虑分给其他子树的情况：$dp[i][0]=\prod _{t\in son_i}(dp[t][0]+dp[t][1])$

特别的，当这个点是黑点时，$dp[i][0]=0,dp[i][1]=\prod_{t\in son_i} (dp[t][1]+dp[t][0])$

初始化一个点最深的点时，如果点为白点$dp[i][0]=1,dp[i][1]=0$，否则$dp[i][0]=0,dp[i][1]=1$

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<vector>
#define ll long long
#define mod 1000000007
#define maxn (int)(1e5+100)
using namespace std;
ll dp[maxn][2],n;
ll pre[maxn],suc[maxn];
vector<int>side[maxn];
bool col[maxn];
int cnt,list[maxn];
void dfs(int now,int fa) {
    if(side[now].size()==1&&fa!=0) {
        if(col[now])dp[now][0]=0,dp[now][1]=1;
        else dp[now][0]=1,dp[now][1]=0;
        return;
    }
    dp[now][0]=1,dp[now][1]=0,pre[0]=1;
    for(int i=0;i<side[now].size();i++) {
        int v=side[now][i];if(v==fa)continue;
        dfs(v,now);
        dp[now][0]*=dp[v][0]+dp[v][1];dp[now][0]%=mod;
    }
    cnt=0;
    for(int i=0;i<side[now].size();i++) {
        int v=side[now][i];if(v==fa)continue;
        list[++cnt]=v;
    }
    suc[cnt+1]=1;
    if(col[now]){dp[now][1]=dp[now][0];dp[now][0]=0;return;}
    for(int i=1;i<=cnt;i++) {pre[i]=pre[i-1]*(dp[list[i]][0]+dp[list[i]][1]);pre[i]%=mod;}
    for(int i=cnt;i>=1;i--) {suc[i]=suc[i+1]*(dp[list[i]][0]+dp[list[i]][1]);suc[i]%=mod;}
    for(int i=1;i<=cnt;i++) {
       dp[now][1]+=dp[list[i]][1]*((pre[i-1]*suc[i+1])%mod);dp[now][1]%=mod;
    }
    return;
}
int main() {
    ios::sync_with_stdio(0);
    cin>>n;
    for(int i=2;i<=n;i++) {
        int u;cin>>u;u++;side[i].push_back(u);side[u].push_back(i);
    }
    for(int i=1;i<=n;i++)cin>>col[i];
    dfs(1,0);
    cout<<dp[1][1];
    return 0;
}
```

