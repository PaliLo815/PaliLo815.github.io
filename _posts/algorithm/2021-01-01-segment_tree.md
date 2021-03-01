---
layout: article
title: Segment tree
aside:
  toc: true
tags:
- ✅Tutorial (Basic)
- segment tree
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Introduction

point update와 range query를 $O(\log n)$에 처리하는 자료구조. $O(n)$의 메모리와 $O(n)$의 전처리를 필요로 한다.

update가 없을 경우 query를 $O(1)$에 처리해주는 [sparse table](/2021/01/01/sparse_table)이 존재한다. 그러나 segment tree쪽이 전처리가 더 빠르고 query가 $\log n$이지만 상수가 작기 때문에, 실행해보면 segment tree쪽이 더 빠른 경우가 허다하다.
{:.info}

## Tutorial

segment tree를 만드는 방법은 다음과 같다.

먼저 전체 구간 $[0, n)$의 대푯값을 구해서 저장한다.

이 구간을 둘로 쪼개서 $[0, m), [m, n)$ 각각의 대푯값을 또 저장한다.

이것을 구간의 길이가 1이 될 때까지 반복한다.

구간 $[l, r)$을 $[l, m), [m, r)$로 쪼갤 때, 쪼개진 구간이 원래 구간의 child node가 되도록 binary tree를 만들어주면 segment tree가 된다.

즉, segment tree는 아래 그림처럼 만들어진다.

![](/assets/images/algorithm/segment_tree/0.png)

$node_5$는 구간 $[4, 8)$의 대푯값을, $node_9$는 구간 $[2, 4)$의 대푯값을 가진다.

여기서 **대푯값**은 처리해야 하는 query에 대한 답이다. 만약 우리가 처리해야 하는 query가 최솟값을 묻는 query면 최솟값을 저장하고, 구간합을 묻는 query면 구간합을 저장하면 된다.

- - -

query를 처리해보자. 구간 $[3, 15)$의 대푯값은 아래처럼 다섯 개의 구간을 합쳐서 구할 수 있다. 어떤 range query가 주어지더라도, 이걸 $O(\log n)$개의 구간을 합쳐서 구할 수 있다.

![](/assets/images/algorithm/segment_tree/1.png)

update를 처리해보자. index 11의 값이 바뀌면 아래처럼 다섯 개의 구간의 값만 수정하면 된다. 어떤 point update가 주어지더라도, $O(\log n)$개의 구간만 값을 바꿔주면 된다.

![](/assets/images/algorithm/segment_tree/2.png)

## Time Complexity

update의 시간 복잡도를 구해보자.

어떤 node가 포함하는 구간은 그 parent node도 포함하고 있다.

따라서 어떤 node가 수정되면 그 parent node도 수정되어야 한다. 그럼 또 그 parent node의 parent node도 수정되어야 하고... 이걸 반복해서 root node까지 수정해야 한다.

즉, point update에서 수정되어야 하는 node의 개수는 구간의 길이가 1인 node(leaf node)부터 root node까지의 길이와 같다. 이는 트리의 높이와 같고, segment tree는 balanced binary tree이므로 $\log n$이다.

- - -

query의 시간 복잡도를 구해보자.

query에서 묻는 구간의 값을 몇몇 개의 node를 합쳐서 구한다고 했다. 이때 선택되는 node는 segment tree에서 같은 높이에 세 개 이상 있을 수 없다.

![](/assets/images/algorithm/segment_tree/3.png)

만약 그림처럼 한 높이에서 연속된 세 개의 빨간 node를 합치면 query에서 묻는 구간이 된다고 하자. 그럼 그중 두 개를 합친 구간(파란색 node)가 반드시 위에 존재한다. 그럼 굳이 빨간 node 세 개를 합칠 필요가 없다. 빨간 node 하나랑 위의 파란색 node를 합치면 된다.

즉, 높이 당 최대 두 개의 node만 합치면 된다. segment tree는 balanced binary tree이므로 전체 높이 $\log n$이 된다.

