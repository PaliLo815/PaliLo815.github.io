---
layout: page
title: "Segment Tree with Lazy Propagation"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 수정 예정...

test test

<em>"<strong>This</strong> is an example sentence."</em> -<strong>OP</strong>

is it ok ??? 

<script src="https://gist.github.com/PaliLo815/b0532e77d798dad7c87428e04b54e7c4.js"></script>

/*{% gist PaliLo815/b0532e77d798dad7c87428e04b54e7c4 %}*/

## Introduction

[segment tree](/algorithm/segment_tree)에서 range update를 해보자.

1. 단순히 point update를 반복하는 경우, update 당 $$O(\log n)$$이 걸리니 range update는 $$O(n \log n)$$이 걸린다.
2. leaf node를 모두 update하고, Tree 전체를 다시 만들어주면 $$O(n)$$이 걸린다.

어찌됐건 두 방법 모두 효율적이진 않다. lazy propagation은 range update를 $$O(\log n)$$에 가능하게 해준다.

## Code

###### update & query

Segment Tree에서 구간 $$[l, r)$$를 나타내는 $$node_i$$에 대한 `update`와 `query` 함수다.

```
propagation(i) {

}

update(i) {
    if no overlap
        return

    if total overlap
        update lazy
        return

    propagation

    update(left child), update(right child)
    tree[i] = merge(tree[left child] and tree[right child])
}

query(i) {
    if no overlap
        return identity element

    if total overlap
        return tree[i]

    propagation

    return merge(query(left child) and query(right child))
}
```

`lazy`, `prop`를 제외하면 일반적인 Segment Tree의 top-down 구현방식이다. 각자가 어떤 역할을 하는지 알아보자.

###### lazy

앞서 말했다시피, range query에는 $$O(n \log n)$$ 또는 $$O(n)$$이 걸린다.

아래의 Segment Tree를 보자.

![](/assets/img/algorithm/segment_tree_lazy/0.png)

구간 $$[3,15)$$를 update를 했을 때, 영향을 받는 node는 빨간색 부분이다. $$[3,15)$$에 속하는 모든 부분 구간을 다 update하면 얼마나 비효율적인지 확연히 알 수 있다. 

Lazy Propagation의 기본 아이디어는 여기서부터 시작된다.

1. Segment Tree와 같은 크기의 Lazy Tree를 만든다.
2. update할 때, total overlap인 node를 만나면 그 child node부터는 update하지 않는다. 단, 해당 위치의 Lazy Tree에 **표시**를 해둔다.
3. 어떤 node를 update했다면 그대로 종료한다. 즉, child node는 신경쓰지 않는다.




###### prop

```cpp
prop(i) {
    add lazy[i] to lazy[left]
    add lazy[i] to lazy[right]
    lazy[i] = 0
}
```

###### fix

```cpp
fix(i) {
    node[i] = (node[left] and lazy[left]) and (node[right] and lazy[right])
}
```


## Problems to try

```cpp
struct node {
    int sum, lazy;
};

inline int calc(int i) {
    return segT[i].sum + segT[i].lazy * (sgN >> 31 - __builtin_clz(i));
}
inline void prop(int i) {
    segT[i].sum = calc(i);
    segT[left].lazy += segT[i].lazy, segT[right].lazy += segT[i].lazy;
    segT[i].lazy = 0;
}
void update(int ql, int qr, int qv, int l = 0, int r = sgN, int i = 1) {
    if (qr <= l || r <= ql) return;
    if (ql <= l && r <= qr) {
        segT[i].lazy += qv;
        return;
    }
    if (segT[i].lazy) prop(i);

    int m = (l + r) >> 1;
    update(ql, qr, qv, l, m, left), update(ql, qr, qv, m, r, right);
    segT[i].sum = calc(left) + calc(right);
}
int query(int ql, int qr, int l = 0, int r = sgN, int i = 1) {
    if (qr <= l || r <= ql) return 0;
    if (ql <= l && r <= qr) return calc(i);
    if (segT[i].lazy) prop(i);

    int m = (l + r) >> 1;
    return query(ql, qr, l, m, left) + query(ql, qr, m, r, right);
}
```
