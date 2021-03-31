---
layout: article
title: Game Theory
aside:
  toc: true
tags:
- ✅Tutorial (Basic)
- game_theory
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Introduction

두 플레이어가 턴을 번갈아가면서 진행하는 어떤 게임이 있다고 하자.

게임의 현재 상태를 정점으로 표현한다면 플레이어가 하는 선택은 간선으로 나타낼 수 있다.

<center><img src="/assets/images/algorithm/game_theory/0.png"></center>
<center><i> ex) Tic Tac Toe <br> 왼쪽 게임판에서 다음 플레이어가 할 수 있는 선택은 여덟 가지다.</i></center>

그러면 게임의 모든 경우의 수를 표현한 그래프를 만들 수 있고, 게임을 하는 것은 그래프에서 어떤 경로를 따라 움직이는 것이다.

### Winning vertex & Losing vertex & Drawn vertex

어떤 정점(게임의 상태)이 되었을 때 승리한다면 그 정점은 winning vertex다.

마찬가지로 어떤 정점이 되었을 때 패배한다면 그 정점은 losing vertex, 무승부로 끝난다면 drawn vertex다.

Tic Tac Toe 게임에서 X 플레이어일 때의 예시는 아래와 같다.

| winning vertex | losing vertex | drawn vertex |
| -------------- | ------------- | ------------ |
| ![](/assets/images/algorithm/game_theory/1.png) | ![](/assets/images/algorithm/game_theory/2.png) | ![](/assets/images/algorithm/game_theory/3.png) |

## Tutorial

게임의 현재 상태를 **W** (winning vertex), **L** (losing vertex), **D** (drawn vertex) 중 하나로 나타내자.

게임이 끝난 상황(그래프에서 outdegree가 0인 정점)은 **W, L, D** 중 어느 상태인지 바로 알 수 있다. 그렇다면 그래프에서 임의의 정점이 어떤 상태인지 

if L in next state
    return W
if D in next state
    return D
return L

## Proof

$\sum_{i=0}^{\mathrm{to}-1}{(ki+c) \% m}$

## Code

```cpp

```

## header 2
### header 3
#### header 4
##### header 5

done
