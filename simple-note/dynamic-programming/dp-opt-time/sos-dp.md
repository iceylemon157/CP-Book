---
description: under construction
---

# SOS 優化

## 概述

還記得枚舉子集的 DP 嗎？如果忘記的話請左轉 [這篇](https://app.gitbook.com/@oosheepyerd79135/s/test/simple-note/dynamic-programming/dp-time/zhuang-ya-dp#mei-ju-zi-ji-de-zhuang-ya-dp)  
他的複雜度是 $$3^N$$，但是在某些特殊的情況下，可以利用 SOS 優化把複雜度加速到 $$N\cdot 2^N$$。

為了講解 SOS DP 我們先引入一道簡單的題目：

> 給你一個有長度為$$2^N$$的陣列$$A_i$$，請你對於$$i\in [1,2^N]$$計算出$$F(i)$$，其中$$F(i)=\sum\limits_{j\subseteq i}a[i]$$。也就是$$j$$是$$i$$的子集。

這個問題看起來並不是 DP，就只是單純求和而已。我們在枚舉子集DP那邊已經講過了，我們可以$$3^N$$把這個問題解決。而 SOS DP 告訴我們其實他可以被做到$$N2^N$$。

我們在枚舉的時候，事實上重複枚舉到了很多東西。想想DP的精隨，就是算過的東西不要重複計算。所以我們可以考慮用DP讓他不要算那麼多次。DP在做的事情就是把一個問題分成不相關的許多子問題然後分別求解並且合併。所以如果我們想要用DP來做SOS的話，那我們勢必就需要把$$F(i)$$這個狀態分成若干個不相關的子問題。

