# 狀壓DP

貌似有人叫他位元DP，不要跟數位DP搞混了喔。\(個人比較常叫他狀壓DP\)

## 介紹

狀壓 DP，全名叫做狀態壓縮，簡單來說就是把狀態壓縮在整數裡面。最常見的是壓縮成二進制整數。這篇我們只考慮壓縮成二進制整數的作法。

## 2^N DP

這個就是最基本的二進制壓縮 DP。主要就是利用二進制代表一個集合選或不選的狀態。

一個常用的狀態設計：  
令$$mask$$為當前狀態 \(一個二進制整數\)  
$$cnt(mask)$$ = $$mask$$ 中 $$1$$ 的數量  
$$dp(mask)$$ 代表前 $$cnt(mask)$$ 個人狀態是 $$mask$$ 的答案

而轉移的過程大多是由 $$dp(mask\oplus2^i)\ \forall i \in mask$$去更新 $$dp(mask)$$

小補充

* 有一些原本是 $$N!$$ 的枚舉可以用狀壓 DP 壓成 $$2^N$$
* 小技巧：狀態可以加入 lowbit 作限制

### 例題

1. [基本題](https://atcoder.jp/contests/dp/tasks/dp_o)
2. [UVa 11084](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2025)
3. [經典題](https://cses.fi/problemset/task/1653)
4. [LG 2704](https://www.luogu.com.cn/problem/P2704) 題目簡單但細節頗
5. [CF11 D](https://codeforces.com/problemset/problem/11/D) 計算簡單環個數
6. [CF 11D延伸討論](https://codeforces.com/blog/entry/337)

## 枚舉子集的狀壓 DP

上面的狀態轉移是利用 $$mask$$中 $$1$$的個數去轉移，而這個做法則是利用 $$mask$$的所有子集去進行轉移。

最常見作法：先枚舉 $$dp[0\sim 2^n-1]$$，然後對每一個數再去枚舉他的子集。枚舉子集可以直接用 DFS 或者是下面的炫泡 for 迴圈寫法。個人比較推薦 for 迴圈，因為常數很小而且又好寫。

子集枚舉 for 迴圈版本：

![](https://i.imgur.com/G2m5IjD.png)

[\[for 迴圈版本證明\]](https://cp-algorithms.com/algebra/all-submasks.html)

值得注意的是這樣子的做法複雜度是 $$O(3^N)$$，而不是 $$O(4^N)$$

複雜度證明：$$(2+1)^n=\sum\limits_{i=1}^{n}2^i\cdot$$$$n\choose i$$

### 例題

1. [\[基本題\]](https://atcoder.jp/contests/dp/tasks/dp_u)
2. [\[最小分團\]](https://atcoder.jp/contests/abc187/tasks/abc187_f)
3. [\[LG 3959\]](https://www.luogu.com.cn/problem/P3959) 按層轉移
4. [\[CF1209 E2\]](https://codeforces.com/problemset/problem/1209/E2)