## Implementation

### Complete Binary Tree

두 구간을 합칠 때 순서와 길이가 상관 없는 경우.

range minimum, range sum 등이 해당한다.

###### # of nodes

구간 $[l, m), \ [m, r)$이 있으면 새로운 구간 $[l, r)$을 만들어 둘을 합칠 수 있다.

새로운 구간을 만들 때마다 구간 두 개가 하나로 합쳐져서 전체 구간의 개수는 1 감소한다.

leaf node의 개수를 $n$이라 하면, 이걸 하나의 구간으로 묶는데 $n - 1$개의 추가 node가 필요하므로 총 필요한 node의 개수는 $2n - 1$이다.

하지만 $2n$개의 node를 만들고 0번 node를 더미로 두는 게 구현이 깔끔하다.

###### Code

```cpp
template <typename node_t>
struct complete_seg {
    complete_seg(int _n) : n(_n), tree(_n << 1, e) {}

#define lson (i << 1)
#define rson (i << 1 | 1)
    node_t& operator[](int i) { return tree[n + i]; }
    void build() {
        for (int i = n - 1; i; --i)
            pull(i);
    }
    void update(int idx, node_t val) {
        assert(0 <= idx and idx < n);

        op(tree[idx += n], val);
        while (idx >>= 1) pull(idx);
    }
    node_t query(int l, int r) {
        assert(0 <= l and l <= r and r <= n);

        node_t ret = e;
        for (l += n, r += n; l != r; l >>= 1, r >>= 1) {
            if (l & 1) ret = op(ret, tree[l++]);
            if (r & 1) ret = op(tree[--r], ret);
        }
        return ret;
    }

private:
    const node_t e = node_t {}; // -> identity element
    const int n;
    vector<node_t> tree;

    void pull(int i) {
        tree[i] = op(tree[lson], tree[rson]);
    }
    node_t op(node_t lhs, node_t rhs) {
        return node_t {};
    }
#undef lson
#undef rson
};
```

### Full Binary Tree

구간 값뿐만 아니라 좌우 구분, 구간의 길이도 필요한 경우.

두 구간을 합칠 때, 두 구간의 길이가 같아야 하거나 순서가 유지되어야 할 때 쓰인다.

full binary tree 형태로 만들면 같은 높이에 있는 node끼리 구간의 길이가 같고, parent node는 구간의 길이가 2배가 되고, left child가 나타내는 구간은 항상 right child가 나타내는 구간의 바로 왼쪽에 있다.

###### # of nodes

full binary tree를 만들기 위해 leaf node의 개수를 $2^k$꼴로 맞춰줘야 한다.

그때의 node 개수는 $2^{k + 1}-1$이다. $k$의 최솟값은 $\lceil \log_2 n \rceil$이다.

하지만 $2 \cdot 2^{\lceil log_2N \rceil}$개의 node를 만들고 0번 node를 더미로 두는 게 구현이 깔끔하다.

###### Code

```cpp

```

### Merge Sort Tree

구간의 `node` 값을 전부 가지고 있어야 하는 경우.

node의 개수는 Complete Binary Tree냐 Full Binary Tree냐에 따라 선택하면 된다.

`update`가 매우 비효율적이므로, 문제에서 `update`를 요구하는 경우 다른 방법으로 접근하자.

###### Code

```cpp

```

## Challenge

[segment tree with Lazy Propagation](/algorithm/segment_tree_lazy)

segment tree Beats 포스팅 예정...

## Problems to try

###### k-th number

[\[백준 &nbsp;&nbsp;7469\] K번째 수](/baekjoon/7469)

고정된 배열의 어떤 구간 안에서 k번째 수를 찾는 쿼리를 해결하는 문제

###### 직사각형으로 덮힌 넓이

[\[백준 &nbsp;&nbsp;3392\] 화성 지도](/baekjoon/3392)

좌표 압축 x

[\[백준 &nbsp;&nbsp;7626\] 직사각형](/baekjoon/7626)

좌표 압축 o