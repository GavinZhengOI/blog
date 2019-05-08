# 倍增LCA

$fa[a][i]$代表a的第$2^{i}$个祖先。

主体思路是枚举二进制位，让两个查询节点跳到同一高度然后再向上跳相同高度找LCA。

```cpp
int fa[N][21], dep[N]; 
void dfs(int u, int f) {
    dep[u] = dep[f] + 1, fa[u][0] = f, tag1[u] = tag2[u] = Data(1);
    for (int i = 1;i <= 20;i++) fa[u][i] = fa[fa[u][i - 1]][i - 1];
    for (int i = head[u];i;i = e[i].next)
        if (e[i].to != f) dfs(e[i].to, u);
}
inline int LCA(int a, int b) {
    if (dep[a] < dep[b]) swap(a, b);
    for (int i = 20;i >= 0;i--) if (dep[fa[a][i]] >= dep[b]) a = fa[a][i];
    if (a == b) return a;
    for (int i = 20;i >= 0;i--) if (fa[a][i] != fa[b][i]) a = fa[a][i], b = fa[b][i];
    return fa[a][0];
}

```

