# CF1207G Indie Album

一道很有意思的AC自动机好题。

AC自动机的本质是记录了一棵字典树上所有字符串的所有后缀，所以这道题我们可以充分利用这个性质。

这道题与板子题的不同之处就在于：文本串在不停的变化，每次增加一个字符。

我们先来复习一下AC自动机模版题的流程

1. 遍历文本串的所有前缀。
2. 将所有是这个前缀的后缀的模式串出现次数+1。（即沿着fail指针一直走到根，沿途的所有节点都是符合条件的）

而对于这道题，因为文本串每次只会添加一个字符。所以我们在上一个文本串的基础上，加入以当前节点到根组成的字符串的贡献即可。

那这道题的解决方案就比较清晰了：

我们将字符串$s$定义为当前字典树节点到根的所有节点组成的字符串，文本串$s$对应的模式串出现次数的记为$t[s]$，将文本串$s$对应的答案记为$ans[s]$

1. 建出一个文本串组成的字典树。
2. 对字典树进行DFS，每走到一个节点，将所有为s后缀的字符串的出现次数$t$加1。
3. 记录答案$ans[s]=t[s]$。
4. DFS回溯时，将所有为s后缀的字符串的出现次数$t$减1。

因为所有字符串长度和$T$的大小关系，$T^2$算法是过不了的。但是因为累计答案是在$fail$指针构成的树，所以我们可以用数据结构来维护这个东西。复杂度变成了$T\log T$。

个人感觉这道题的关键在于利用AC自动机“求解单文本串问题时会遍历文本串的所有前缀”这个性质，从而求解特殊的多文本串问题。

```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<string>
#include<vector>
#define ll long long
#define maxn (int)(4*1e5+1000)
using namespace std;
int ans[maxn],ch[maxn][30],tr[maxn][30],c[maxn],queue[maxn],n,m,now_insert,tcnt,acnt,fcnt,ver_tr[maxn],fail[maxn],size[maxn],id[maxn];//id指在ac自动机上的点在fail树上的编号
bool is_end[maxn];
char s[10];
struct gg{
    int lo,id,mod_ed;string add;//版本和询问的模式串,以及模式串结尾位置在AC自动机上的编号
}query[maxn];
vector<int>tr_ver[maxn],fail_son[maxn];
vector<gg>so[maxn];//记录每个版本的询问
void change(int now,int num) {
    for(int i=now;i<=fcnt;i+=i&-i){c[i]+=num;}
}
int sum(int now) {
    int re=0;for(int i=now;i>=1;i-=i&-i){re+=c[i];}return re;
}
int insert_ac(string data) {
    int now=0;
    for(int i=0;i<data.size();i++) {
        int cha=data[i]-'a';
        if(!ch[now][cha])ch[now][cha]=++acnt;
        now=ch[now][cha];
    }
    is_end[now]=1;return now;
}
void build_ac() {
    int l=1,r=0;
    for(int i=0;i<30;i++)if(ch[0][i]){queue[++r]=ch[0][i];fail_son[0].push_back(ch[0][i]);}
    while(l<=r) {
        int now=queue[l];l++;
        for(int i=0;i<30;i++) {
            if(ch[now][i]) {
                queue[++r]=ch[now][i];
                fail[ch[now][i]]=ch[fail[now]][i];
                fail_son[ch[fail[now]][i]].push_back(ch[now][i]);
            }
            else ch[now][i]=ch[fail[now]][i];
        }
    }
}
void dfs1(int now) {
    id[now]=++fcnt;size[id[now]]=1;
    for(int i=0;i<fail_son[now].size();i++){dfs1(fail_son[now][i]);size[id[now]]+=size[id[fail_son[now][i]]];}
}
void update(int now,int num) {//更改AC自动机上now点所有儿子
    change(id[now],num);
}
void get_ans(gg data,int fail_now) {
    ans[data.id]=sum(fail_now+size[fail_now]-1)-sum(fail_now-1);
}
void dfs2(int tr_now,int ac_now) {
    if(tr_now) {
        update(ac_now, 1);//所有匹配的模式串加上1。
        for (int i = 0; i < tr_ver[tr_now].size(); i++) {//枚举以当前点结尾的所有版本
            int now_ver = tr_ver[tr_now][i];//现在的处理版本
            for (int j = 0; j < so[now_ver].size(); j++) {
                gg now_qu = so[now_ver][j];//现在处理的询问
                get_ans(now_qu,id[now_qu.mod_ed]);
            }
        }
    }
    for(int i=0;i<30;i++) {
        if(tr[tr_now][i]) {
            dfs2(tr[tr_now][i],ch[ac_now][i]);
        }
    }
    if(tr_now) {
        update(ac_now,-1);
    }
}
int main() {
    scanf("%d",&n);
    for(int i=1;i<=n;i++) {
        int ty;scanf("%d",&ty);
        if(ty==1) {
            scanf("%s",s);
            now_insert=0;
        }
        else {
            scanf("%d",&now_insert);now_insert=ver_tr[now_insert];scanf("%s",s);
        }
        int tar=s[0]-'a',now;
        if(!tr[now_insert][tar])tr[now_insert][tar]=++tcnt;
        now=tr[now_insert][tar];
        tr_ver[now].push_back(i);
        ver_tr[i]=now;
    }
    scanf("%d",&m);
    for(int i=1;i<=m;i++) {
        scanf("%d",&query[i].lo);cin>>query[i].add;query[i].mod_ed=insert_ac(query[i].add);query[i].id=i;
        so[query[i].lo].push_back(query[i]);
    }
    build_ac();
    dfs1(0);
    dfs2(0,0);
    for(int i=1;i<=m;i++)printf("%d\n",ans[i]);
}
```

另外，记录一下本题的小故事：

![IMG_20190828_071125](pic/CF1207G Indie Album-1.png)