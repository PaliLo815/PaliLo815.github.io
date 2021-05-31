---
title: Binary Indexed Tree
tags:
- library
- data structure
---

# Binary Indexed Tree (a.k.a. Fenwick Tree)

`segment tree`에서 불필요한 node를 없애서 메모리 사용을 줄인 자료구조다. 메모리가 줄었으므로 캐시 효율도 좋아져서 속도도 빨라진다.
<center><img src="/assets/images/library/data/binary_indexed_tree/0.png"></center>
<center><i>$n = 8$일 때의 range sum segment tree</i></center>

위의 segment tree를 보자. 굳이 $$2n - 1$$개의 node마다 range sum을 일일이 저장하고 있어야 할까?

이를테면 $$\text{sum}[4,8) = 7$$은 굳이 필요 없다. $$\text{sum}[0,8) - \text{sum}[0,4) = 7$$와 같은 방식으로, 다른 값을 이용해 계산할 수 있기 때문이다.

이를 일반화해보자. 어떤 $$node_i$$의 $$child_{left}$$와 $$child_{right}$$가 있을 때, 둘 중 하나는 필요 없다.

segment tree의 $$2n - 1$$개 node 중 leaf node가 $$n$$개, non leaf node가 $$n - 1$$개다. 모든 non leaf node는 각자의 두 chlid node 중 하나를 버려도 되니 총 node 수를 $$(2n - 1) - (n - 1) = n$$개로 줄일 수 있다.

<center><img src="/assets/images/library/data/binary_indexed_tree/1.png"></center>
<center><i>필요없는 node가 지워진 segment tree <br> 이것이 binary indexed tree다.</i></center>

###### properties

모든 non leaf node의 right child를 없애고, 남은 node를 index에 맞춰서 오른쪽으로 밀어보자.

![](/assets/img/algorithm/fenwick_tree/2.jpg)

right child가 없어진 자리에 그 parent node를 겹쳐서 놓는다. X표 node는 없어진 right child node다. 

없앤 node에 update를 해야 할 경우 그냥 건너뛴다.

예) update 3

| Segment Tree | Fenwick Tree |
|:------------:|:------------:|
|![](/assets/img/algorithm/fenwick_tree/3.jpg)|![](/assets/img/algorithm/fenwick_tree/4.jpg)|

예) update 7

| Segment Tree | Fenwick Tree |
|:------------:|:------------:|
|![](/assets/img/algorithm/fenwick_tree/5.jpg)|![](/assets/img/algorithm/fenwick_tree/6.jpg)|

- - -

node 번호의 이진수 표현과 나타내는 구간 사이에서 연관성을 찾을 수 있다.

lsb(최하위 비트)는 구간의 길이를, lsb를 제외한 나머지는 구간의 시작점을 나타낸다.

예를 들어 6(0110)의 경우 lsb 0010과 lsb를 제외한 나머지(0110 - 0010 = 0100)로 나뉜다. 이는 구간의 시작점이 4(0100)이고, 길이가 2(0010)이란 뜻이다. 즉, $$tree_6 = a_4 + a_5$$를 나타낸다. 

몇 가지 예시를 더 보자.

| Decimal | Binary | lsb | 나머지 | 구간 |
|:-------:|:------:|:---:|:-----:|:---:|
| 3 | 0011 | 0001 (1) | 0010 (2) | $$[2, 3)$$ |
| 4 | 0100 | 0100 (4) | 0000 (0) | $$[0, 4)$$ |
| 7 | 0111 | 0001 (1) | 0110 (6) | $$[6, 7)$$ |

겹쳐진 node의 공통점도 찾을 수 있다.

1. $$[3, 4)$$: 3에서 시작하는 길이 1 구간 $$= 0001 + 0011 = 0100$$
2. $$[2, 4)$$: 2에서 시작하는 길이 2 구간 $$= 0010 + 0010 = 0100$$
3. $$[0, 4)$$: 0에서 시작하는 길이 4 구간 $$= 0100 + 0000 = 0100$$

전부 $$tree_4$$에서 겹친다. $$tree_4$$ 아래에 지워진 node가 (1), (2) node다.

###### update

이 특징을 이용해 update를 해보자.

1. 어떤 node를 update했다면 segment tree에서 해당 node부터 root까지 parent node를 update시켜야 한다.
2. parent node의 구간은 left child node의 구간과 시작점은 같고, 길이는 두 배다.
3. 따라서 fenwick tree에서 어떤 node에 lsb를 더해 주면(길이를 두 배로 해주면) parent node가 된다.
4. fenwick tree에서 root의 index는 $$n$$이다.

이를 코드로 나타내면 아래와 같다.

```cpp
void update(int i, int val) {
    for (++i; i <= n; i += i & -i)
        bit[i] += val;
}
```

