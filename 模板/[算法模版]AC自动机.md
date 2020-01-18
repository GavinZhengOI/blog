# [算法模版]AC自动机

## 基础内容

板子不再赘述，[OI-WIKI](https://oi-wiki.org/string/ac-automaton/)有详细讲解。

$query$函数则是遍历文本串的所有位置，在文本串的每个位置都沿着$fail$跳到根，将沿途所有元素答案++。意义在于累计所有以当前字符为结尾的所有模式串的答案。看代码就能很容易的理解。

另外$e[i]$记录的是第$t$个模式串结尾是哪个节点（所有节点均有唯一的编号）。

贴个[P5357 【模板】AC自动机（二次加强版）](https://www.luogu.org/problem/P5357)板子：

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
#include<vector>
#define maxn (int)(2e6+10000)
int ch[(int)(2e5+1000)][30],fail[maxn],cnt,e[maxn],nex[maxn],n,queue[maxn],ans[maxn];
using namespace std;
char s[(int)(2e6+1)];
char data[maxn];
void init() {
    memset(ch,0,sizeof(ch));
    memset(fail,0,sizeof(fail));
    memset(e,0,sizeof(e));
    memset(nex,0,sizeof(nex));
    memset(ans,0,sizeof(ans));
    cnt=0;
}
void insert(int t) {
    int now=0,len=strlen(s);
    for(int i=0;i<len;i++) {
        int num=s[i]-'a';
        if(!ch[now][num])ch[now][num]=++cnt;
        now=ch[now][num];
    }
    e[t]=now;
}

void build(){
    int l=0,r=0;
    for(int i=0;i<=26;i++)if(ch[0][i])queue[++r]=ch[0][i];
    while(l<r) {
        int now=queue[++l];
        for(int i=0;i<=26;i++) {
            if(ch[now][i]) {
                queue[++r]=ch[now][i];
                fail[ch[now][i]]=ch[fail[now]][i];
            }
            else ch[now][i]=ch[fail[now]][i];
        }
    }
}
void query() {
    int now=0;
    for(int i=0;data[i];i++) {
        now=ch[now][data[i]-'a'];
        for(int j=now;j;j=fail[j])ans[j]++;
    }
}
int main() {
    init();
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%s", s);
        insert(i);
    }
    build();
    scanf("%s", data);
    query();
    for(int i=1;i<=n;i++)printf("%d\n",ans[e[i]]);
    return 0;
}
```

## last优化(引自sclbgw7)

博主懒，就不造轮子了。原文链接见参考文献。

***

上述方法将建图+匹配的复杂度成功优化为了 $𝑂(∑𝑛+𝑚)O(∑n+m) $，但是别忘了，匹配成功时的计数也是需要跳fail边的。然而，为了跳到一个结束节点，我们可能需要中途跳到很多没用的伪结束节点：

> 如果一个节点的fail指向一个结尾节点，那么这个点也成为一个（伪）结尾节点。在匹配时，如果遇到结尾节点，就进行相应的计数处理。

这里面就又有优化的余地了：对于不是真正结束节点的伪结束点，直接跳过它就好了。我们用一个last指针表示“在它顶上的fail边所指向的一串节点中，第一个真正的结束节点”。于是，每次计数处理时，我们不跳fail边，改为跳last边，省去了很多冗余操作。

获得last指针的方法也十分简单，就是在`void build()`中加一句话：

```
last[c]=end[fail[c]]?fail[c]:last[fail[c]];
```

然后匹配时的代码就变成了：

```
void count(int x)
{
    while(x)
    {
       //计数、打印等，视题目要求顶
        x=last[x];
    }
}

void match()
{
    int now=1;
    for(int i=1;s[i]!='\0';++i)
    {
        int x=s[i]-'a';
        now=ch[now][x];
        if(end[now])count(now);
        else if(last[now])count(last[now]);
    }
}
```

注意：last优化是对复杂度没有影响的小优化，但是大多数情况下效果明显，类似于搜索剪枝。

***

使用last优化后AC的代码：

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
#include<vector>
#define maxn (int)(2e6+10000)
int ch[(int)(2e5+1000)][30],fail[maxn],cnt,e[maxn],nex[maxn],n,queue[maxn],ans[maxn],last[maxn];
bool endf[maxn];
using namespace std;
char s[(int)(2e6+1)];
char data[maxn];
void init() {
    memset(ch,0,sizeof(ch));
    memset(fail,0,sizeof(fail));
    memset(e,0,sizeof(e));
    memset(nex,0,sizeof(nex));
    memset(ans,0,sizeof(ans));
    cnt=0;
}
void insert(int t) {
    int now=0,len=strlen(s);
    for(int i=0;i<len;i++) {
        int num=s[i]-'a';
        if(!ch[now][num])ch[now][num]=++cnt;
        now=ch[now][num];
    }
    e[t]=now;
    endf[now]=1;
}

void build(){
    int l=0,r=0;
    for(int i=0;i<=26;i++)if(ch[0][i])queue[++r]=ch[0][i];
    while(l<r) {
        int now=queue[++l];
        for(int i=0;i<=26;i++) {
            if(ch[now][i]) {
                queue[++r]=ch[now][i];
                if(endf[ch[fail[now]][i]]) {
                    last[ch[now][i]]=ch[fail[now]][i];
                }
                else last[ch[now][i]]=last[ch[fail[now]][i]];
                fail[ch[now][i]]=ch[fail[now]][i];
            }
            else ch[now][i]=ch[fail[now]][i];
        }
    }
}
void query() {
    int now=0;
    for(int i=0;data[i];i++) {
        now=ch[now][data[i]-'a'];
        for(int j=now;j;j=last[j])ans[j]++;
    }
}
int main() {
    init();
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%s", s);
        insert(i);
    }
    build();
    scanf("%s", data);
    query();
    for(int i=1;i<=n;i++)printf("%d\n",ans[e[i]]);
    return 0;
}
```



## AC自动机+DP

咕咕咕