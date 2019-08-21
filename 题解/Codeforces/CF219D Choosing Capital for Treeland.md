# CF219D Choosing Capital for Treeland

这道题一开始觉得比较毒，但是想一下还是想的出来的。

我们考虑定义$dp[i][0]$代表到点$i$所有子节点的最小权值，$dp[i][1]$代表到点$i$子树外所有节点的最小权值。

那么显然有：$dp[i][0]=\sum _{t\in son_i} dp[t][0]+[t->i]$

当$fa_i=p$，又可以推出：$dp[i][1]=dp[p][1]+[i->p]+\sum _{t\in son_p,t\neq i}(dp[t][0]+[t->p])$

那么最后，对于节点$x$，选择$x$为首都的代价就是：$dp[x][0]+dp[x][1]$

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<vector>
#include<map>
#include<unordered_map>
#define ll long long
#define maxn  (int)(2e5+1000)
using namespace std;
struct node {
    int u,v;
};
struct cop {
  bool operator()(const node a,const node b) const{
    return (a.u==b.u)?(a.v<b.v):(a.u<b.u);
  }
};
int dp[maxn][2],n,ans[maxn],cnt,mi=1e9+1000;
vector<int>side[maxn];
map<node,bool,cop>check;
ll sum[maxn];
bool test(int u,int v) {
    struct node a={u,v};
    return check[a];
}
void dfs1(int now,int fa) {
    for(int i=0;i<side[now].size();i++) {
        int v=side[now][i];if(v==fa)continue;
        dfs1(v,now);dp[now][0]+=dp[v][0]+test(v,now);
    }
}
void dfs2(int now,int fa) {
    if(now==1){dp[now][1]=0;}
    else {
        dp[now][1]=dp[fa][1]+test(fa,now)+(sum[fa]-(dp[now][0]+test(now,fa)));
    }
    int tmp=dp[now][0]+dp[now][1];
    if(tmp<mi) {
        cnt=1,ans[cnt]=now,mi=tmp;
    }
    else if(tmp==mi) {
        ans[++cnt]=now;
    }
    for(int i=0;i<side[now].size();i++) {
        int v=side[now][i];if(v==fa)continue;
        sum[now]+=dp[v][0]+test(v,now);
    }
    for(int i=0;i<side[now].size();i++) {
        int v=side[now][i];if(v==fa)continue;
        dfs2(v,now);
    }
}
int main() {
    cin>>n;
    for(int i=1;i<n;i++) {
        int u,v;cin>>u>>v;side[u].push_back(v);side[v].push_back(u);
        struct node a={u,v};check[a]=1;
    };
    dfs1(1,0);
    dfs2(1,0);
    sort(ans+1,ans+1+cnt);
    cout<<mi<<endl;
    for(int i=1;i<=cnt;i++)cout<<ans[i]<<' ';
    return 0;
}
```

