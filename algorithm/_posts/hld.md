---
layout: page
title: "Heavy-Light Decomposition"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Tutorial

트리를 일자로 펴주는 알고리즘

트리에서의 query는 처리하기 까다롭지만, 일자로 펴진 수열에서는 쉬워진다. 따라서 hld로 트리를 일자로 펴고 [segment tree](/algorithm/segment_tree)를 결합해 푸는 문제가 대부분이다.

```cpp
vector<int> adj[mxN];
int t, sz[mxN], par[mxN], top[mxN], in[mxN];

void dfs(int u) {
    sz[u] = 1;
    for (int i = 0; i < adj[u].size(); ++i) {
        int& v = adj[u][i];
        if (v == par[u]) {
            swap(v, adj[u].back());
            adj[u].pop_back();
            --i;
            continue;
        }

        par[v] = u;
        dfs(v);
        sz[u] += sz[v];

        if (sz[v] > sz[adj[u][0]])
            swap(v, adj[u][0]);
    }
}
void hld(int u) {
    in[u] = t++;
    bool heavy = true;
    for (int& v : adj[u]) {
        top[v] = heavy ? top[u] : v;
        hld(v);
        heavy = false;
    }
}
int lca(int u, int v) {
    while (top[u] != top[v])
        sz[top[u]] < sz[top[v]] ? u = par[top[u]] : v = par[top[v]];
    return in[u] < in[v] ? u : v;
}
```