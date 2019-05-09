# [SPOJ-TTM]To the moon

## 题面

### Background 

To The Moon is a independent game released in November 2011, it is a role-playing adventure game powered by RPG Maker. 
The premise of To The Moon is based around a technology that allows us to permanently reconstruct the memory on dying man. In this problem, we'll give you a chance, to implement the logic behind the scene. 

You‘ve been given N integers A [1], A [2],..., A [N]. On these integers, you need to implement the following operations: 
1. C l r d: Adding a constant d for every {A i | l <= i <= r}, and increase the time stamp by 1, this is the only operation that will cause the time stamp increase. 
2. Q l r: Querying the current sum of {A i | l <= i <= r}. 
3. H l r t: Querying a history sum of {A i | l <= i <= r} in time t. 
4. B t: Back to time t. And once you decide return to a past, you can never be access to a forward edition anymore. 
.. N, M ≤ 10 5, |A [i]| ≤ 10 9, 1 ≤ l ≤ r ≤ N, |d| ≤ 10 4 .. the system start from time 0, and the first modification is in time 1, t ≥ 0, and won't introduce you to a future state.

### Input

n m 
A 1 A 2 ... A n 
... (here following the m operations. )

### Output

... (for each query, simply print the result. )

### Sample Input

```
10 5
1 2 3 4 5 6 7 8 9 10
Q 4 4
Q 1 10
Q 2 4
C 3 6 3
Q 2 4
```

## 思路

因为这道题不依赖前缀查询，所以我们可以像线段树一样在建树的时候就赋上值。

- 对于操作1,我们可以打个tag。注意，这里为了方便写一个永久tag，不下传，只在求和的时候直接累加。（具体看代码）

- 对于操作2,我们可以直接像线段树一样查询。
- 对于操作3,在时刻$i$,我们只需要在2的基础上把初始节点改成$rt[i]$即可。
- 把记录最新版本的指针指向还原时刻$t$即可。

### 代码

```cpp
#include<string>
#include<cstring>
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#define ll long long
#define maxn (int)(1e5+2)
using namespace std;
int n,m,rt[maxn],rs[maxn*29],ls[maxn*29];
ll sum[maxn*29];
int tag[maxn*29],idx;
int build(int l,int r){
    int now=++idx;
    if(l==r){scanf("%lld",&sum[now]);return now;}
    int mid=(l+r)>>1;
    ls[now]=build(l,mid);rs[now]=build(mid+1,r);sum[now]=sum[ls[now]]+sum[rs[now]];
    return now;
}
int update(int pre,int l,int r,int tl,int tr,ll add){
    int now=++idx;
    rs[now]=rs[pre],ls[now]=ls[pre],tag[now]=tag[pre];
    sum[now]=sum[pre]+add*(min(r,tr)-max(tl,l)+1);
    int mid=(l+r)>>1;
    if(tl<=l&&r<=tr){tag[now]=tag[pre]+add;return now;}
    if(tl<=mid){ls[now]=update(ls[pre],l,mid,tl,tr,add);}
    if(tr>=mid+1){rs[now]=update(rs[pre],mid+1,r,tl,tr,add);}
    return now;
}
ll query(int now,int l,int r,int tl,int tr,ll add){
    if(tl<=l&&r<=tr){return add*(r-l+1)+sum[now];}
    add+=tag[now];ll ans=0;int mid=(l+r)>>1;
    if(tl<=mid)ans+=query(ls[now],l,mid,tl,tr,add);
    if(tr>=mid+1)ans+=query(rs[now],mid+1,r,tl,tr,add);
    return ans;
}
int main(){
    //freopen("in","r",stdin);
    int t=0;int flag=1;
    while(~scanf("%d%d",&n,&m)){
    t=0,idx=0;
     if(!flag)printf("\n");
flag=0;
    memset(tag,0,sizeof(tag));
    memset(sum,0,sizeof(sum));
    rt[0]=build(1,n);
    for(int i=1;i<=m;i++){
        char s[3];int l,r;ll d;
        scanf("%s",s);
        if(s[0]=='C'){
            scanf("%d%d%lld",&l,&r,&d);
            t++;rt[t]=update(rt[t-1],1,n,l,r,d);
        }
        else if(s[0]=='Q'){
            scanf("%d%d",&l,&r);
            printf("%lld\n",query(rt[t],1,n,l,r,0));
        }
        else if(s[0]=='H'){
            scanf("%d%d%lld",&l,&r,&d);
            printf("%lld\n",query(rt[d],1,n,l,r,0));
        }
        else if(s[0]=='B'){
            scanf("%lld",&d);t=d;
        }
    }
   
    }
    return 0;
}
```

