# [SPOJ-COT]Count on a tree

## 题面

You are given a tree with **N** nodes. The tree nodes are numbered from **1** to **N**. Each node has an integer weight.

We will ask you to perform the following operation:

- **u v k** : ask for the kth minimum weight on the path from node **u** to node **v**

### Input

In the first line there are two integers **N** and **M**. (**N, M** <= 100000)

In the second line there are **N** integers. The ith integer denotes the weight of the ith node.

In the next **N-1** lines, each line contains two integers **u** **v**, which describes an edge (**u**, **v**).

In the next **M** lines, each line contains three integers **u** **v** **k**, which means an operation asking for the kth minimum weight on the path from node **u** to node **v**.

### Output

For each operation, print its result.

### Example

```
Input:
8 5
105 2 9 3 8 5 7 7
1 2
1 3
1 4
3 5
3 6
3 7
4 8
2 5 1
2 5 2
2 5 3
2 5 4
7 8 2 
Output:
2
8
9
105
7 
```

## 思路

这是一个把主席树推广到树上的题。我们首先思考一下，普通的主席树/权值线段树只能对线性的区间进行操作，我们要做的，就是把一棵树分解成一个线性的结构，同时便于进行题目规定的查询操作。

因为这道题是求路径到LCA上的k小值。我们就可以把每个节点到根节点的路径建一棵树。每个节点的子节点对应的线段树可以从父节点线段树继承得到。复杂度$n\log_2 n$。

那解法就很显然了，我们先对根节点建一个权值线段树，然后按照DFS序依次遍历整棵树，将父节点继承下来，成为一颗新的树。

对于查询$(i,j,k)$，我们只需要求出一棵包含$i$到$j$所有点权值的权值线段树，按照k小值标准查询方式查询即可。

那么我们设这棵线段树为$T_a$，从节点$i$到根节点的权值线段树为$T_i$。那么很显然：
$$
T_a=T_i+T_j-T_{LCA(i,j)}-T_{fa[LCA(i,j)]}
$$
注意，至于为什么最后一项是$fa[LCA(i,j)]$，仔细想一想就能发现如果两个都是$LCA(i,j)$，会把$LCA(i,j)$多减一次。

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
using namespace std;
#define maxn (int)(1e5+1000)
int
n,m,idx,len,ls[maxn*21],rs[maxn*21],sum[maxn*21],a[maxn],sorted[maxn],head[maxn],cnt,rt[maxn],f[maxn];
struct gg{
    int u,v,next;
}side[maxn*2];
int getid(int x){
    return lower_bound(sorted+1,sorted+1+len,x)-sorted;
}
void insert(int u,int v){
    gg q={u,v,head[u]};side[++cnt]=q;head[u]=cnt;
    return;
}
int build(int l,int r){
    int now=++idx;
    if(l==r)return now;
    int mid=(l+r)>>1;
    ls[now]=build(l,mid);
    rs[now]=build(mid+1,r);
    return now;
}
int update(int last,int pos,int l,int r){
    int now=++idx;
    rs[now]=rs[last],ls[now]=ls[last],sum[now]=sum[last]+1;
    if(l==r){
        return now;	
    }
    int mid=(l+r)>>1;
    if(pos<=mid){
        ls[now]=update(ls[last],pos,l,mid);
    }
    else{
        rs[now]=update(rs[last],pos,mid+1,r);
    }
    return now;
}
void dfs(int now,int fa){
    f[now]=fa;
    rt[now]=update(rt[fa],getid(a[now]),1,len);//test
    for(int i=head[now];i;i=side[i].next){
        int v=side[i].v;
        if(v==fa)continue;
        dfs(v,now);
    }
    return;
}
int query(int p1,int p2,int p3,int p4,int k,int l,int r){//i,j,lca,fa[lca]
    int tot=sum[ls[p1]]+sum[ls[p2]]-sum[ls[p3]]-sum[ls[p4]];
    int mid=(l+r)>>1;
    if(l==r)return l;
    if(k<=tot){
        return query(ls[p1],ls[p2],ls[p3],ls[p4],k,l,mid);
    }
    else{
        return query(rs[p1],rs[p2],rs[p3],rs[p4],k-tot,mid+1,r);
    }
    return 0;
}
/*-----------------------*/
int fa[maxn][30],dep[maxn];
void dfs1(int u, int f) {
    dep[u] = dep[f] + 1, fa[u][0] = f;
    for (int i = 1;i <= 20;i++) fa[u][i] = fa[fa[u][i - 1]][i - 1];
    for (int i = head[u];i;i = side[i].next)
        if (side[i].v != f) dfs1(side[i].v, u);
}
int lca(int a1, int b1) {
    if (dep[a1] < dep[b1]) swap(a1, b1);
    for (int i = 20;i >= 0;i--) if (dep[fa[a1][i]] >= dep[b1]) a1 = fa[a1][i];
    if (a1 == b1) return a1;
    for (int i = 20;i >= 0;i--) if (fa[a1][i] != fa[b1][i]) a1 =
        fa[a1][i],b1 = fa[b1][i];
    return fa[a1][0];
}

/*------------------------*/
int main(){
    //freopen("in","r",stdin);
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++){
        scanf("%d",&a[i]);sorted[i]=a[i];
    }
    sort(sorted+1,sorted+1+n);
    len=unique(sorted+1,sorted+1+n)-sorted-1;
    for(int i=1;i<n;i++){
        int u,v;scanf("%d%d",&u,&v);
        insert(u,v);insert(v,u);
    }
    rt[0]=build(1,len);
    dfs(1,0);dfs1(1,0);
    for(int i=1;i<=m;i++){
        int u,v,k;scanf("%d%d%d",&u,&v,&k);
        int ans=query(rt[u],rt[v],rt[(lca(u,v))],rt[f[lca(u,v)]],k,1,len);
        printf("%d\n",sorted[ans]);
    }
    return 0;
}
```

