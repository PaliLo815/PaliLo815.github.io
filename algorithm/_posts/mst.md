---
layout: page
title: "Minimum Spanning Tree"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Kruskal

```cpp
struct edge {
    int u, v, w;
    edge(int u = 0, int v = 0, int w = 0) : u(u), v(v), w(w) {}
    bool operator<(const edge& rhs) const {
        return w < rhs.w;
    }
};

int kruskal(vector<edge> e, int n) {
    disjoint_set dset(n);

    sort(e.begin(), e.end());

    int mst = 0;
    for (auto& [u, v, w] : e)
        if (dset._union(u, v))
            mst += w;
    return mst;
}
```

## Prim


