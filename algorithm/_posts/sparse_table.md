---
layout: page
title: "Sparse Table"
category: algorithm
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Tutorial

range query를 처리하는 대표적인 자료구조는 [segment tree](segment_tree)와 sparse table입니다.

segment tree의 경우 주어진 구간을 $$O(\log n)$$개의 구간으로 **똑똑하게** 나누어서 해결합니다.

![](/assets/img/algorithm/sparse_table/0.jpg)

예) 구간 $$[3, 15)$$를 $$[3, 4) + [4, 8) + [8, 12) + [12, 14) + [14, 15)$$로 나누어서 해결한다.

* * *

여기서 잠깐 overlap-friendly에 대해 설명하겠습니다.

계산하는 구간 중에 겹치는 부분이 있더라도 결과가 같은 경우 overlap friendly라고 합니다.

예) $$f(l, r) = \min{a_i} \ (l \le i \lt r) $$

![](/assets/img/algorithm/sparse_table/1.jpg)

$$f(4, 13) = \min(f(4, 10),f(7, 13))$$임을 알 수 있습니다. 중간에 $$[7, 10)$$ 구간이 겹쳤지만 상관없습니다.

$$\text{min, max, gcd}$$등이 대표적인 overlap-friendly 함수입니다.

* * *

만약 overlap-friendly한 query를 처리해야 하는 경우, segment tree처럼 구간을 **똑똑하게** 나눌 필요가 없습니다. 구간이 겹치는지 상관 안 하고 그냥 대충 큼직하게 나누어도 됩니다.

여기서 나온 자료구조가 sparse table입니다. sparse table은 주어진 구간을 단 두 개의 부분 구간으로 나눠서 해결합니다. 즉, 시간 복잡도가 $$O(1)$$입니다. 하지만 공간 복잡도는 $$O(n \log n)$$으로 늘어납니다.

**NOTE**: sparse table도 query 구간을 겹치지 않게 쪼개서 해결할 수 있습니다. 하지만 이 경우 시간 복잡도가 $$O(\log n)$$이 돼서 segment tree와 같아지므로, 공간 복잡도가 $$O(n)$$인 segment tree쪽이 더 낫습니다.
{:.message}

## Build Table

###### Table Size

sparse table에서 $$cell_{i,j}$$는 구간 $$[j,j+2^i)$$를 뜻합니다. 전체 구간 $$[0, n)$$에 대해 가능한 $$i,j$$의 범위를 생각해보면...

$$0 \le i \le \log_2 n$$이므로 $$Row$$는 $$(\lfloor \log_2 n \rfloor + 1)$$개 필요합니다.

$$0 \le j \lt n$$이므로 $$Column$$은 $$n$$개 필요합니다.

> Table Size: $$(\lfloor \log_2 n \rfloor + 1) \times n$$

###### How to Fill

다음과 같은 array에서 RMQ(range minimum query)를 구하는 sparse table을 만들어봅시다.

![](/assets/img/algorithm/sparse_table/2.jpg)

$$cell_{i,j} = \text{구간 } [j,j+2^i)$$임을 상기합시다.

$$i = 0$$일 때는 구간의 길이가 1이므로 $$cell_{0,j} = a_j$$입니다.

![](/assets/img/algorithm/sparse_table/3.jpg)

_ _ _

예) $$cell_{2,5}$$
{:.lead}

구간 $$[5,9)$$에서의 최솟값이다.

![](/assets/img/algorithm/sparse_table/4.jpg)

_ _ _

예) $$cell_{3,2}$$
{:.lead}

구간 $$[2,10)$$에서의 최솟값이다.

![](/assets/img/algorithm/sparse_table/5.jpg)

_ _ _

예) $$cell_{2,10}$$
{:.lead}

구간 $$[10,14)$$에서의 최솟값이다. 하지만 문제가 생깁니다. $$a_{13}$$은 존재하지 않는데 최솟값을 어떻게 채워야 할까요?

![](/assets/img/algorithm/sparse_table/6.jpg)

이 경우 **채우지 않습니다**.

따라서 `sparse table`에서 사용되지 않는 부분이 여러 개 있습니다. 사용하지 않는 부분을 모두 지우면 아래처럼 됩니다.

![](/assets/img/algorithm/sparse_table/7.jpg)

그림에서 볼 수 있듯, $$Row_i$$에서 실제로 사용하는 $$cell$$은 $$N - (2^i-1)$$개입니다. 2d-array를 한 번에 만드는 게 아니라, vector나 malloc을 사용해 필요한 양만 만들 수도 있습니다.

- - -

###### Using Dynamic Programming

$$cell_{i,j} = \text{구간 } [j,j+2^i)$$이므로 $$cell_{i,j}$$를 계산하는데 드는 복잡도는 $$O(2^i)$$입니다. 하지만 이미 계산된 값을 이용한다면(dynamic programming) $$cell$$당 $$O(1)$$으로 sparse table을 채워나갈 수 있습니다.

![](/assets/img/algorithm/sparse_table/8.jpg)

$$[j,j+2^i) = [j,j+2^{i-1}) + [j+2^{i-1},j+2^i)$$로 나눌 수 있으므로, $$Row_i$$를 채울 땐 $$Row_{i-1}$$에서 값 2개만 보면 됩니다.

- - -

완성된 `sparse table`의 모습

![](/assets/img/algorithm/sparse_table/9.jpg)

## Query

query(l, r)을 구해봅시다.

$$k = \lfloor log_2(r - l) \rfloor$$이라 하면 $$[l, l + 2^k), [r - 2^k, r)$$으로 $$[l, r)$$을 완전히 덮을 수 있습니다.

**단 $$[r - 2^k, l + 2^k)$$의 겹치는 구간이 생기죠. 따라서 overlap friendly인 경우에만 해당합니다.**

예) query(1, 12)

![](/assets/img/algorithm/sparse_table/a.jpg)

$$
\begin{aligned}
k & = \lfloor log_211 \rfloor       \\
  & = 3                             \\
\\
[1, 12) & = [1,1+2^3) \cup [12-2^3,12)     \\
        & = [1,9) \cup [4,12)              \\
\\
query_{1,12} & = \min(cell_{3,1}, cell_{3,4})   \\
             & = -1
\end{aligned}
$$

## Code

```cpp
// ** WARNING **
// only for overlap-friendly function !!!
template <typename T, class F = function<T(const T&, const T&)>>
class sparse_table {
public:
    vector<vector<T>> dp;
    int n;
    F func;

    sparse_table(const vector<T>& a, const F& f) : func(f) {
        n = a.size();
        int row = 32 - __builtin_clz(n);
        dp.resize(row);
        dp[0] = a;
        for (int i = 1, k = 1; i < row; ++i, k <<= 1) {
            dp[i].resize(n - (k << 1) + 1);
            for (int j = 0; j + (k << 1) <= n; ++j)
                dp[i][j] = func(dp[i - 1][j], dp[i - 1][j + k]);
        }
    }
    T query(int l, int r) {
        assert(0 <= l && l < r && r <= n);
        int k = 31 - __builtin_clz(r - l);
        return func(dp[k][l], dp[k][r - (1 << k)]);
    }
};
```