# CF852A Digits

隔壁yijian大佬写出了正解。那我就写一个随机化大法吧？

我们先考虑一种错误的贪心，每个数字分成一位,使其分割后数字和最小。虽然这样是错的，但是我们发现错误的概率很小，所以我们可以每次随机一个数字一位或者两个数字一位。后者的概率调整在百分之一左右。我们用这样的方法做出第一次分割，剩余的两次分割都每个数字一位即可。最后判断一下是否满足条件，如果不满足就重新跑一遍随机化。

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<time.h>
#include<string>
#include<stdlib.h>
#define MAX 100
using namespace std;
char s[200500];
int ans[200500],cnt=0,n,nums;
int data[3][200500],add[3];
string output;
int rd() {
    int num=rand()%MAX;
    if(num==0)return 2;
    else return 1;
}
bool generate() {
    cnt=0,nums=0;output.clear();
    for(int i=0;i<n;i++) {
            if(i!=0)output.push_back('+');
            if(i!=n-1&&rd()==2) {
                ans[++cnt]=(s[i]-'0')*10+s[i+1]-'0';
                output.push_back(s[i]);output.push_back(s[i+1]);
                i++;
            }
            else {
                ans[++cnt]=s[i]-'0';
                output.push_back(s[i]);
            }
    }
    return 1;
}
void out() {
    cout<<output;
    printf("\n");
    for (int i = add[1]; i >=1; i--) {
        printf("%d", data[1][i]);
        if (i != 1)printf("+");
    }
    printf("\n");
    for (int i = add[2]; i >=1; i--) {
        printf("%d", data[2][i]);
        if (i != 1)printf("+");
    }
    return;
}
int main() {
    srand((unsigned)(time(NULL)));
    scanf("%d%s",&n,s);
    while(generate()) {
        long long tot=0,trans;add[0]=add[1]=add[2]=0;
        for(int i=1;i<=cnt;i++)tot+=ans[i];trans=tot;tot=0;
        while(trans){tot+=trans%10;data[1][++add[1]]=trans%10;trans/=10;}trans=tot;tot=0;
        while(trans){tot+=trans%10;data[2][++add[2]]=trans%10;trans/=10;}
        if(tot<=9){out();return 0;}
        else continue;
    }
}
```