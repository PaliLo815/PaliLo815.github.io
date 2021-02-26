---
layout: article
title: Binary Search
aside:
  toc: true
tags:
- ✅Tutorial (Basic)
- binary_search
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Tutorial

배열 $a$가 어떤 함수 `bool f(x)`에 의해 두 개의 그룹으로 나뉘어지는 partitioned array일 경우 사용 가능한 알고리즘이다.

즉, `f`에 $a$의 각 원소를 넣고 실행한 결과 $f(a_0), f(a_1), \cdots, f(a_{n-1})$가

$$
\begin{align}
\text{False}, \cdots, \text{False}, \text{True}, \cdots, \text{True} \\
\text{True}, \cdots, \text{True}, \text{False}, \cdots, \text{False}
\end{align}
$$

반드시 둘 중 하나의 꼴이어야만 한다.

주로 `bool f(x)`는 문제의 조건으로 주어지고, 저 조건을 만족하는($f(a_i) = \text{True}$) $i$의 최솟값 또는 최댓값을 찾아야 한다. 최솟값을 찾는 경우엔 1번 꼴일 것이며 최댓값일 경우엔 2번 꼴일 것이다.

배열 $a$를 정수나 실수 집합 전체로 확장시키는 경우, 매개 변수 탐색(parametric search)라고 한다. PS 영역에서 둘을 딱히 구별지을 필요는 없다.

- - -

이제 우리는 탐색 범위 $[mn, mx]$와 `bool f(x)`를 설정하기만 하면 된다.

문제에서 탐색할 배열을 직접 주거나 범위의 최솟값과 최댓값이 명시되어 있을 경우 그대로 사용하면 되지만, 그렇지 않을 경우 직접 가능한 해의 범위를 계산해야 한다.

핵심은 `bool f(x)`를 만드는 것이다. 범위 내의 수를 $\text{False}, \cdots, \text{False}, \text{True}, \cdots, \text{True}$ 또는 $\text{True}, \cdots, \text{True}, \text{False}, \cdots, \text{False}$ 꼴로 분리시키는 함수라고 생각하면 쉽다.

```cpp
const int mn = 0, mx = 1e9;
auto f = [&](int x) -> bool {
    if (...) return true;
    else return false;
};
```

$[mn, mx]$와 `bool f(x)`를 위에처럼 적당히 만들었다고 가정하고, 탐색을 시작하자.

- 최솟값인 경우

```cpp
auto lo = mn, hi = mx + 1;
while (lo != hi) {
    auto mid = lo + hi >> 1;
    f(mid) ? hi = mid : lo = mid + 1;
}
if (lo == mx + 1)
    impossible;
else
    answer = lo; // or answer = hi
```

- 최댓값인 경우

```cpp
auto lo = mn - 1, hi = mx;
while (lo != hi) {
    auto mid = lo + hi + 1 >> 1;
    f(mid) ? lo = mid : hi = mid - 1;
}
if (lo == mn - 1)
    impossible;
else
    answer = lo; // or answer = hi
```

$$[mn, mx]$$구간에서 가능한 해가 없는 경우에 **impossible** 블록이 실행된다.

최소 하나의 해가 보장될 경우, `lo = mn, hi = mx`로 통일을 해도 무방하다.

## STL functions

### lower_bound & upper_bound

이 함수의 구현은 다음과 같다.

```cpp
lower_bound(lo, hi, key) {
    m = (lo + hi) >> 1;
    cmp(*m, key) ? (lo = m + 1) : (hi = m);
    return lo;
}
upper_bound(lo, hi, key) {
    m = (lo + hi) >> 1;
    !cmp(key, *m) ? (lo = m + 1) : (hi = m);
    return lo;
}
```

`cmp`함수의 인자를 보자. `lower_bound`는 `key`가 항상 오른쪽이고 `upper_bound`는 `key`가 항상 왼쪽이다.

`*lower_bound <= key < *upper_bound`임을 생각해보면 직관적이다.

이를 이용해서 `[key - l_val, key + r_val)`구간을 아래처럼 구할 수 있다.

```cpp
const vector<int> vt = {1, 1, 2, 2, 3, 3, 4, 4, 5, 5, 6, 6, 7, 7};
const int key = 3, lval = 1, rval = 2;

auto lo = lower_bound(vt.begin(), vt.end(), key, [&](const auto& value, const auto& key) {
    return value < key - lval;
});
auto hi = upper_bound(vt.begin(), vt.end(), key, [&](const auto& key, const auto& value) {
    return key + rval < value;
});

// *[lo, hi) = {2, 2, 3, 3, 4, 4, 5, 5}
```

### partition_point

partitioned array에서 두 번째 그룹의 시작점을 반환한다. parametric search가 아니라 주어진 배열 안에서의 binary search인 경우 사용하면 된다.

**주의)** 여기서 말하는 partitioned array는 $\text{True}, \cdots, \text{True}, \text{False}, \cdots, \text{False}$ 꼴이다. 두 번째 그룹이 $\text{False}$가 되도록 함수를 만들어서 넘겨줘야 한다.
{:.error}

```cpp
vector<int> vt = {1, 3, 6, 10, 15, 100, 9999};
auto mid = partition_point(vt.begin(), vt.end(), [&](auto& x) {
    return x < 10;
});

// *[begin, mid) = {1, 3, 6}
// *[mid,   end) = {10, 15, 100, 9999}
```

## Parallel Binary Search

여러 개의 query에 대해 동시에 binary search를 한다.

각가의 query를 binary search로 해결 가능한 경우 그리고 offline query일 경우에 사용할 수 있다.

아래는 $m$개의 query를 구간 $$[0, n)$$에서 동시에 탐색하는 code다. 이 틀을 적절히 변형해서 사용하면 된다.

```cpp
vector<int> lo(m), hi(m, n);
for (;;) {
    vector<vector<int>> check(n);
    bool done = true;
    for (int i = 0; i < m; ++i)
        if (lo[i] != hi[i]) {
            done = false;
            check[lo[i] + hi[i] >> 1].emplace_back(i);
        }
    if (done) break;

    for (int mid = 0; mid < n; ++mid)
        for (const auto& i : check[mid])
            f(i) ? hi[i] = mid : lo[i] = mid + 1;
}
```

## Problems to Try

#### lis

[\[백준 12738\] 가장 긴 증가하는 부분 수열 3](/baekjoon/12738)
--- 길이 구하기.

[\[백준 14003\] 가장 긴 증가하는 부분 수열 5](/baekjoon/14003)
--- 길이 + 수열 구하기.

[\[백준 17411\] 가장 긴 증가하는 부분 수열 6](/baekjoon/17411)
--- 길이 + 개수 구하기.