# [Codeforces1250E] The Coronation

[ The Coronation](https://codeforces.com/contest/1250/problem/E)

又是一道并查集。。。最近做的并查集咋这么多。。。

## 思路

首先，维护元素间关系的题想到并查集。

因为这里涉及到“翻转”操作。所以我们把反转过后的点$i$设为$i'$，令$i'=i+n$。然后使用拆点并查集来计算。

我们把需要同时满足的条件放入一个并查集。然后对于任意两个串，都有四种情况：

1. 两个串无论转不转都不相似，显然无解，直接输出-1即可。
2. 两个串无论转不转都相似，因为这两个串之间没有相互牵制的限制，所以不管。
3. 两个串相似当且仅当其中一个翻转。那么这时候将$i,j+n$合并，将$j,i+n$合并。（代表了如果取$i$ , $j+n$必须取，反之亦然）
4. 两个串相似当且仅当没有一个翻转，那么这时候将$i,j$合并，将$i+n,j+n$合并。（理由同上）

这样我们就维护了并查集之间的关系。而题目求的就是在满足上述条件情况下，把所有字母代表的点都取到，形如$i'$的字母取的最少。我们可以给并查集加权来维护这个东西。

又因为对于$i$处于的集合，这个集合和$i'$所在的集合的唯一区别是所有字母取反($i$变成$i'$，$i'$变成$i$)。

所以对于这两个集合，无论取哪个，能取到的字母都是一样的。而同种字母$i$和$i'$只能取一个。所以取哪个集合效果等价，那我们就贪心的取权值最小的那个集合。

具体看代码实现吧。

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cmath>
#include<algorithm>
#include<vector>
using namespace std;
int map[55][55],size[105],n,m,k,fa[105];
bool visit[105];
char s[55];//i=keep i'=i+n=reverse
vector<int>ans;
int check(int u,int v) {
    int flag=0;int m1=0,m2=0;
    for(int i=1;i<=m;i++){if(map[u][i]^map[v][i])flag++;}
    if(flag<=k){m1=1;}
    flag=0;
    for(int i=1,j=m;i<=m;i++,j--){if(map[u][i]^map[v][j])flag++;}
    if(flag<=k){m2=1;}
    if(m1&&m2)return 0;
    else if(m1&&!m2)return 2;
    else if(!m1&&m2)return 1;
    else return -1;
}
int get(int x){return (fa[x]==x)?x:(fa[x]=get(fa[x]));}
void uni(int x,int y){size[get(y)]+=size[get(x)];fa[get(x)]=get(y);}
void solve() {
    ans.clear();
    memset(map,0,sizeof(map));
    memset(size,0,sizeof(size));
    memset(visit,0,sizeof(visit));
    scanf("%d%d%d",&n,&m,&k);k=m-k;
    for(int i=1;i<=n;i++) {
        scanf("%s",s+1);
        for(int j=1;j<=m;j++)map[i][j]=(s[j]=='1')?1:0;
    }
    for(int i=1;i<=2*n;i++)fa[i]=i;
    for(int i=n+1;i<=2*n;i++)size[i]=1;
    for(int i=1;i<=n;i++) {
        for(int j=i+1;j<=n;j++) {
            int tmp=check(i,j);
            if(tmp==-1){printf("-1\n");return;}
            else if(tmp==1){
                if(get(i)==get(i+n)||get(j)==get(j+n)){printf("-1\n");return;}
                if(get(i)!=get(j+n)){uni(i,j+n);}
                if(get(j)!=get(i+n)){uni(j,i+n);}
            }
            else if(tmp==2){if(get(i)!=get(j))uni(i,j);if(get(i+n)!=get(j+n))uni(i+n,j+n);}
        }
    }
    for(int i=1;i<=n;i++) {
        if(get(i)==get(i+n)){printf("-1\n");return;}
        else if(visit[get(i)])continue;
        else if(visit[get(i+n)]){ans.push_back(i);continue;}
        else if(size[get(i)]>size[get(i+n)]){visit[get(i+n)]=1;ans.push_back(i);}
        else {visit[get(i)]=1;}
    }
    printf("%d\n",ans.size());
    for(int i=0;i<ans.size();i++)printf("%d ",ans[i]);
    printf("\n");
}
int main() {
    int t;scanf("%d",&t);
    while(t--)solve();
}
```



## Tips

对于这类维护元素间两两关系（改变一个会同时改变其他的），很容易使用拆点并查集维护。需要注意的是每当我们得到一个信息。我们需要把这个信息能得到的**所有**关系都体现在并查集中。也就是说要把开出来的虚点当作实际点来处理。举个例子，如果上面的第四种情况只合并了$i,j$没有合并$i+n,j+n$。会得到WA11的好成绩。