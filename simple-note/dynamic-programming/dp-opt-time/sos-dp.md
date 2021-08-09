---
description: >-
  (under construction) SOSDP，Sum over Subsets Dynamic
  Programming，又稱高維前綴和。多用來解決子集類的求和問題
---

# SOS 優化

## 概述

還記得枚舉子集的 DP 嗎？如果忘記的話請左轉 [這篇](https://app.gitbook.com/@oosheepyerd79135/s/test/simple-note/dynamic-programming/dp-time/zhuang-ya-dp#mei-ju-zi-ji-de-zhuang-ya-dp)  
他的複雜度是 $$3^N$$，但是在某些特殊的情況下，可以利用 SOS 優化把複雜度加速到 $$N\cdot 2^N$$。

為了講解 SOS DP 我們先引入一道簡單的題目：

> 給你一個有長度為$$2^N$$的陣列$$A_i$$，請你對於$$i\in [1,2^N]$$計算出$$F(i)$$，其中$$F(i)=\sum\limits_{j\subseteq i}a[i]$$。$$j$$也就是$$i$$的子集。

這個問題看起來並不是 DP，就只是單純求和而已。我們在枚舉子集DP 那邊已經講過了，我們可以$$3^N$$把這個問題解決。而 SOS DP 告訴我們其實他可以被做到$$N2^N$$。

我們在枚舉的時候，事實上重複枚舉到了很多東西。要怎麼加速呢？想想 DP 的精隨，就是算過的東西不要重複計算。所以我們可以考慮使用 DP 讓他不要重複算那麼多次。而 DP 在做的事情就是把一個問題分成不相關的許多子問題然後分別求解並且合併。所以如果我們想要用 DP 來做這題的話，那我們勢必就需要把$$F(i)$$這個狀態分成若干個不相關的子問題。

所以我們可以考慮以下的 DP 狀態：

> 令$$dp(mask,i)$$代表$$mask$$的所有子集中最低$$i$$\(0-base\)位跟$$mask$$不一樣的子集。

舉個例子$$dp(100101,2)=\{100101,100100,100001,100000\}$$  
只有最後面$$3$$位\(因為是0-base\) 是不一樣的。

那麼我們就可以把他分成若干個不相干子集了，轉移式如下：

> $$
> dp(mask,i)=
> \left\{
>     \begin{array}.
>     dp(mask,i-1), & if\ 第i位為0.\\
>     dp(mask,i-1)\bigcup dp(mask\oplus2^i,i-1), & otherwise.
>     \end{array}
> \right.
> $$

* 如果第$$i$$位是$$0$$的話，那麼$$dp(mask,i)$$跟$$dp(mask,i-1)$$所包含的子集是一模一樣的
* 如果第$$i$$位不為$$0$$的話，就要結合有第$$i$$位有跟沒有的兩個子集

那麼最後$$dp(mask,n-1)$$就會是每一個$$mask$$的子集了。

現在我們有子集了，如果我們要求解上面那題的話那就對上面的 DP 式作一點修改就可以了。



