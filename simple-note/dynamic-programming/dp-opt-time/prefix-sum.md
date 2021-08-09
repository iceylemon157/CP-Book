---
description: 施工中
---

# 前綴和優化

## 概述

有時候 DP 我們會需要加一段連續區間的值，或者是需要前綴 max/min 的資訊，這個時候我們就可以在枚舉狀態的過程中順便維護一個前綴\(後綴\)的資訊來加速轉移。

正式一點來講，如果有類似以下的轉移式：

> $$dp(i)=\sum\limits_{l\leq j\leq r}g(j)$$ \($$g(j)$$是一個可以包含$$dp(j)$$的函數\)

那我們就可以透過在做 DP 的過程順便維護$$dp$$的前綴和來快速求值。

## 題目

1. [ABC 212E](https://atcoder.jp/contests/abc212/tasks/abc212_e)
2. [LG P2885](https://www.luogu.com.cn/problem/P2885)
3. [LG P4099](https://www.luogu.com.cn/problem/P4099)

