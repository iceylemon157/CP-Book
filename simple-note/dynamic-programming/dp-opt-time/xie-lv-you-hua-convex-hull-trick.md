# 斜率優化 \(Convex Hull Trick\)

前面的都是比較簡單一點的DP優化，從這裡開始會變得稍微難一點。

## 介紹

斜率優化應該不算太難的 DP 優化，而且出現的頻率也絕對不在少數。

雖然說他的名字叫做斜率優化，但是我個人覺得凸包優化 \(英文名字\) 比較適合他。理由的話我們就繼續看下去吧。

## 概述

### 理論

有一些 1D/1D 的 DP 的轉移式會長成下面這樣：

$$dp(i)=h(i)+\max\limits_{j\leq r(i)}\{A(j)X(i)+B(j)\}$$

其中 $$r(i)<i$$是一個遞增函數、$$X(i)$$和 $$h(i)$$是容易求得的函數，而 $$A(j)$$、$$B(j)$$裡面可能會包含 $$dp(j)$$，也就是 $$j$$是狀態 $$i$$ 的子問題。這樣子的複雜度是 $$O(N^2)$$的，而斜率優化就是在優化這種類型的轉移式。因為 $$h(i)$$是一個容易求得的函數，而真正難算的部分在於後面的 $$max$$，所以下面說明後面那坨東西要怎麼優化。

最一開始有提到，DP 優化大多都是優化轉移的過程，斜率優化也不例外，就是要加速轉移的過程。為了加速轉移的過程，我們可以拋棄掉那些不可能成為轉移來源的狀態不去處理，那要怎麼做呢？

對於每個**已經算出答案的** $$j$$\(也就是說 $$A(j)$$和 $$B(j)$$都已經被算出來了\)，我們畫一條直線 $$y=A(j)x+B(j)$$到平面上。那麼結果可能會長得跟下面這張圖這樣 \(考慮 $$j\in[0,4]$$\)：

\(圖片待補\)

我們會發現所有數字的最大值會形成一個**下凸包**。而如果有線完全落在這個下凸包的下面\(例如 $$j=3$$那條線\)，那麼這條線就永遠不可能會成為我們的轉移來源。換句話說，我們的轉移來源只會出現在那個下凸包上面。假設我們現在要更新 $$dp(i)$$這個狀態，我們的轉移就相當於是在這個下凸包上找到 $$X(i)$$這個點是落在下凸包哪一條線段上。把 $$X(i)$$帶入那個線段所得到的值就會是我們的 $$dp(i)$$。

所以說我們現在把題目變成要做兩件事：

1. 動態維護下凸包
2. 快速找到 $$X(i)$$這個點是落在哪一個線段上，並求他的值

### ~~動態凸包?~~

我們現在要維護一個支持**插入操作**跟**查詢操作**的凸包。事實上真的有動態凸包這個東西。但是身為一個佛系競程玩家，我不想學那種又長又不好寫常數又大的東西。所以我們這邊要使用一個神奇的資料結構：李超線段樹

### 李超線段樹

這個東西其實應該被放在資料結構裡面，但是因為 iceylemon 很懶，所以他把決定先寫在這邊。

我們用一個問題來引入李超線段樹這個東西：

> 現在要進行若干次操作，每一次操作會是下列兩種中的一種：
>
> * 插入一條直線
> * 詢問一條直線 $$x=t$$與其他直線相交的點中，最大的 $$y$$ 座標

沒錯，這就是一個動態凸包問題 \(就是我們要解決的問題\)。而李超線段樹就是一個用來解決這類問題的**線段樹變形**。

李超線段樹的理念如下：對**值域**開線段樹。而每個節點維護的是讓這個區間**中點**的值最大的那**一條直線**。注意，每一個節點只維護一條直線。這看起來像是一個奇怪的定義，別急，我們繼續看下去。

考慮 $$[L,R]$$ 這個區間\(線段樹節點\)，不妨先假設原先這個節點維護的是 $$B$$這條直線，而我們現在要**插入**$$A$$這條直線 \(不妨先考慮$$A$$的斜率大於$$B$$\)。那麼應該會有兩種情況，$$A$$帶入$$mid$$ 比較大 \(如下圖\)，或是$$B$$帶入$$mid$$ 比較大。

![](../../../.gitbook/assets/tu-pian-1%20%281%29.png)

