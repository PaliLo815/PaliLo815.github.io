---
layout: page
title: "Bellman-Ford"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Tutorial

$$O(VE)$$의 최단 경로 알고리즘

그래프에 `negative cycle`이 있는지도 알아낼 수 있다.

```cpp
struct edge {
    int u, v, w;
    edge(int u, int v, int w) : u(u), v(v), w(w) {}
};

int V;          // # of vertices
vector<edge> e; // edges
int src;        // source
int dst;        // destination

// return the graph has negative cycle or not
bool bellman_ford() {
    fill(dist, dist + V, INF);
    dist[src] = 0;

    bool update;
    for (int cnt = V; cnt--;) {
        update = false;
        for (auto& [u, v, w] : e)
            if (dist[u] != INF && dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                update = true;
            }
        if (!update) break;
    }
    return update;
}
```

`single-destination shortest path`를 구할 땐 `negative cycle`의 유무만으로 최단 경로가 존재하는지 아닌지를 구분할 수 없다. 그 `negative cycle`에서 도착지로의 경로가 존재하지 않다면, 영향을 끼치지 않는다.

이를 확인하는 방법으로 2가지가 있다.

_ _ _

(1) reverse edge를 구하기 편한 경우

도착지에서 거꾸로 `bfs`를 수행. 도착지로 갈 수 있는 `vertex`만 고려한다.

즉, 도착지와 다른 `component`에 속한 `vertex`를 잇는 `edge`는 지워버린다.

```cpp
vector<bool> visited(V);
bfs(dst);
// now, visited[i] is true if the path from i to destination exists

e.erase(remove_if(e.begin(), e.end(), [&](edge& elem) {
            return !visited[elem.u] || !visited[elem.v];
        }), e.end());
```

(2) reverse edge를 구하기 어려운 경우

`negative cycle`을 만드는 `vertex`를 따로 모아놨다가 `bfs`를 수행해서 도착지로의 경로가 있는지 확인한다.

`loop` 한 번을 따로 돎을 유의.

```cpp
// return the shortest path from source to destination
int bellman_ford() {
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

    queue<int> q;
    for (auto& [u, v, w] : e)
        if (dist[u] != INF && dist[v] > dist[u] + w)
            q.emplace(v);

    // bfs(q) return true if can visit destination
    if (bfs(q)) return neg_cycle;
    return dist[dst];
}
```

- - -

## Problems to try

[\[백준 7040\] 밥 먹기](/baekjoon/7040)