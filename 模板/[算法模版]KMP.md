# [算法模版]KMP

$next$的意义是当前串的最长公共前后缀长度。对于一个公共前后缀，一直跳$next$可以遍历到所以长度比他小的公共前后缀。

另外指针的位置是在当前字串下最长的公共前后缀，跳一次next之后就会变成次长的公共前后缀。

主要是我原来的板子太屑了，就换个板子。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxn = 1e6 + 10;
int n, m, nxt[maxn];//next为闭区间next[i]=j代表[1,j]和i的[i-j+1,i]相等
char str1[maxn], str2[maxn];

int main() {
    scanf("%s %s", str1 + 1, str2 + 1);
    n = strlen(str1 + 1);
    m = strlen(str2 + 1);
    for (int i = 2, j = 0; i <= m; i++) {//j指针代表现在模式串匹配到哪里了（初始为0，指已匹配到的模式串为空）
        for (; j && str2[i] != str2[j + 1]; j = nxt[j]);
        if (str2[i] == str2[j + 1]) {
            j++;
        }
        nxt[i] = j;
    }
    for (int i = 1, j = 0; i <= n; i++) {
        for (; j && str1[i] != str2[j + 1]; j = nxt[j]);
        if (str1[i] == str2[j + 1]) {
            j++;
        }
        if (j == m) {
            j = nxt[j];
            printf("%d\n", i - m + 1);
        }
    }
    for (int i = 1; i <= m; i++) {
        printf("%d ", nxt[i]);
    }
    return 0;
}
```

## 循环节

对于一个长度为$n$的，由一个字符串循环若干次得到的新字符串，其最小循环节长度是$n-next[n]$。

证明的话自己画一下图就好了。倒推一下即可。

## **next树上节点`x`子树的意义**

- 比`x`长，且公共前后缀长度大于等于`x`的所有前缀
- `x`的子树大小即前缀`x`在原串中的出现次数。（因为我们发现`x`在原串中重复出现的位置都一一对应了一个上一条所说的点）

