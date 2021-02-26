---
layout: page
title: "Segment Tree"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Introduction

point update와 range query를 $$O(\log n)$$에 해주는 자료구조.

Segment Tree를 만드는 방법은 다음과 같다.

먼저 전체 구간 $$[0, n)$$의 대푯값을 구해서 저장한다.

이 구간을 둘로 쪼개서 $$[0, m), [m, n)$$ 각각의 대푯값을 또 저장한다.

이것을 구간의 길이가 1이 될 때까지 반복한다.

구간 $$[l, r)$$을 $$[l, m), [m, r)$$로 쪼갤 때, 쪼개진 구간이 원래 구간의 child node가 되도록 Binary Tree를 만들어주면 Segment Tree가 된다.

즉, Segment Tree는 아래 그림처럼 만들어진다.

![](/assets/img/algorithm/segment_tree/0.png)

$$node_5$$는 구간 $$[4, 8)$$의 대푯값을, $$node_9$$는 구간 $$[2, 4)$$의 대푯값을 가진다.

여기서 **대푯값**은 처리해야 하는 query에 대한 답이다. 만약 우리가 처리해야 하는 query가 최솟값을 묻는 query면 최솟값을 저장하고, 구간합을 묻는 query면 구간합을 저장하면 된다.

- - -

이제 query를 처리해보자. 구간 $$[3, 15)$$의 대푯값은 아래처럼 다섯 개의 구간을 합쳐서 구할 수 있다. 어떤 range query가 주어지더라도, 이걸 $$O(\log n)$$개의 구간을 합쳐서 구할 수 있다.

![](/assets/img/algorithm/segment_tree/1.png)

이제 update를 처리해보자. index 11의 값이 바뀌면 아래처럼 다섯 개의 구간의 값만 수정하면 된다. 어떤 point update가 주어지더라도, $$O(\log n)$$개의 구간만 값을 바꿔주면 된다.

![](/assets/img/algorithm/segment_tree/2.png)

## 시간 복잡도

update의 시간 복잡도를 구해보자.

Segment Tree에서 어떤 node가 수정되면, 이 node의 parent node도 수정되어야 한다. 그럼 또 그 parent node의 parent node도 수정되어야 하고... 이걸 반복해서 root node까지 수정하면 된다.

즉, point update에서 수정되어야 하는 node의 개수는 구간의 길이가 1인 node(leaf node)부터 root node까지의 길이와 같다. 이는 Tree의 높이와 같고, Segment Tree를 Balanced Binary Tree로 만들어주면 높이는 $$\log n$$이 된다.

- - -

query의 시간 복잡도를 구해보자.

query에서 묻는 구간의 값을 몇몇 개의 node를 합쳐서 구한다고 했다. 이때, 선택되는 node는 Segment Tree에서 같은 level에 세 개 이상 있을 수 없다.

![](/assets/img/algorithm/segment_tree/3.png)

만약 그림처럼 한 level에서 연속된 세 개의 빨간 node를 합치면 query에서 묻는 구간이 된다고 하자. 그럼 그중 두 개를 합친 구간(파란색 node)가 반드시 위에 존재한다. 그럼 굳이 빨간 node 세 개를 합칠 필요가 없다. 빨간 node 하나랑 위쪽 level의 파란색 node를 합치면 된다.

즉, level 당 최대 두 개의 node만 합치면 된다. Segment Tree를 Balanced Binary Tree로 만들어주면 전체 level(높이)은 $$\log n$$이 된다.

- - -

이 증명은 bottom-up 구현에서는 바로 이해가 가지만 top-down 구현에서는 직관적이지 않다. 합칠 구간의 개수가 $$O(\log n)$$이고 각각 구간을 재귀 호출 할 때 깊이는 $$O(\log n)$$이니, 총 복잡도는 $$O(\log^2 n)$$이 아닌가?

top-down 방식도 $$O(\log n)$$임을 증명해보자.

어떤 node가 나타내는 구간을 XX...XX로 표현하자. X의 개수는 그 구간의 길이다. 그중 query에서 주어진 구간과 겹치는 부분을 O로 표시하자.

이때 구간의 모습은 세 종류 중 하나다.

![](/assets/img/algorithm/segment_tree/4.png)

