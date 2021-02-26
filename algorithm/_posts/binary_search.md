---
layout: page
title: "Binary Search"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

# Tutorial

어떤 배열 $a_0, a_1, \cdots , a_{n-1}$과 함수 `bool f(x)`가 있을 때,

`solve` 함수를 아래와 같이 정의할 때,
```cpp
bool solve(val) :
    // val이 정답 후보에 들어가는가?
    if possible
        return true
    else return false
```
문제에 따라 `solve(val)`를 만족하는 `val` 중 최솟값 또는 최댓값을 구해야 한다.

탐색 범위:  $$[mn, mx]$$

- 최솟값인 경우

```cpp
auto lo = mn, hi = mx + 1;
while (lo ^ hi) {
    auto m = (lo + hi) >> 1;
    solve(m) ? (hi = m) : (lo = m + 1);
}
if (lo == mx + 1)
    impossible;
else ans = lo; // or ans = hi;
```

- 최댓값인 경우

```cpp
auto lo = mn - 1, hi = mx;
while (lo ^ hi) {
    auto m = (lo + hi + 1) >> 1;
    solve(m) ? (lo = m) : (hi = m - 1);
}
if (lo == mn - 1)
    impossible;
else ans = lo; // or ans = hi;
```

$$[mn, mx]$$구간에서 가능한 해가 없는 경우에 **impossible** 블록이 실행된다.

최소 하나의 해가 보장될 경우, `lo = mn, hi = mx`로 통일을 해도 무방하다.

## std::lower_bound & std::upper_bound

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
auto lo = lower_bound(vt.begin(), vt.end(), key, [&](auto& a, auto& b) {
    return a + l_val < b;
});
auto hi = upper_bound(vt.begin(), vt.end(), key, [&](auto& a, auto& b) {
    return a < b - r_val;
});
// *lo <= key - l_val && key + r_val < *hi
// *lo + l_val <= key < *hi - r_val
```

## std::partition_point

범위 내의 원소가 어떤 함수 $$f$$에 대해 `true`인 그룹과 `false`인 그룹으로 나뉘여 있을 때, 두 번째 그룹의 시작점을 반환한다.

```cpp
vector<int> vt = {1, 3, 6, 10, 15, 100, 9999};
auto mid = partition_point(vt.begin(), vt.end(), [&](auto& x) {
    return x < 10;
});

// [begin, mid) : {1, 3, 6}
// [mid ,  end) : {10, 15, 100, 9999}
```

## Parallel Binary Search

여러 개의 query에 대해 동시에 binary search를 한다.

각가의 query를 binary search로 해결 가능한 경우 그리고 offline query일 경우에 사용할 수 있다.

대략적인 pseudo code는 아래와 같다. 여러 개의 query를 구간 $$[0, n)$$에서 동시에 탐색한다.

```python
fill lo[] with 0
fill hi[] with n

log(n) steps:
    clear check_list

    for q in query
        mid = (lo[q] + hi[q]) / 2
        insert q in check[mid]

    for i in range(n)
        # do something...
        for q in check[i]
            satisfied ? hi[q] = i : lo[q] = i + 1

```

## Problems to Try

###### lis

[\[백준 12738\] 가장 긴 증가하는 부분 수열 3](/baekjoon/12738)
--- 길이 구하기.

[\[백준 14003\] 가장 긴 증가하는 부분 수열 5](/baekjoon/14003)
--- 길이 + 수열 구하기.

[\[백준 17411\] 가장 긴 증가하는 부분 수열 6](/baekjoon/17411)
--- 길이 + 개수 구하기.