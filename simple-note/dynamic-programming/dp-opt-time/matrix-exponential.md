# 矩陣快速冪優化

## 概述

如果你的 DP 是一個類似線性遞迴的型式的話，那麼有時候你可以把轉移的過程視為進行矩陣乘法，並且使用矩陣快速冪求解。一個最簡單的例子就是費式數列。

> $$F_i=F_{i-1}+F_{i-2}$$

那麼我們就可以寫成這樣的形式來做轉移：

> $$ \begin{pmatrix} 1 & 1\\ 1 & 0 \end{pmatrix} \begin{pmatrix} F_{i-1}\\ F_{i-2} \end{pmatrix}= \begin{pmatrix}  F_{i-1}+ F_{i-2}\\ F_{i-1} \end{pmatrix}= \begin{pmatrix}  F_{i}\\ F_{i-1} \end{pmatrix}$$

於是我們只要求出最左邊那個轉移矩陣的$$N$$次方就可以求出$$F_N$$了。而這樣子的複雜度會是$$O(k^3logN)$$，其中$$k$$為矩陣大小。

矩陣快速冪還有另外一個地方可以使用  
有時候在處理二維 DP $$dp(i,j)$$ 的時候，如果$$dp(i)$$的轉移全部來自$$dp(i-1)$$，而$$j$$又很小的話，那我們就可以把$$dp(i)$$視為一組長度為$$j$$的向量，而轉移的過程就變成$$dp(i-1)$$乘上一個轉移矩陣變成$$dp(i)$$。如果轉移式長得像下面這樣：

$$dp(i,x)=\sum\limits_{y}dp(i-1,y)$$  
那麼在轉移矩陣上$$row\ x,\ column\ y$$的位置就會$$+1$$。

這樣講可能有點難懂，直接上一題例題吧

## 例題 POJ 3734 Blocks

有$$N$$個方塊排成一列，用紅、藍、綠、黃四種顏色把每個方塊塗色。 將所有方塊上色後，求紅色和綠色方塊的數量都是偶數的上色方法有幾種？ 輸出該方法數 $$mod\ 10007$$ 後的結果。總共有$$T$$組輸入$$(N\leq10^9,\ T\leq 100)$$

如果先不管數據範圍的話，我們可以很容易列出一個 DP 式子：

$$dp(i,a,b,c,d)$$代表總共$$i$$個方塊，紅、藍、綠、黃分別有$$a,b,c,d$$個的答案。

因為我們只需要奇偶關係，所以只需要記錄奇偶性就好了。既然只記錄奇偶性的話我們就不妨用狀態壓縮把後面四維壓成一個二進整數。轉移的過程如下：

$$dp(i,x)=\sum\limits_{j=0}^3dp(i-1,x\oplus 2^j)$$

然而這題的數據範圍相當大，普通的 DP 是沒辦法處理的。看到第二維只有$$16$$我們可以考慮使用矩陣乘法優化，那轉移矩陣要怎麼寫呢？其實上面這個 DP 式就有點線性遞迴的感覺了。如果把他拆開來寫就會變成：$$dp(i,x)=dp(i-1,x\oplus 2^0)+dp(i-1,x\oplus 2^1)+dp(i-1,x\oplus 2^2)+dp(i-1,x\oplus 2^3)$$

那我們就可以按照他的規律把轉移矩陣寫出來。  
四個顏色寫出來的矩陣會有點大，我們先考慮只有兩種顏色的情形：

> $$ \begin{pmatrix} 0 & 1 & 1 & 0\\ 1 & 0 & 0 & 1\\ 1 & 0 & 0 & 1\\  0 & 1 & 1 & 0\end{pmatrix} \begin{pmatrix} dp_{i-1,3}\\  dp_{i-1,2}\\ dp_{i-1,1}\\ dp_{i-1,0}\\ \end{pmatrix}= \begin{pmatrix} dp_{i,3}\\  dp_{i,2}\\ dp_{i,1}\\ dp_{i,0}\\ \end{pmatrix}$$

基本上就是按照線性遞迴來把矩陣構造出來，四個顏色也同理。

所以我們就只需要對矩陣做$$N$$次方就可以求出$$dp_N$$陣列了。至於複雜度的部分就是$$16^3logN$$。

## 小知識

1. 通常會教你用矩陣快速冪優化的題目都會有一維特別大，一維比較小\(或者他根本就是線性遞迴\)。看到數字$$\leq 10^9$$時就可以考慮使用。

## 題目

1. [TIOJ 2053](https://tioj.ck.tp.edu.tw/problems/2053) 裸費氏數列
2. [HDU 2294](https://acm.hdu.edu.cn/showproblem.php?pid=2294)
3. [CF 1182E](https://codeforces.com/contest/1182/problem/E)



