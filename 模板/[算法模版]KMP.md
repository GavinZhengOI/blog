# [算法模版]KMP

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