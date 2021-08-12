---
description: 施工中
---

# 資料結構優化

## 概述

我們知道資料結構就是在幫我們快速的維護一些性質，讓我們可以快速地獲得一些數字。而有時候我們就可以利用這些資料結構 \(set, BIT, 線段樹 等等\) 來加速我們的轉移。

來回顧一個老問題：[LIS 最長遞增子序列](https://oosheepyerd79135.gitbook.io/iceylemon_cp/simple-note/dynamic-programming/dp-time/lis-and-lcs#lis-zui-chang-di-zeng-zi-xu-lie)

我們在前面的部分有介紹過使用Vector+二分搜來優化這個問題。而這個問題也可以使用 BIT 或 線段樹來做優化。先來回顧一下轉移式：

$$dp[i]=\max\limits_{j<i,a[j]<a[i]}\{dp[j]+1\}$$

假設我們已經做完$$1\sim i-1$$的 dp 陣列了，那麼基於 DP 優化就是在加速轉移速度的思想，我們要想辦法在比暴力快的方法得到$$dp(i)$$。

注意一下 max 下面放的東西，$$j<i,a[j]<a[i]$$，$$j<i$$已經被滿足了\(目前求出的所有 DP 值都小於$$i$$\)，所以我們要做的就是快速找到滿足$$a[j]<a[i]$$的所有 DP 值的最小值。白話一點來說，我們需要找到所有 $$a[j]$$的值比$$a[i]$$小中最大的$$dp(j)$$。

那麼我們就可以有一個想法，原本枚舉$$1\sim i-1$$沒有快速的做法，那如果我們枚舉$$1\sim a[i]-1$$呢？我們可以考慮維護一個陣列$$b$$，在$$b[a[j]]$$的位置上記錄$$dp[j]$$\(如果有兩個不同的 DP 在同一個位置，既最大值的那個\)。那麼我們就把問題變成掃過$$b$$中$$1\sim a[i]-1$$的所有位置並且找到最大值，也就是查詢前綴 max！於是我們就可以用一個熟知的資料結構 -- BIT 來把他解掉啦。

有些情況下$$a[i]$$的值可能會比較大，這時候直接離散化

{% code title="BIT 解 LIS 問題" %}
```cpp
int n, a[maxn], bit[maxn];
int low(x) { return x & -x; }
int qry(int x) 
```
{% endcode %}

## 題目

1. [CF 1557D](https://codeforces.com/contest/1557/problem/D) \(PS. 這題有不需要DP優化的作法 [這篇](https://oosheepyerd79135.gitbook.io/iceylemon_cp/sui-bi/ti-jie/cf-1557d)\)
2. [CF 1000F](https://codeforces.com/problemset/problem/1000/F)
3. [ARC 73F](https://atcoder.jp/contests/arc073/tasks/arc073_d)
4. [\[ZJOI2010\]基站選址](https://www.luogu.com.cn/problem/T175821)
5. [LG P5298](https://www.luogu.com.cn/problem/P5298)

