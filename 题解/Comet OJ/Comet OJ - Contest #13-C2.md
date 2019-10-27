# Comet OJ - Contest #13-C2

[C2-佛御石之钵 -不碎的意志-」(困难版)](https://cometoj.com/contest/72/problem/Ｃ2?problem_id=4032)

又是一道并查集。最近做过的并查集的题貌似蛮多的。

## 思路

首先考虑，每次处理矩形只考虑从0变成1的点。这样我们的复杂度就控制在了$O(nm)$。

那么，我们考虑怎么维护每个点下一个0的位置。因为我们知道**并查集是用来维护一些元素的相同关系**。所以我们考虑使每个点的$fa$为这一行下一个0的位置。那么，当我们把一个点由0染成1时，将这个点和左边的点合并。我们就能保证当前这个点的$fa$就是下一个0的位置，同时还能保证自己的“儿子”的$fa$变成了新的下一个0的位置。

那么考虑怎么动态维护联通块。

再开一个并查集维护1的联通块。我们先让所有1都给答案加1。当两个1的联通块合并时，答案减1即可。

不需要管联通顺序，每次处理一个1，都把周围能合并的合并了。（这又是并查集另一个比较重要的性质。当你每次把**能得到的关系**都合并起来后，最后就能得到所有的关系）。

复杂度$O(n m \alpha(n m)+n q)$。(加上按秩合并)

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<cmath>
using namespace std;
const int maxn=1005;
int map[maxn][maxn],fa1[maxn][maxn],fa2[maxn*maxn],id[maxn][maxn],n,m,q,tot,ans;
char s[maxn];
int get1(int x,int y) {return (fa1[x][y]==y)?y:fa1[x][y]=get1(x,fa1[x][y]);}
void uni1(int x,int u,int v){fa1[x][get1(x,u)]=get1(x,v);}
int get2(int x){return (fa2[x]==x)?x:fa2[x]=get2(fa2[x]);}
void uni2(int u,int v){fa2[get2(u)]=get2(v);}
int dx[5]={0,1,-1,0,0};
int dy[5]={0,0,0,1,-1};
void mark(int x,int y) {//color (x,y)
    id[x][y]=++tot;ans++;fa2[tot]=tot;
    if(get1(x,y)!=get1(x,y+1))uni1(x,y,y+1);
    for(int i=1;i<=4;i++) {
        int nx=x+dx[i],ny=y+dy[i];
        if(1<=nx<=n&&1<=ny<=m&&id[nx][ny]) {
            if(get2(id[nx][ny])!=get2(id[x][y])){uni2(id[nx][ny],id[x][y]);ans--;}
        }
    }
}
int main() {
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++) {
        scanf("%s",s+1);
        for(int j=1;j<=m;j++)map[i][j]=(s[j]=='1')?1:0;
    }
    for(int i=1;i<=n;i++)for(int j=1;j<=m+1;j++)fa1[i][j]=j;
    for(int i=1;i<=n;i++)for(int j=1;j<=m;j++)if(map[i][j])mark(i,j);
    scanf("%d",&q);
    for(int t=1;t<=q;t++) {
        int d[5];scanf("%d%d%d%d",&d[1],&d[3],&d[2],&d[4]);//x1 x2 y1 y2
        for(int i=d[1];i<=d[2];i++) {
            for(int j=get1(i,d[3]);j<=d[4];j=get1(i,j)) {
                mark(i,j);
            }
        }
        printf("%d\n",ans);
    }
    return 0;
}
```





