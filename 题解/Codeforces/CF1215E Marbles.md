# CF1215E Marbles

[传送门](https://codeforces.com/problemset/problem/1215/E)

## 思路

一道比较有意思的状压dp。

首先有一个结论，**把一个序列通过交换相邻元素排序，那么交换次数的最小值就是逆序对个数**。

证明：从小到大依次把元素换到最前面，那么每次交换都会使逆序对个数-1。逆序对为0则为有序。

考虑引入一个$a_{c_i}$代表$c_i$这种颜色在序列中的排名。

考虑按照$a_i$大小依次考虑每个$c$。dp数组中存储了以及被用过的$c$。而$dp[3]=d[(101)_2]$代表$c=3\space or\space c=1$的放在序列前两个位置，最少需要交换多少次。

对于$dp[i]$，我们枚举从哪个状态加入一种$c$转移过来，令来源状态为$j$，加入的颜色为$k$。$i$在$j$的基础上多了一个1。那么显然$dp[i]=min(dp[j]+\sum_{v\in j}w[k][v])$。其中$w[k][v]$就是原序列中$k,v$形成的逆序对个数（令$a_k>a_v$）。

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#define ll long long
const int maxn=4e5+100;
using namespace std;
int n,a[maxn],cnt[22];
ll w[22][22],dp[1<<21],sum;
int main(){
	ios::sync_with_stdio(0);
	cin>>n;int set=1<<21, mx = 0;
	for(int i=1;i<=n;i++)cin>>a[i], mx = max(mx, a[i]);
	for(int i=1;i<=n;i++){
		cnt[a[i]]++;
		for(int j=0;j<=20;j++){
			if(j==a[i])continue;
			w[j][a[i]]+=cnt[j];
		}
	}
	set = 1 << (mx + 1);
	for(int i=1;i<set;i++){
		dp[i]=1e18;
		for(int j=0;j<=mx;j++){
			if((1<<j)&i){
				int tmp=i^(1<<j);
				sum=0;
				for(int k=0;k<=mx;k++){
					if(((1<<k)&tmp)){
						sum+=w[j][k];
						if (dp[tmp] + sum >= dp[i]) break;	
					}
				}
				dp[i]=min(dp[i],dp[tmp]+sum);
			}
		}
	}
	cout<<dp[set-1];
	return 0;
}
```

