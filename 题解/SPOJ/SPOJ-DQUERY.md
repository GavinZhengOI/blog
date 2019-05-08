# [SPOJ-DQUERY]D-query

## 题面

Given a sequence of n numbers a1, a2, ..., an and a number of d-queries. A d-query is a pair (i, j) (1 ≤ i ≤ j ≤ n). For each d-query (i, j), you have to return the number of distinct elements in the subsequence ai, ai+1, ..., aj.

### Input

- Line 1: n (1 ≤ n ≤ 30000).
- Line 2: n numbers a1, a2, ..., an (1 ≤ ai ≤ 106).
- Line 3: q (1 ≤ q ≤ 200000), the number of d-queries.
- In the next q lines, each line contains 2 numbers i, j representing a d-query (1 ≤ i ≤ j ≤ n).

### Output

- For each d-query (i, j), print the number of distinct elements in the subsequence ai, ai+1, ..., aj in a single line.

  

### Example

```
Input
5
1 1 2 1 3
3
1 5
2 4
3 5

Output
3
2
3 
```

## 思路

这道题还是在主席树上进行一些操作。当拿到一个新的元素时，如果这个元素没被插入过，我们就直接插入。反之则删除这个数再插入一次这个数。也就是说，我们既能保证序列中有相同元素时，先插入位置靠后的（前面的删掉再插入相当于把插入位置向后挪），又能保证每一个元素在线段树中只有一个。

这样我们在查询区间[l,r]时，只需要查询一下第r棵线段树中[l,r]的区间和即可。

注意，这里我们是以每个数列的每个位置作为叶子节点。跟权值线段树有所不同。

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#define maxn (int)(3e4+1000)
using namespace std;
int
q,sum[maxn*21],rs[maxn*21],ls[maxn*21],len,n,a[maxn],sorted[maxn],flag[maxn],rt[maxn],idx;
int getid(int x){
	return lower_bound(sorted+1,sorted+1+len,x)-sorted;
}
int build(int l,int r){
	int now=++idx;
	if(l==r)return now;
	int mid=(l+r)>>1;
	ls[now]=build(l,mid);
	rs[now]=build(mid+1,r);
	return now;
}
int update(int last,int pos,int l,int r,int val){
	int now=++idx;
	ls[now]=ls[last];rs[now]=rs[last];sum[now]=sum[last]+val;
	if(l==r)return now;
	int mid=(l+r)>>1;
	if(pos<=mid){
		ls[now]=update(ls[last],pos,l,mid,val);
	}
	else{
		rs[now]=update(rs[last],pos,mid+1,r,val);
	}
	return now;
}
int query(int x,int l,int r,int pos){
	if(l==r)return sum[x];
	int mid=(l+r)>>1;
	if(pos<=mid){	
		return sum[rs[x]]+query(ls[x],l,mid,pos);
	}
	else{
		return query(rs[x],mid+1,r,pos);
	}
	return 0;
}
int main(){
	//freopen("in","r",stdin);
	scanf("%d",&n);
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);sorted[i]=a[i];
	}
	sort(sorted+1,sorted+1+n);
	len=unique(sorted+1,sorted+1+n)-sorted-1;
	rt[0]=build(1,len);
	for(int i=1;i<=n;i++){
		int num=getid(a[i]);
		if(!flag[num]){
			rt[i]=update(rt[i-1],i,1,n,1);
		}
		else{
			int tmp=update(rt[i-1],flag[num],1,n,-1);
			rt[i]=update(tmp,i,1,n,1);
		}
		flag[num]=i;
	}	
	scanf("%d",&q);
	for(int i=1;i<=q;i++){
		int j,k;scanf("%d%d",&j,&k);
		printf("%d\n",query(rt[k],1,n,j));
	}
	return 0;
}	
```