1. no overlap: 전혀 겹치지 않는 경우
2. total overlap: 완전 겹치는 경우
3. partial overlap: 부분적으로 겹치는 경우

no overlap인 경우 그 node는 query와 연관이 없으므로 필요가 없다. total overlap인 경우 그 node의 값을 읽어가면 된다. 즉, 둘 다 $$O(1)$$에 처리 가능하다.

partial overlap인 경우 구간을 쪼개서 재귀적으로 처리해야 하므로 $$O(1)$$은 아니다.

그럼 partial overlap인 경우 어떻게 쪼개지는지 보자.

![](/assets/img/algorithm/segment_tree/5.png)

이 그림을 정리하면 다음과 같다.

* middle overlap의 경우 하나의 middle overlap을 재귀 호출하거나 최대 두 개의 single ended overlap을 재귀 호출한다.
* single ended overlap의 경우 최대 하나의 single ended overlap을 재귀 호출한다.
* 재귀 호출의 최대 깊이는 Tree의 높이와 같다.

즉, level 당 최대 두 개의 재귀 호출만 된다. 시간 복잡도 역시 $$O(\log n)$$이다.

## Complete Binary Tree

###### Tutorial

구간 값끼리의 연산만 하면 되는 경우.

range minimum, range sum 등이 해당한다.

1. 모든 $$i \ (1 \le i \le n)$$에 대해 $$\min (a_1,a_2,a_3,\cdots,a_n) = \min(\min(a_1,a_2,a_3\cdots,a_i), \min(a_{i+1},a_{i+2},a_{i+3}\cdots,a_n))$$이 성립한다.
2. 길이가 $$n$$인 모든 순열 $$p_1,p_2,\cdots,p_m$$에 대해 $$\min(a_{p_{11}},a_{p_{12}},\cdots,a_{p_{1n}})=\min(a_{p_{21}},a_{p_{22}},\cdots,a_{p_{2n}})=\cdots=\min(a_{p_{m1}},a_{p_{m2}},\cdots,a_{p_{mn}})$$이 성립한다.

즉, 두 구간을 합칠 때 순서가 어떻든 양 구간의 길이가 어떻든 상관없다. 이것은 minimum 외 sum, gcd 등에도 적용된다.

###### node 개수

구간 $$[l,m), \ [m,r)$$이 있으면 새로운 구간 $$[l,r)$$을 만들어 둘을 합칠 수 있다.

새로운 구간을 만들 때마다 구간 두 개가 하나로 합쳐져서 전체 구간의 개수는 1 감소한다.

leaf node의 개수를 $$N$$이라 하면, 이걸 하나의 구간으로 묶는데 $$N-1$$개의 추가 node가 필요하므로 총 필요한 node의 개수는 $$2N-1$$이다.

하지만 $$2N$$개의 node를 만들고 0번 node를 더미로 두는 게 구현이 깔끔하다.

###### Pseudo Code

```cpp
#define left (i << 1)
#define right (i << 1 | 1)

a[mxN]
segT[mxN << 1]

init() {
    move(a, a + N, segT + N)
    for (int i = N - 1; i; --i)
        segT[i] = merge(segT[left], segT[right])
}
query(l, r) {
    ret = INF
    for (l += N, r += N; l != r; l >>= 1, r >>= 1) {
        if (l & 1) ret = merge(ret, segT[l++])
        if (r & 1) ret = merge(ret, segT[--r])
    }
    return ret
}
update(i, val) {
    segT[i += N] = val
    while (i >>= 1)
        segT[i] = merge(segT[left], segT[right])
}
```

## Full Binary Tree

###### Tutorial

구간 값뿐만 아니라 좌우 구분, 구간의 길이도 필요한 경우.

두 구간을 합칠 때, 두 구간의 길이가 같아야 하거나 순서가 유지되어야 할 때 쓰인다.

Full Binary Tree 형태로 만들면 같은 depth를 가지는 node는 구간의 길이가 모두 같고, parent node는 구간이 2배가 되고, left child가 나타내는 구간은 항상 right child가 나타내는 구간보다 왼쪽에 있게 된다.

###### node 개수

Full Binary Tree를 만들기 위해 leaf node의 개수를 $$2^k$$꼴로 맞춰줘야 한다.

