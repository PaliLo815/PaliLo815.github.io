---
layout: article
title: Bipartite Matching
aside:
  toc: true
tags:
- ✅Tutorial (Basic)
- bipartite matching
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Introduction

매칭(matching)은 그래프에서 인접하지 않은 간선들의 집합을 말한다. 그래프에서 아무 정점이나 골랐을 때, 매칭에서 이 정점과 연결된 간선이 두 개 이상 있으면 안된다.

## Tutorial


## Code

```cpp
struct bipartite_matching {
    bipartite_matching(int _n, int _m) : n(_n), m(_m),
                                         adj(n), match(n, -1), rev(m, -1), visited(n) {}

    void add_edge(int u, int v) { adj[u].emplace_back(v); }
    /**
     * @note O(n^2 m)
     * @return the size of maximum matching
     */
    int maximum_matching() {
        for (bool update = true; update;) {
            fill(visited.begin(), visited.end(), false);
            update = false;

            for (int i = 0; i < n; ++i)
                if (match[i] == -1 && dfs(i))
                    update = true;
        }
        return n - count(match.begin(), match.end(), -1);
    }
    /**
     * @warning must be used after `maximum_matching`
     * @note O(nm)
     * @return the vertices of minimum vertex cover
     *         for x in returned vector:
     *             x >= 0 means vertex x in lhs
     *             x < 0  means vertex ~x in rhs
     */
    vector<int> minimum_vertex_cover() {
        vector<bool> check(m);
        auto bfs = [&](int src) {
            queue<int> q;
            q.emplace(src);
            visited[src] = true;

            while (!q.empty()) {
                const auto u = q.front();
                q.pop();

                for (const auto& v : adj[u])
                    if (~rev[v] && !visited[rev[v]] && match[u] != v) {
                        check[v] = true;
                        visited[rev[v]] = true;
                        q.emplace(rev[v]);
                    }
            }
        };

        fill(visited.begin(), visited.end(), false);
        for (int i = 0; i < n; ++i)
            if (match[i] == -1 && !visited[i])
                bfs(i);

        vector<int> ret;
        ret.reserve(n - count(match.begin(), match.end(), -1));

        for (int i = 0; i < n; ++i)
            if (!visited[i])
                ret.emplace_back(i);
        for (int i = 0; i < m; ++i)
            if (check[i])
                ret.emplace_back(~i);

        return ret;
    }
    
private:
    const int n, m;          // # of vertices in lhs/rhs
    vector<vector<int>> adj; // graph
    vector<int> match, rev;  // matched pairs. if match[u] = v, then rev[v] = u.
    vector<bool> visited;    // for internal dfs/bfs

    bool dfs(int u) {
        visited[u] = true;
        for (const auto& v : adj[u]) {
            if (rev[v] == -1 || (!visited[rev[v]] && dfs(rev[v]))) {
                match[u] = v, rev[v] = u;
                return true;
            }
        }
        return false;
    }
};
```