上圖顯示 $$A$$ 帶入 $$mid$$ 的值比較大。根據李超線段樹的定義，$$[L,R]$$ 這個節點應該要保留$$A$$這條直線，但是我們會發現$$B$$直線仍然是有可能會被選到的，不能直接把他丟掉。我們看著上面這張圖可以發現，$$B$$這條直線接下來只有可能會在$$mid$$的左邊會被選到。還記得李超線段樹是線段樹吧?我們可以直接往左邊遞迴下去做就可以處理到$$B$$直線的情形了。同理如果 $$B$$ 帶入的 $$mid$$ 值比較大，我們也可以用類似的方法處理。

詳細細節我們可以看下面的程式碼：

李超線段樹插入程式碼

```cpp
struct Line {
    int m, k;
    int val(int x) {
        return m * x + k;
    }
} seg[maxn * 4]; 
// seg 李超線段樹用到的陣列

void insert(Line line, int l, int r, int idx) {
    // line = 要插入的直線
    // 目前處理的區間 [l,r]
    // 對應到線段樹哪一個編號的節點
    if(l == r) {
        // 如果 line 的值到底了還比原本的大，那就直接取代掉
        if(line.val(l) > seg[idx].val(l)) {
            seg[idx] = line;
            return;
        }
    }
    int mid = (l + r) >> 1;
    // 預設要插入的直線斜率比較大
    if(seg[idx].m > line.m) swap(seg[idx], line);
    // 看要往哪邊遞迴
    if(seg[idx].val(mid) < line.val(mid)) {
        swap(line, seg[idx]);
        // 這邊使用 swap 是因為上面有可能 swap 過了
        // 我們不知道現在手上拿的是誰，反正比較弱的就丟下去就對了
        insert(line, l, md, idx << 1);
    }
    else insert(line, md + 1, r, idx << 1 | 1);
}
```

那麼查詢呢？你可能會想要直接查詢最下面那個節點的直線帶進去的值，但很不幸的那是錯的。

回憶一下上面的過程，你可能會發現我們在處理區間的時候只有一直往下處理 $$mid$$值比較小的那條直線可能會被選到的區間。也就是說另外半邊的區間我們是沒有處理的。所以說查詢的過程應該是要由上往下經過的每一個節點都把$$x$$帶進那條直線並取所有值的 $$max$$才是對的。

李超線段樹詢問

```cpp
int query(int x, int l, int r, int idx) {
    // x 是要查詢的點
    if(l == r) return seg[idx].val(x);
    int mid = (l + r) >> 1, ret = seg[idx].val(x);
    if(x <= mid) ret = max(ret, query(x, l, mid, idx << 1));
    else ret = max(ret, query(x, mid + 1, r, idx << 1 | 1);
    return ret;
}
```

我們可以發現全部的操作都跟線段樹一樣，所以總複雜度是 $$O(NlogN)$$

於是我們就可以使用李超線段樹解決上面的問題了。至此斜率優化就這樣被解決了。

## 特例

雖然我們已經把斜率優化解決掉了，但是斜率優化還有一些很有趣的特例。

上面的那個作法是最通用的作法，而如果題目有給定一些特別的性質的話，會有一些更好的做法。

### 斜率單調

如同這一小節的標題，如果我們的斜率單調遞增\(也就是 $$a(i)$$會隨著 $$i$$變大而增加\)，那麼會有一個不用寫線段樹的做法。

由於下凸包這個東西如果 $$x$$座標越大，那麼斜率就會越大。如果我們插入的直線斜率是遞增的話，這不就代表說這條直線只會差在這個下凸包的最右邊嗎？也就是說我們可以直接用一個單調 Stack 來維護我們的凸包。

至於查詢的部分，一個最暴力的作法當然是遍歷整個 Stack，找到 $$X(i)$$在哪一個區間。但是因為區間這個東西是單調遞增的，所以其實我們可以直接對他做二分搜。至此我們獲得了一個 $$O(NlogN)$$但常數比較小的辦法了。