그때의 node 개수는 $$2^{k+1}-1$$이다. $$k$$의 최솟값은 $$\lceil log_2N \rceil$$이다.

하지만 $$2 \cdot 2^{\lceil log_2N \rceil}$$개의 node를 만들고 0번 node를 더미로 두는 게 구현이 깔끔하다.

###### Pseudo Code

$$N$$을 $$sgN$$으로 바꾼 것 외에는 차이가 거의 없다.

$$sgN$$을 구하는 두 가지 방법만 염두에 두면 된다.

대개는 $$N$$ 범위가 주어지므로, $$\lceil log_2N \rceil$$를 계산기를 두드려서 구해도 된다. 예) $$N = 10^5 \to sgN = 131072$$

```cpp
#define left (i << 1)
#define right (i << 1 | 1)

a[mxN]
segT[sgN << 1]

init() {
    // manner 1
    sgN = 1
    while (sgN < N) sgN <<= 1

    // manner 2
    sgN = 1 << (32 - __builtin_clz(N - 1))

    move(a, a + N, segT + N)
    fill(segT + N, segT + sgN, identity element)

    for (int i = sgN - 1; i; --i)
        segT[i] = merge(segT[left], segT[right])
}
query(l, r) {
    ret = INF
    for (l += sgN, r += sgN; l != r; l >>= 1, r >>= 1) {
        if (l & 1) ret = merge(ret, segT[l++])
        if (r & 1) ret = merge(ret, segT[--r])
    }
    return ret
}
update(i, val) {
    segT[i += sgN] = val
    while (i >>= 1)
        segT[i] = merge(segT[left], segT[right])
}
```

###### Useful Functions

```cpp
int len(int i) {
    return sgN >> 31 - __builtin_clz(i);
}
```

$$node_i$$가 나타내는 구간의 길이를 구해준다.

예) $$N = 1$$인 경우 $$sgN$$을 리턴하고 $$N \ge sgN$$인 경우 $$1$$을 리턴한다.

`(31 - __builtin_clz(N)) = floor(lgN)`는 정말 질리도록 사용된다. 꼭 알아두자.

```cpp
int fst(int i) {
    int d = 31 - __builtin_clz(i); // depth
    return (sgN >> d) * (i ^ 1 << d);
}
```

$$node_i$$가 나타내는 구간의 첫 번째 인덱스를 구해준다. 즉, $$node_i = interval \ [l, r)$$이라면 $$l$$을 리턴한다.

`(sgN >> d)`는 위의 `len`함수랑 같다.

`(i ^ 1 << d)`는 `i`에서 msb를 지운다. msb가 지워진 `i`는 Tree의 왼쪽에서 몇 칸이나 떨어져 있는지를 뜻한다.

## Merge Sort Tree

###### Tutorial

구간의 `node` 값을 전부 가지고 있어야 하는 경우.

node의 개수는 Complete Binary Tree냐 Full Binary Tree냐에 따라 선택하면 된다.

`update`가 매우 비효율적이므로, 문제에서 `update`를 요구하는 경우 다른 방법으로 접근하자.

###### Code

```cpp
int a[mxN];
vector<int> mrgT[mxN << 1];

void init() {
    for (int i = 0; i < N; ++i)
        mrgT[N + i] = {a[i]};
    for (int i = N - 1; i; --i) {
        mrgT[i].resize(mrgT[left].size() + mrgT[right].size());
        merge(mrgT[left].begin(), mrgT[left].end(), mrgT[right].begin(), mrgT[right].end(), mrgT[i].begin());
    }
}
```

## Challenge

[Segment Tree with Lazy Propagation](/algorithm/segment_tree_lazy)

Segment Tree Beats 포스팅 예정...

## Problems to try

###### k-th number

[\[백준 &nbsp;&nbsp;7469\] K번째 수](/baekjoon/7469)

고정된 배열의 어떤 구간 안에서 k번째 수를 찾는 쿼리를 해결하는 문제

###### 직사각형으로 덮힌 넓이

[\[백준 &nbsp;&nbsp;3392\] 화성 지도](/baekjoon/3392)

좌표 압축 x

[\[백준 &nbsp;&nbsp;7626\] 직사각형](/baekjoon/7626)

좌표 압축 o