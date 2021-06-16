---
title: Disjoint Set Union
tags:
- library
- data structure
---

# Disjoint Set Union (simple)

여러 집합을 관리하는 자료구조. 두 집합을 합치는 연산(union), 어떤 원소가 어느 집합에 들어있는지 알려주는 연산(find)을 해주기 때문에 Union-Find라고도 불린다.

**Union by Rank**와 **Path Compression** 최적화를 둘 다 적용했기 때문에 위의 두 연산을 $O(1)$에 해준다.

엄밀히 말하면 $O(1)$이 아니라 $O(\alpha (n))$이다. 여기서 $\alpha (n)$은 아커만 함수의 역함수로, 매우 느리게 증가하는 함수다. $n \lt 10^{600}$의 경우 4를 넘지 않으므로, PS에서는 그냥 $O(1)$이라고 생각해도 된다.
{:.warning}

offline-dynamic-connectivity, 트리의 경로상에서 최소/최대값을 구하는 쿼리, union으로 두 집합을 합치는 것 뿐만 아니라 split으로 두 집합을 나누기 등, 여러가지 응용 버전이 있다.

그런 응용 버전은 **(advance)**로 따로 나누었다.

## Constructor

```cpp
disjoint_set(int n)
```

*Complexity*

- $O(n)$

## find

```cpp
int find(int u)
```

`u`가 속한 집합을 리턴한다.

*Constraints*

- $0 \leq u < n$

*Complexity*

- $O(\alpha (n))$

## unite

```cpp
bool unite(int u, int v)
```

`u`가 속한 집합과 `v`가 속한 집합을 서로 합친다.

합쳐진 경우 `true`를, 이미 합쳐진 상태였다면 `false`를 리턴한다.

*Constraints*

- $0 \leq u, v < n$

*Complexity*

- $O(\alpha (n))$

## size_of

```cpp
int size_of(int u)
```

`u`가 속한 집합의 크기를 리턴한다.

*Constraints*

- $0 \leq u < n$

*Complexity*

- $O(\alpha (n))$

## is_same

```cpp
bool is_same(int u, int v)
```

`u`와 `v`가 같은 집합에 속하면 `true`를, 아니라면 `false`를 리턴한다.

*Constraints*

- $0 \leq u, v < n$

*Complexity*

- $O(\alpha (n))$

# Code

[github link (CLICK)](https://github.com/PaliLo815/Team-Library/blob/f22c5f557775e116601061ed192ec6da4dc70890/data/disjoint_set.cpp)

```cpp
/**
 * @brief 
 *      Disjoint Set Union (a.k.a Union-Find)
 */
class disjoint_set {
public:
    disjoint_set(int _n) : n(_n), par(n, -1) {}

    int find(int u) {
        assert(0 <= u and u < n);
        return par[u] < 0 ? u : par[u] = find(par[u]);
    }
    bool unite(int u, int v) {
        assert(0 <= u and u < n and 0 <= v and v < n);
        u = find(u), v = find(v);
        if (u == v) return false;

        if (par[u] > par[v]) swap(u, v);
        par[u] += par[v];
        par[v] = u;
        return true;
    }
    int size_of(int u) {
        assert(0 <= u and u < n);
        return -par[find(u)];
    }
    bool is_same(int u, int v) {
        assert(0 <= u and u < n and 0 <= v and v < n);
        return find(u) == find(v);
    }

private:
    const int n;
    vector<int> par;
};
```
