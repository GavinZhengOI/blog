# CF888G Xor-MST

我们只需要把所有01串按照第一位大小分类，然后把其中一类建成一棵字典树，再把第二类的每个数字丢进字典树里暴力查找，寻找第一位不同的两堆树连一条边的最小权值，把这个值累加进答案，再分治处理每类数即可。

因为一共需要处理$\log n$次，每次处理$n$个数，处理每个数复杂度为$\log n$。总复杂度$O(n\log ^2n)$

代码：

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<vector>
#include<cstring>
#define ll long long
#define maxn 200500
#define node vector<int>
using namespace std;
int n,ch[maxn*20][2],ori[maxn],tot;
void insert(int num) {
    int p=30,now=0;
    while(p>=0) {
        int digit=0;
        if((1<<p)&num)digit=1;
        if(!ch[now][digit]){ch[now][digit]=++tot;ch[tot][1]=ch[tot][0]=0;}
        now=ch[now][digit];p--;
    }
    return;
}
int query(int num) {
    int p=30;int ans=0;int now=0;
    while(p>=0) {
        int digit=0;
        if(num&(1<<p))digit=1;
        if(ch[now][digit])now=ch[now][digit];
        else{now=ch[now][digit^1];ans|=(1<<p);}
        p--;
    }
    return ans;
}
ll solve(node a,int p) {
   if(p<0||!a.size())return 0;
   node b1,b0;
   for(int i=0;i<a.size();i++) {
        if(a[i]&(1<<p))b1.push_back(a[i]);
        else b0.push_back(a[i]);
   }
   int mi=0;
   if(b0.size()&&b1.size()){mi=1e9+1000;tot=0;ch[0][1]=ch[0][0]=0;
   for(int i=0;i<b0.size();i++)insert(b0[i]);
   for(int i=0;i<b1.size();i++)mi=min(mi,query(b1[i]));}
   return mi+solve(b0,p-1)+solve(b1,p-1);
}
int main() {
    scanf("%d",&n);node v;for(int i=1;i<=n;i++){scanf("%d",&ori[i]);v.push_back(ori[i]);}
    printf("%lld",solve(v,30));
}
```

