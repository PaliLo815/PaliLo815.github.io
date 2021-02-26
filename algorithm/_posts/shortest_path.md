---
layout: page
title: "Shortest Path"
description: >
  최단 경로를 구하는 여러가지 알고리즘 <br />
  Dijkstra, Bellman-Ford + SPFA, Floyd-Warshall
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Dijkstra

$$O(V + E \log E)$$의 최단 경로 알고리즘

그래프에 negative cycle이 있으면 무한 루프에 빠진다. negative cycle은 없지만 negative weight edge가 있으면 복잡도가 지수적으로 증가할 수 있다.

```cpp
vector<pair<int, int>> adj[mxV];
int dist[mxV], par[mxV];

void dijkstra(int V, int src) {
    struct elem {
        int d, u;
        elem(int d, int u) : d(d), u(u) {}
        bool operator<(const elem& rhs) const {
            return d > rhs.d;
        }
    };

    priority_queue<elem> pq;

    fill(dist, dist + V, INF); // O(V)
    pq.emplace(dist[src] = 0, src);

    par[src] = -1;

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        if (dist[u] != d) continue;

        for (auto& [w, v] : adj[u]) // O(E)
            if (dist[v] > d + w) {
                pq.emplace(dist[v] = d + w, v); // O(logE)
                par[v] = u;
            }
    }
}
vector<int> reconstruct(int dst) {
    vector<int> path;
    for (int u = dst; ~u; u = par[u])
        path.emplace_back(u);
    reverse(path.begin(), path.end());
    return path;
}
```

## Bellman-Ford

$$O(VE)$$의 최단 경로 알고리즘

그래프에 `negative cycle`이 있는지 알아낼 수 있다.

```cpp
struct edge {
    int u, v, w;
    edge(int u, int v, int w) : u(u), v(v), w(w) {}
};

vector<edge> e; // edges
int dist[mxV], par[v];

// return the graph has negative cycle or not
// if return false, dist[i] is shortest path from src to i
// if return true, it is not guaranteed.
bool bellman_ford(int V, int src) {
    fill(dist, dist + V, INF);
    dist[src] = 0;
    par[src] = -1;

    bool update;
    for (int cnt = V; cnt--;) {
        update = false;
        for (auto& [u, v, w] : e)
            if (dist[u] != INF && dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                par[v] = u;
                update = true;
            }
        if (!update) break;
    }
    return update;
}
vector<int> reconstruct(int dst) {
    vector<int> path;
    for (int u = dst; ~u; u = par[u])
        path.emplace_back(u);
    reverse(path.begin(), path.end());
    return path;
}
```

어떤 도착지로의 최단 경로를 구할 때, negative cycle의 유무만으로 최단 경로가 존재하는지 아닌지를 구분할 수 없다. 그 negative cycle에서 도착지로의 경로가 존재하지 않다면, 영향을 끼치지 않는다.

이를 확인하는 방법으로 2가지가 있다.

(1) reverse edge를 구하기 편한 경우

도착지에서 거꾸로 bfs나 dfs를 수행. 도착지로 갈 수 있는 `vertex`만 고려한다.

즉, 어떤 vertex에서 도착지로 가는 경로가 없다면 그 vertex와 이어진 edge는 전부 지워버린다.

```cpp
vector<bool> visited(V);

bfs(dst);

// now, visited[i] is true if the path from i to destination exists

e.erase(remove_if(e.begin(), e.end(), [&](edge& elem) {
            return !visited[elem.u] || !visited[elem.v];
        }), e.end());
```

이렇게 전부 지우고 `bellman_ford`를 실행해도 negative cycle이 있으면 최단 경로는 정의되지 않는다.

(2) reverse edge를 구하기 어려운 경우

negative cycle을 만드는 vertex를 따로 모아놨다가 bfs를 수행해서 도착지로의 경로가 있는지 확인한다.

loop 한 번을 따로 돎을 유의.

```cpp
// if return false, dist[i] is shortest path from src to i
// if return true, it is not guaranteed.
bool bellman() {
    fill(dist, dist + V, INF);
    dist[s] = 0;

    bool update;
    // **WARNING** cnt = V - 1
    for (int cnt = V - 1; cnt--;) {
        update = false;
        for (auto& [u, v, w] : e)
            if (dist[u] != INF && dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                update = true;
            }
        if (!update) return dist[dst];
    }

    vector<int> vt;
    for (auto& [u, v, w] : e)
        if (dist[u] != INF && dist[v] > dist[u] + w)
            vt.emplace_back(v);

    // bfs(vt) return true if any vertex in vector can visit destination
    return bfs(vt);
}
```

## SPFA

추가 예정...

## Floyd-Warshall

$$O(V^3)$$의 최단 경로 알고리즘

모든 vertex 쌍에 대한 최단 경로를 구해준다.

```cpp
#define loop(i, n) for (int i = 0; i < n; ++i)

int adj[mxV][mxV], via[mxV][mxV];
vector<int> path;

void floyd(int V) {
    memset(via, -1, sizeof(via));
    loop(k, V) loop(i, V) if (adj[i][k] != INF)
        loop(j, V) if (adj[k][j] != INF && adj[i][k] + adj[k][j] < adj[i][j]) {
            adj[i][j] = adj[i][k] + adj[k][j];
            via[i][j] = k;
    }
}
// path from u to v
void reconstruct(int u, int v) {
    if (via[u][v] == -1) {
        path.emplace_back(u);
        if (u != v) path.emplace_back(v);
        return;
    }
    reconstruct(u, via[u][v]);  // path from u to via point
    path.pop_back();            // delete via point, because it pushed twice
    reconstruct(via[u][v], v);  // path from via point to v
}
```