###### query

query는 좀 복잡해진다.

query 구간을 존재하는 node와 지워진 node를 합쳐서 고려해야 하기 때문이다. 하지만 시작점을 처음으로 고정시킨다면(항상 $$[0,k]$$ 꼴의 구간 query만 주어진다면) 구하기 쉬워진다. update와 반대로 계속 lsb를 빼주면 처음부터 해당 지점까지의 구간을 표현하는 모든 node를 지나게 된다.

```cpp
// return sum[0,i]
int query(int i) {
    int ret = 0;
    for (++i; i; i -= i & -i)
        ret += bit[i];
    return ret;
```

이를 이용해 임의의 구간 $$[l,r]$$에서의 구간 합을 $$\text{sum}[l,r] = \text{sum}[0,r] - \text{sum}[0,l-1]$$을 이용해 구할 수 있다.

###### when to use

query 구간의 시작점이 처음에 고정되어 있다면 일반적인 segment tree를 대체할 수 있다.

임의의 구간을 처리해야 한다면 왼쪽 구간 $$[l, m)$$과 전체 구간 $$[l, r)$$의 값으로 오른쪽 구간 $$[m, r)$$의 값을 구할 수 있는 경우에만 사용해야 한다.

예를 들어 $$f(x) = \text{sum}(x)$$의 경우, $$f[m, r) = f[l, r) - f[l, m)$$이 성립하므로 fenwick tree를 쓸 수 있다. 그리고 대부분의 프로그래머가 이 경우에만 fenwick tree를 사용한다.

$$f(x) = \min(x)$$의 경우, $$f[m, r) = f[l, r) - f[l, m)$$이 성립하지 않으므로 fenwick tree를 쓸 수 없다. 물론 query 구간의 시작점이 처음에 고정되어 있다면 쓸 수 있다.

두 개의 fenwick tree를 사용하여 range minimum query를 처리하는 방법도 있으나 여기서는 일반적인 구현만을 다루었다.

## Code

```cpp
template <typename T>
struct BIT {
    vector<T> vt;
    BIT(int n) {
        vt.resize(n + 1);
    }
    void update(int i, T val) {
        for (++i; i < vt.size(); i += i & -i)
            vt[i] += val;
    }
    // return sum [0,i)
    T query(int i) {
        T ret = 0;
        for (++i; i; i -= i & -i)
            ret += vt[i];
        return ret;
    }
    // return sum [l,r)
    T query(int l, int r) {
        T ret = 0;
        for (; l; l -= l & -l)
            ret -= vt[l];
        for (; r; r -= r & -r)
            ret += vt[r];
        return ret;
    }
};
```

## Problems to try

###### k-th number

[\[백준 12899\] 데이터 구조](/baekjoon/12899)

새로운 수를 추가/삭제하는 쿼리와 전체 수 중 k번째 수를 찾는 쿼리를 해결하는 문제


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

## Code

[LINK](https://github.com/PaliLo815/Team-Library/blob/f22c5f557775e116601061ed192ec6da4dc70890/data/disjoint_set.cpp)

```cpp
/**
 * @brief 
 *      Binary Indexed Tree (a.k.a. Fenwick Tree)
 * 
 * @warning 
 *      `query(l, r)` and `get(i)`  must be used when inverse operation exists    
 * 
 * @note
 *      `lower_bound(k)`
 *          @return minimum i s.t. sum[0...i] >= k
 *      `upper_bound(k)`
 *          @return minimum i s.t. sum[0...i] > k
 */
template <typename T = int>
class BIT {
    const int n;
    vector<T> tree;

public:
    BIT(int _n) : n(_n), tree(_n + 1) {}

    void update(int i, T val) {
        assert(0 <= i and i < n);
        for (++i; i <= n; i += i & -i)
            tree[i] += val;
    }
    T query(int i) {
        assert(0 <= i and i <= n);
        T ret = 0;
        for (; i; i &= i - 1)
            ret += tree[i];
        return ret;
    }
    T query(int l, int r) {
        assert(0 <= l and l <= r and r <= n);
        return query(r) - query(l);
    }
    T get(int i) {
        assert(0 <= i and i < n);
        return i & 1 ? query(i, i + 1) : tree[i + 1];
    }
    int lower_bound(T k) {
        if (k <= 0) return -1;
        int x = 0;
        for (int pw = 1 << __lg(n); pw; pw >>= 1)
            if ((x | pw) <= n && tree[x | pw] < k)
                k -= tree[x |= pw];
        return x;
    }
    int upper_bound(T k) {
        if (k < 0) return -1;
        int x = 0;
        for (int pw = 1 << __lg(n); pw; pw >>= 1)
            if ((x | pw) <= n && tree[x | pw] <= k)
                k -= tree[x |= pw];
        return x;
    }
};
```