但老實說，我認為如果題目沒有特別卡的話，寫線段樹也不失為一個不錯的選擇，畢竟寫線段樹就不太需要思考 \(x。\(我好像有點毒?\)

### 查詢單調

上面那個做法只是常數比較小而已，那如果我現在再加入查詢單調這件事會變成怎樣呢？

上面的作法需要使用二分搜來進行查詢，而既然現在給定查詢遞增，就代表我們查詢的區間的 $$x$$ 座標只會增加不會減少。也就是說我的查詢只會把值域遍歷一遍而已。所以查詢的複雜度也會是 $$O(N)$$。於是總複雜度就是 $$O(N)$$。

有沒有發現這個做法好像似曾相似？其實**單調隊列優化**就是斜率優化的特例喔！

## 一些你要知道的事

1. 斜率優化有一個重要的先決條件：取 $$max$$中 $$j$$左界必須固定，否則你就可能要支援一個可以動態插入跟刪除的凸包，就算用動態凸包也沒那麼神。但這有一些特例，因為有點複雜就不講了，但是一個最簡單的例子就是單調隊列優化。
2. 上面都只有提到$$max$$如果題目要問$$min$$怎麼辦？有一個可愛的東西叫做負號，用他就可以了。

## 例題

下面這題是一道過於經典的題目，建議大家先不要看題解自己先做。

### APIO 2010 Commando

[題目連結](https://tioj.ck.tp.edu.tw/problems/1745)

給你一個長度為 $$N$$的正整數列 $$x_1,...x_n$$以及一個二次方程 $$f(x)=ax^2+bx+c\ (a < 0)$$。你可以將這個數列分成連續的很多塊，而該分法的價值就是每塊的總和帶入 $$f$$之後的函數值的總和。求可以獲得的最大價值為何。

#### 題解

容易知道轉移式是 $$dp(i)=\max\limits_{j<i}\{dp(j)+f(\sum\limits_{k=j+1}^{i}x_k)\}$$，令 $$S(i)$$代表 $$\sum\limits_{j=1}^{i}x_j$$。那麼就可以變成：

$$dp(i)=\max\limits_{j<i}\{dp(j)+f(S(i) - S(j))\}\\ \ \ \ \ \ \ \ \ \ \ = \max\limits_{j<i}\{dp(j)+a(S(i)^2-2S(i)S(j)+S(j)^2)+b(S(i)-S(j))+c\}\\ \ \ \ \ \ \ \ \ \ \ =g(S(i))+\max\limits_{j<i}\{(-2aS(j))S(i)+(f(j)+aS(j)^2-bS(j))\}$$

然後我們就發現斜率優化已經成形了！

* $$A(j)=-2aS(j)$$
* $$B(j)=f(j)+aS(j)^2-bS(j)$$
* $$X(i)=S(i)$$
* $$h(i)=g(S(i))$$

那麼我們就可以套李超線段樹輕鬆解決這題了！但這題還可以做得更好！

* 斜率單調：$$S(j)$$遞增\(因為$$x_i$$都是正整數\)，而且 $$a<0$$。
* 查詢單調：$$S(i)$$遞增

所以我們就可以套單調隊列優化來解掉這題了。

接下來這題是一個很有趣的題目，大家可以自己想一下。

### CF 932F Escape Through Leaf

[題目連結](https://codeforces.com/contest/932/problem/F)

給你一個$$N$$個節點的有根樹，第$$i$$個節點有$$a_i,b_i$$兩個值。你可以從一個節點$$x$$跳到他的子樹的任意一個節點 $$y$$，並且花費$$a_x\cdot b_y$$的代價。對於每個節點$$x$$，請找出$$x$$經過若干次跳躍後到達任意一個葉節點的所花費的代價總和最小值。$$(1\leq N\leq 10^5)$$

#### 題解

容易想到一個暴力的 dp 做法，令 $$dp(x)$$代表所求。那麼轉移就會是

$$dp(x)=\min\limits_{y\in S_x}\{dp(y)+a(x)b(y)\}$$，其中$$S_x$$代表以$$x$$為根的子樹。他雖然長得一臉就是斜率優化的樣子，但是他是在樹上欸？這也就代表對於每一個節點$$x$$我都要把他的子樹全部遍歷一遍，然後把這些東西加到李超線段樹裡面維護。這樣顯然是不對的，但是真的需要那麼麻煩嗎？

這時候我們如果把焦點放在$$x$$跟他的小孩身上，就會發現如果他的每一個小孩都建好了一棵李超線段樹，那麼就只需要把他們全部合併就好了。嗯？合併？所以我們就可以合理的想到樹上啟發式合併啦！

但是李超線段樹要怎麼做樹上啟發式呢。總不能開$$N$$棵李超線段樹吧？等等，我不行嗎？別忘了李超線段樹也是線段樹！動態開點！至此我們就可以把這題用樹上啟發式+斜率優化+動態開點解掉啦~

但是因為作者太菜了，已經 $$N$$年沒有寫過動態開點這種東西\(其實我根本沒有想到\)。所以我就往其他的方向去想。因為一次插入只會影響到$$logN$$個節點，那我要刪除的時候就把那些被影響到的節點清空就可以了。因為插入多少節點就會清空多少節點，所以複雜度是不會影響的！那麼就可以不用再寫動態開點了。

程式碼如下

```cpp
#include "bits/stdc++.h"
#define int long long
#define f first
#define s second
#define endl '\n'
#define pb push_back
#define pii pair<int, int>
#define all(x) x.begin(), x.end()
#define mem(x, a) memset(x, a, sizeof(x))
#define FFOR(i, a, b) for(int i = a; i <= b; i ++)
#define FOR(i, n) FFOR(i, 1, n)
#define loli ios_base::sync_with_stdio(false), cin.tie(0);
using namespace std;
const int maxn = 1e5 + 50;

int n, m, q, k, ans, inf = 1e15;
vector<int> vc[maxn];
int dp[maxn], a[maxn], b[maxn];
int sz[maxn], mxson[maxn];

struct segment {
    int m = 0, k = -inf;
    int val(int x) {
        return m * x + k;
    }
} tr[maxn * 8];

struct LiChao {
    vector<int> vc;
    #define ls (idx << 1)
    #define rs (idx << 1 | 1)
    void clear() {
        for(int i : vc) tr[i] = {0, -inf};
        vc.clear();
    }
    void add(segment seg, int l = 0, int r = 2e5, int idx = 1) {
        vc.pb(idx);
        if(l == r) {
            if(seg.val(l) > tr[idx].val(l)) tr[idx] = seg;
            return;
        }
        int md = (l + r) / 2;
        if(seg.m < tr[idx].m) swap(seg, tr[idx]);
        if(seg.val(md - 1e5) > tr[idx].val(md - 1e5)) {
            swap(seg, tr[idx]);
            add(seg, l, md, ls);
        }
        else add(seg, md + 1, r, rs);
    }
    int qry(int v, int l = 0, int r = 2e5, int idx = 1) {
        if(l == r) return tr[idx].val(v);
        int md = (l + r) / 2;
        int ret = tr[idx].val(v);
        if(v <= (md - 1e5) ) ret = max(ret, qry(v, l, md, ls));
        else ret = max(ret, qry(v, md + 1, r, rs));
        return ret;
    }
} LCT;

void init(int x, int fa = -1) {
    int tmp = 0;
    sz[x] = 1;
    for(int i : vc[x]) {
        if(i == fa) continue;
        init(i, x);
        sz[x] += sz[i];
        if(tmp < sz[i]) mxson[x] = i, tmp = sz[i];
    }
}

inline int A(int x) {
    return -b[x];
}

inline int B(int x) {
    return -dp[x];
}

void upd(int x, int fa) {
    segment tmp = {A(x), B(x)};
    LCT.add(tmp);
    for(int i : vc[x]) {
        if(i == fa) continue;
        upd(i, x);
    }
}

void dfs(int x, int fa = -1) {
    for(int i : vc[x]) {
        if(i == fa or i == mxson[x]) continue;
        dfs(i, x);
        LCT.clear();
    }
    if(mxson[x]) dfs(mxson[x], x);
    for(int i : vc[x]) {
        if(i == fa or i == mxson[x]) continue;
        upd(i, x);
    }
    if(sz[x] != 1) dp[x] = -LCT.qry(a[x]);
    segment tmp = {A(x), B(x)};
    LCT.add(tmp);
}
 
void solve() {
    init(1);
    dfs(1);
    for ++FOR(i, n) cout << dp[i] << ' '; cout << endl;   
}

void input() {
    cin >> n;
    FOR(i, n) cin >> a[i], a[i] = -a[i];
    FOR(i, n) cin >> b[i], b[i] = -b[i];
    FOR(i, n - 1) {
        int a, b;
        cin >> a >> b;
        vc[a].pb(b);
        vc[b].pb(a);
    }
}

signed main(){
    loli;
    (
}
```

