# [算法模版]Prim-完全图最小生成树

众所周知，对于常用的Kruskal算法，算法复杂度为$O(m \log m)$。这在大多数场景下已经够用了。但是如果遇到及其稠密的完全图，Prim算法就能更胜一筹。

Prim算法也可以使用很多数据结构进行优化。但是对于完全图来说，这写优化都无足轻重。暴力的Prim算法的$O\left(n^{2}+m\right)$就足够了。

Prim算法也很简单。就是每次考虑把一个加入一个点到已经建成的生成树。可以证明如果选择一个不在当前生成树，且离当前生成树最近的点加入生成树一定最优。

例题：[【模板】最小生成树](https://www.luogu.org/problem/P3366)

```cpp
for(int i=0;i<=n;i++)dis[i]=1e18;
dis[s]=0;
while(1) {#include<iostream>
#include<cstdio>
#include<algorithm>
#include<vector>
#include<cstring>
using namespace std;
const int maxn=5005;
vector<pair<int,int> >side[maxn];
int n,m,vis[maxn];
long long dis[maxn],ans;
int main() {
    ios::sync_with_stdio(0);
    cin>>n>>m;
    for(int i=1;i<=m;i++) {
        int u,v,w;cin>>u>>v>>w;
        side[u].push_back(make_pair(v,w));
        side[v].push_back(make_pair(u,w));
    }
    memset(dis,0x3f,sizeof(dis));
    int s=1;
    dis[s]=0;//dis[i]代表一个未加入联通块的点到联通块的最近距离
    while(1) {
        int now=0;
        for(int i=1;i<=n;i++)if(!vis[i]&&dis[i]<dis[now])now=i;//取一个dis最小的（显然这个点满足通过一条边就能到达联通块，且这条边最小）
        if(!now)break;//所有点都走过 退出
        ans+=dis[now];//代表走连接联通块和now之间的边，累计答案
        vis[now]=1;//标记这个点 代表这个点并入联通块
        for(int i=0;i<side[now].size();i++) {
            int v=side[now][i].first,w=side[now][i].second;
            if(!vis[v])dis[v]=min(dis[v],1ll*w);//更新不在联通块内的点的dis
        }
    }
    cout<<ans;
}
    int now=0;
    for(int i=1;i<=n;i++)if(!vis[i]&&dis[i]<dis[now])now=i;//取一个dis最小的
        vis[now]=1;
        if(now==0)break;
        for(int i=1;i<=n;i++)if(!vis[i]){
            dis[i]=min(dis[i],get_dis(now,i));
        }
}
```

