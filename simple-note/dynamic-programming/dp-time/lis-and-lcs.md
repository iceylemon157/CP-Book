---
description: 這裡介紹基本 LIS 跟 LCS 的作法
---

# LIS と LCS

## LIS 最長遞增子序列

這裡我們的遞增考慮非嚴格遞增，嚴格遞增也差不多。

### 題目+講解

#### 題目

給定一個陣列整數陣列$$a_i$$，求他的最長遞增子序列。定義子序列為從陣列中刪掉某幾個位置並且保持剩下的數字相對順序不變。

因為相信大家都已經會動態規劃了，這邊直接給出狀態以及轉移方程。

$$dp[i]=\max\limits_{j<i,a[j]\leq a[i]}\{dp[j]+1\}$$

如果直接計算，這個東西的複雜度是 $$O(N^2)$$。

而這題可以做到 $$O(NlogN)$$，主流有以下兩個做法：

1. Vector + 二分搜
2. BIT or 線段樹優化

這邊我們只給出 Vector + 二分搜的做法，

### Vector + 二分搜

這個做法的想法就是維護一個 Vector $$V$$，而 $$V[i]$$ 代表 **長度為** $$i$$ **增子序列的最後一個數字的最小值是多少。**會維護這個陣列是因為如果要加一個數到一個之前的 LIS，結尾的值越小越容易加進去。而一個簡單的觀察會發現這個陣列會是遞增的\(如果不能理解的話可以想想如果不是遞增會發生什麼事\)。可以知道這個 Vector 最後一個非零的位置就是 LIS 的長度。\(因為 LIS 是最長遞增子序列\)

所以我們就在枚舉的過程中順便維護這個陣列。而當我們處理到第 $$i$$ 個數字的時候，我們就比較說 $$a[i]$$是否大於放入 Vector 的最後一個數字，也就是做完前 $$i$$ 個數字的最大 LIS 長度。如果 $$a[i]$$ 比較大的話就把 $$a[i]$$ 加到 Vector 的最後面，意思也就是 LIS 長度增加 $$1$$。反之就代表有某一個長度的遞增子序列的最小值可以被換成 $$a[i]$$。因為這個 Vector 是遞增的，所以可以利用二分搜來找到那個位置。

詳細細節請見下方程式碼。

```cpp
int a[105];
int LIS() {
    for(int i = 0; i < n; i ++) {
        if(!vc.empty() or vc.back() <= a[i]) vc.push_back(a[i]);
        else *vc.lower_bound(vc.begin(), vc.end(), a[i]) = a[i];
    }
}
```

### 例題

1. [裸題](https://zerojudge.tw/ShowProblem?problemid=d242)
2. [Almost 裸題](https://zerojudge.tw/ShowProblem?problemid=f608)
3. [拆成最少遞增子序列](https://atcoder.jp/contests/abc134/tasks/abc134_e\)
4. [N維偏序](http://domen111.github.io/UVa-Easy-Viewer/?103)
5. [LIS變形1](https://ac.nowcoder.com/acm/contest/11164/D?&headNav=acm)
6. [LIS變形2](https://codeforces.com/problemset/problem/1468/A)

## LCS 最長公共子序列

### 題目+講解

#### 題目

給你兩個字串$$a,b$$，請求出他們的最長公共子序列。這裡的子序列跟上面的定義一樣，只是從陣列換成字串。

這邊直接給出狀態跟轉移式

$$dp[i][j]$$ 代表 $$A$$ 中前 $$i$$ 個跟 $$B$$ 中前 $$j$$ 個的 LCS 長度

$$dp[i][j] = \max\{dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1] + (a[i] == b[j])\}$$

LCS 沒什麼好說的，直接來看例題

### 例題

1. [裸題](https://atcoder.jp/contests/dp/tasks/dp_f)
2. [Edit Distance](https://cses.fi/problemset/task/1639)
3. [LCS變形](https://atcoder.jp/contests/abc130/tasks/abc130_e)
4. [LCIS](https://codeforces.com/problemset/problem/10/D)



## 聽說 LCS 可以轉 LIS ?

什麼意思？[Almost 裸題](https://zerojudge.tw/ShowProblem?problemid=f608) 是一個 LIS 如果把 $$x$$ 座標換成 $$a_i$$， $$y$$ 座標換成 $$b_i$$ 求 LCS 就變成跟那題求的東西一樣了 $$\therefore LCS == LIS$$

什麼時候可以用? 只需要輸出長度的時候 你說建圖也要 $$NM$$？數字小的時候可以Counting sort。數字大就離散化

複雜度 $$O(Klog(min(N,M)) + R)$$。$$K$$是數對數目，$$N$$和 $$M$$是序列長度，$$R$$是數字範圍。

最差情況下比 $$O(NM)$$ 還糟，但大部分時間快一點。$$K$$ 越小時可以用

### 例題

1. [\[UVA 10635\]](https://vjudge.net/problem/UVA-10635)



