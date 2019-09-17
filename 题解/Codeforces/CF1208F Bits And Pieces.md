# CF1208F Bits And Pieces

[传送门](https://codeforces.com/contest/1208/problem/F)

## 思路

这里要运用SOS-DP的思路（$\text{Sum over Subsets}$）。我在另外一篇博客里介绍过，如有需要可以搜索一下我的博客。

SOS-DP是记录每个$mask$所有子集的信息，而这道题只需要记录每个$mask$所有超集的信息即可。$dp[mask]$记录的是能满足$mask$是其的子集的所有数中坐标最大的两个。我们枚举$a_i$，然后贪心的按位取就可以了。具体细节可以看代码。

## 代码

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
#define maxn (int)(5194304)
using namespace std;
int n,a[maxn],mi=0;
struct gg{
	int key1,key2;
	gg(int k1=0,int k2=0){key1=k1,key2=k2;}
}dp[maxn];
void insert(int num,int val){
	if(val>dp[num].key1){
		dp[num].key2=dp[num].key1;
		dp[num].key1=val;
	}
	else if(val>dp[num].key2){
		dp[num].key2=val;
	}
	return;
}
void init(){
	for(int i=1;i<=n;i++)insert(a[i],i);
	for(int i=0;i<=22;i++){
		for(int j=0;j<=2e6;j++){
			if((1<<i)&j){
				insert((1<<i)^j,dp[j].key1);
				insert((1<<i)^j,dp[j].key2);
			}
		}
	}
}
bool test(int mask,int num){
	if(dp[mask].key2>=num)return 1;
	return 0;	
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;i++)scanf("%d",&a[i]);
	init();
	for(int i=1;i<=n-2;i++){
		int check=0;
		for(int j=21;j>=0;j--){
			if((1<<j)&a[i])continue;
			if(test(check^(1<<j),i))check^=(1<<j);
		}
		mi=max(mi,check|a[i]);
	}
	printf("%d",mi);
	return 0;
}

```



## 参考资料

[CodeForces -1208F Bits And Pieces](https://blog.csdn.net/Ratina/article/details/100100711)