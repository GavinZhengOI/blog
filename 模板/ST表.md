# [算法模板]ST表

ST表和线段树一样，都能解决RMQ问题（范围最值查询-Range Minimum Query）。

我们开一个数组数组$f[maxn][maxn\log_2]$来储存数据。

定义$f[i][j]$代表从$i$开始的$2^{j}$位这个区间的最大值。

## 初始化

因为$f[i][0]=a[i]$，所以有：
$$
f[i][j]=max(f[i][j-1],f[i+2^{j-1}][j-1])
$$
通过这个转移方程即可构造出$f$。

## 查询

查询区间$[l,r]$的最大值。

令$k=\log _2(l-r+1)$，那么很容易想明白：
$$
ans=max(f[l][k],f[r-2^k+1][2^k])
$$
注意定义$f$的时候，$i$是包含在$2^k$里面的，所以可能会+1/-1。

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
using namespace std;
#define maxn (int)(1e5+1000)
int a[maxn],f[maxn][21],n,m;
void init(){
	for(int i=1;i<=n;i++)f[i][0]=a[i];
	for(int j=1;j<=20;j++){
		for(int i=1;i-1+(1<<(j-1))<=n;i++){
			f[i][j]=max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
		}
	}
	return;
}
int query(int l,int r){
	int k=log2(r-l+1);
	return max(f[l][k],f[r-(1<<(k))+1][k]);
}
int main(){
	//freopen("in","r",stdin);
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
	}
	init();
	for(int i=1;i<=m;i++){
		int l,r;scanf("%d%d",&l,&r);
		printf("%d\n",query(l,r));
	}
	return 0;
}
```

