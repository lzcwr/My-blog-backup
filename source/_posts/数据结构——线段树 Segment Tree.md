---
title: 数据结构——线段树 Segment Tree
date: 2017-02-17 17:37:37
categories: ACM
tags:
 - 数据结构
 - 线段树
description: "数据结构之线段树的原理与应用."
---

## 线段树的概述

线段树是一颗完全二叉树，它的每个结点都表示一条线段，可以用来解决连续区间的动态查询问题。
线段树**只支持区间信息可以由子区间信息合并而来的问题**（如最值、乘积、区间和等）。

 - 线段树的结构：一般来说，区间$~[a,b]~$的左儿子是$~[a,m]~$，右儿子是$~[m+1,b]~$；
 - 线段树的空间：若数组长度是$~N~$，线段树需要的最大空间为$~4N~$；
 - 线段树的效率：由于二叉树的性质，二叉树的操作时间复杂度基本保持在$~O(\log N)~$。
 
## 线段树的操作

线段树的主要操作有：建树、更新、查询。

### 建树

线段树的构造过程主要是递归构造：如果当前结点的区间**左右端点相等**，则给该节点**赋值**；若该结点区间**左右端点不相等**，则**递归构造它的两个子树**，构造完毕它的两个子树后再给该节点赋值。
建树的实例代码如下：

```cpp
int arr[N], tree[N << 2];
void build(int l, int r, int rt)
{
    if(l == r) tree[rt] = arr[l]; // 左右端点相等，为叶子结点，直接赋值
    else
    {
        int m = l + r >> 1;
        build(l, m, rt << 1); // 构建左子树
        build(m + 1, r, rt << 1 | 1); // 构建右子树
    // 由于不明确线段树存储的内容，故用push up函数来表示两个子区间的合并
        push_up(rt);
    }
}
```

例如下面的这段序列：

```cpp
arr[5] = {11, 12, 13, 14, 15};
```

记录其区间和的线段树如下：

```
                         ([0, 4]=65)
                       /            \
            ([0, 2]=36)             ([3, 4]=29)
            /          \            /         \
      ([0, 1]=23) ([2, 2]=13) ([3, 3]=14) ([4, 4]=15)
      /         \
([0, 0]=11) ([1, 1]=12)
```

记录其区间最大值的线段树如下：

```
                        ([0, 4]=15)
                       /            \
            ([0, 2]=13)             ([3, 4]=15)
            /          \            /         \
      ([0, 1]=12) ([2, 2]=13) ([3, 3]=14) ([4, 4]=15)
      /         \
([0, 0]=11) ([1, 1]=12)
```

### 查询

由于单点查询也可以视为左右端点相等的区间查询，故以下只讨论区间查询。
对于区间$~[a,b]~$，可以从根结点开始，递归地判断查询区间与当前结点的关系。
由线段树的特性可知，查询的过程中，在每一层选择的区间个数不会多余两个（如果一层选择了三个区间，则一定会有两个相邻的区间是同一个结点的儿子，因此这两个区间可以直接合并为它们的父结点区间。）
区间查询的示例代码如下（以记录区间和为例）：

```cpp
// [L, R]表示查询区间，rt表示当前区间标号，[l, r]为当前访问区间
int query(int L, int R, int l, int r, int rt)
{
    // 查询区间包含当前访问区间，直接返回当前区间的值
    if(L <= l && r <= R) return tree[rt];
    // 否则，查询当前区间的两个儿子区间，再合并
    int m = l + r >> 1， ret(0);
    if(m >= L) ret = query(L, R, l, m, rt << 1);
    if(m < R) ret += query(L, R, m + 1, r, rt << 1 | 1);
    return ret;
}
```

**复杂度的估计**：由于该树共有$~O(\log N)~$层，每层最多选择两个结点，故选择的结点个数也是$~O(\log N)~$，即查询的时间复杂度为$~O(\log N)~$。

### 更新

更新是线段树的核心操作，线段树需要维护的一切信息都要由更新操作来体现。
对于更新，一个关键的操作是把儿子的信息合并到父结点上，以求和为例，代码如下：

```cpp
void push_up(int rt)
{
    tree[rt] = tree[rt << 1] + tree[rt << 1 | 1];
}
```

#### 单点更新

单点更新的步骤非常简单：

 1. 找到需要更新的单点，进行更新操作；
 2. 利用`push_up()`函数更新相关区间信息。

仍然以区间求和为例，给出如下示例代码：
  
```cpp
// idx为更新的下标，val为更新的值，[l, r]为更新区间，rt为更新区间标号
void updata(int idx, int val, int l, int r, int rt)
{
    if(l == r) // 左右端点相等，进行更新
    {
        tree[rt] += val;
        return ;
    }
    int m = l + r >> 1; // 递归地搜索左右子树
    if(m >= idx) updata(idx, val, l, m, rt << 1);
    else updata(idx, val, m + 1, r, rt << 1 | 1);
    push_up(rt);
}
```


#### 区间更新

线段树更新中**最难理解的内容就是区间更新**。
区间更新需要用到**延迟标记**，即给每个结点新增一个标记，记录这个结点是否被做过修改。
对于任意区间的修改，我们按照如下方式进行操作：

1. 按查询的方式将其划分成线段树中的结点；
2. 修改这些结点的信息，并打上标记；
3. 在修改和查询的时候，每访问到一个结点，如果该结点有标记，则执行`push_down`；
4. `Push_Down`操作：
 - 按标记对子结点进行更新；
 - 给子结点都打上相同标记；
 - 消掉该结点的标记。
 
实例代码如下：

```cpp
void update(int L,int R,int c,int l,int r,int rt)
{  
    if (L <= l && r <= R)
    {  
        lazy[rt] += ...;
        tree[rt] += ...;
        return ;  
    }  
    PushDown(rt , r - l + 1); // 向下更新
    int m = (l + r) >> 1;  
    if (L <= m) update(L , R , c , l, m, rt << 1);
    if (m < R) update(L , R , c , m + 1, r, rt << 1 | 1);
    PushUp(rt); // 向上更新
}  
```

### 简化代码

在线段树中频繁用到的就是访问左儿子和访问右儿子两个操作，而查询等操作最常使用的就是根结点，我们可以利用宏定义将其简化：

```cpp
#define root 1, n, 1
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
```

## 基础练习题目


### HDU 4116 敌兵布阵
单点修改，区间查询：

```cpp
#include <cstdio>
#include <iostream>
using namespace std;
#define root 1, n, 1
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
const int MAXN = 5e4;
int sum[MAXN << 2];
void push_up(int rt)
{
    sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
}
void build(int l, int r, int rt)
{
    if(l == r)
    {
        scanf("%d", &sum[rt]);
        return ;
    }
    int m = l + r >> 1;
    build(lson); build(rson);
    push_up(rt);
}
void updata(int k, int d, int l, int r, int rt)
{
    if(l == r)
    {
        sum[rt] += d;
        return ;
    }
    int m = l + r >> 1;
    if(m >= k) updata(k, d, lson);
    else updata(k, d, rson);
    push_up(rt);
}
int query(int L, int R, int l, int r, int rt)
{
    if(L <= l && r <= R) return sum[rt];
    int m = l + r >> 1, res(0);
    if(m >= L) res = query(L, R, lson);
    if(m < R) res += query(L, R, rson);
    return res;
}
int main()
{
    int T;
    scanf("%d", &T);
    for(int ca = 1; ca <= T; ca++)
    {
        printf("Case %d:\n", ca);
        int n;
        scanf("%d", &n);
        build(root);
        char op[8];
        while(scanf("%s", op) && op[0] != 'E')
        {
            int a, b;
            scanf("%d%d", &a, &b);
            if(op[0] == 'A') updata(a, b, root);
            else if(op[0] == 'S') updata(a, -b, root);
            else printf("%d\n", query(a, b, root));
        }
    }
    return 0;
}
```

### HDU 1754 I hate it

单点更新，区间查询：

```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
const int maxn = 222222;
int MAX[maxn << 2];
void PushUP(int rt)
{
	MAX[rt] = max(MAX[rt << 1], MAX[rt << 1 | 1]);
}
void build(int l, int r, int rt)
{
	if (l == r)
	{
		scanf("%d", &MAX[rt]);
		return ;
	}
	int m = l + r >> 1;
	build(lson); build(rson);
	PushUP(rt);
}
void update(int p, int sc, int l, int r, int rt)
{
	if(l == r)
	{
		MAX[rt] = sc;
		return ;
	}
	int m = l + r >> 1;
	if(p <= m) update(p, sc, lson);
	else update(p, sc, rson);
	PushUP(rt);
}
int query(int L, int R, int l, int r, int rt)
{
	if(L <= l && r <= R) return MAX[rt];
	int m = l + r >> 1, ret(0);
	if(L <= m) ret = max(ret , query(L, R, lson));
	if(R > m) ret = max(ret , query(L, R, rson));
	return ret;
}
int main()
{
	int n, m;
	while (~scanf("%d%d",&n,&m))
	{
		build(1, n, 1);
		while(m--)
		{
			char op[2];
			int a, b;
			scanf("%s%d%d", op, &a, &b);
			if(op[0] == 'Q') printf("%d\n", query(a, b, 1, n, 1));
			else update(a, b, 1, n, 1);
		}
	}
	return 0;
} 
```

### HDU 1394 Minimum Inversion Number

单点更新，区间查询：

```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
const int maxn = 5555;
int sum[maxn << 2];
void PushUP(int rt)
{
	sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
}
void build(int l, int r, int rt)
{
	sum[rt] = 0;
	if(l == r) return ;
	int m = l + r >> 1;
	build(lson); build(rson);
}
void update(int p, int l, int r, int rt)
{
	if(l == r)
	{
		sum[rt]++;
		return ;
	}
	int m = (l + r) >> 1;
	if(p <= m) update(p, lson);
	else update(p, rson);
	PushUP(rt);
}
int query(int L, int R, int l, int r, int rt)
{
	if(L <= l && r <= R)
	{
		return sum[rt];
	}
	int m = l + r >> 1, ret(0);
	if(L <= m) ret += query(L, R, lson);
	if(R > m) ret += query(L, R, rson);
	return ret;
}
int x[maxn];
int main()
{
	int n;
	while (~scanf("%d", &n))
	{
		build(0, n - 1, 1);
		int sum = 0;
		for(int i = 0; i < n; i ++)
		{
			scanf("%d", &x[i]);
			sum += query(x[i], n - 1, 0, n - 1, 1);
			update(x[i], 0, n - 1, 1);
		}
		int ret = sum;
		for(int i = 0; i < n; i++)
		{
			sum += n - x[i] - x[i] - 1;
			ret = min(ret, sum);
		}
		printf("%d\n" ,ret);
	}
	return 0;
}
```

### POJ 3468 A Simple Problem with Integers

区间修改，区间求和（注意乘法会爆int）：

```cpp
#include <cstdio>
#include <algorithm>
using namespace std;
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
const int maxn = 111111;
long long lazy[maxn << 2];
long long sum[maxn << 2];
void PushUp(int rt)
{
    sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
}
void PushDown(int rt, int m)
{
    if (lazy[rt])
    {
        lazy[rt << 1] += lazy[rt];
        lazy[rt << 1|1] += lazy[rt];
        sum[rt << 1] += lazy[rt] * (m - (m >> 1));
        sum[rt << 1|1] += lazy[rt] * (m >> 1);
        lazy[rt] = 0;
    }
}
void build(int l, int r, int rt)
{
    lazy[rt] = 0;
    if (l == r)
    {
        scanf("%lld", &sum[rt]);
        return ;
    }
    int m = (l + r) >> 1;
    build(lson);
    build(rson);
    PushUp(rt);
}
void update(int L, int R, int c, int l, int r, int rt)
{
    if (L <= l && r <= R)
    {
        lazy[rt] += c;
        sum[rt] += (long long)c * (r - l + 1);
        return ;
    }
    PushDown(rt, r - l + 1);
    int m = (l + r) >> 1;
    if (L <= m) update(L, R, c, lson);
    if (m < R) update(L, R, c, rson);
    PushUp(rt);
}
long long query(int L, int R, int l, int r, int rt)
{
    if (L <= l && r <= R)
    {
        return sum[rt];
    }
    PushDown(rt, r - l + 1);
    int m = (l + r) >> 1;
    long long ret = 0;
    if (L <= m) ret += query(L, R, lson);
    if (m < R) ret += query(L, R, rson);
    return ret;
}
int main()
{
    int N, Q;
    scanf("%d%d", &N, &Q);
    build(1, N, 1);
    while (Q --)
    {
        char op[2];
        int a, b, c;
        scanf("%s", op);
        if (op[0] == 'Q')
        {
            scanf("%d%d", &a, &b);
            printf("%lld\n", query(a, b, 1, N, 1));
        }
        else
        {
            scanf("%d%d%d", &a, &b, &c);
            update(a, b, c, 1, N, 1);
        }
    }
    return 0;
}
```

### 其他基础题目

- POJ 3667 Hotel：区间更新；
- HDU 1540 Tunnel Warfare：单点更新，查询结点所在区间；
- HDU 2871 Memory Control：与POJ 3667类似；


## 进阶题目

这类题目或是写起来比较复杂，或是思维上有难度。

### BNUOJ 51636 Squared Permutation

一次更新需要更新多个点，不容易刻画更新的操作。

```cpp
#include <bits/stdc++.h>
using namespace std; 
#define lson l, m, i << 1
#define rson m + 1, r, i << 1 | 1
const int maxn = 111111; 
long long a[maxn], b[maxn]; 
struct segTree
{
    long long left, right, value; 
}   t[maxn << 2]; 
void push_up(int i)
{
     t[i].value = t[i << 1].value+t[i << 1 | 1].value; 
}
void build(int l, int r, int i)
{
    t[i].left = l; 
    t[i].right = r; 
    if(l == r)
    {
        t[i].value = a[a[l]]; 
        return ; 
    }
    int m = l + r >> 1; 
    build(lson);  build(rson); 
    push_up(i); 
}
void update(int k, int val, int i)
{
    if(k == t[i].left && k == t[i].right)
    {
        t[i].value=val; 
        return; 
    }
    int m = (t[i].left + t[i].right) >> 1; 
    if(k <= m) update(k, val, i << 1); 
    else if(m + 1 <= k) update(k, val, i << 1 | 1); 
    push_up(i); 
}
long long query(int l, int r, int i)
{
    if(l == t[i].left && r == t[i].right) return t[i].value; 
    int m = (t[i].left+t[i].right) >> 1; 
    if(r <= m)
        return query(l, r, i << 1); 
    if(m + 1 <= l)
        return query(l, r, i << 1 | 1); 

    return query(lson) + query(rson); 
}
int main()
{
    int T; 
    scanf("%d", &T); 
    while(T--)
    {
        int n, q; 
        scanf("%d", &n); 
        for(int i = 1; i <= n; i++)
        {
            scanf("%lld", &a[i]); 
            b[a[i]] = i; 
        }
        build(1, n, 1); 
        scanf("%d", &q); 
        int op, l, r; 
        while(q--)
        {
            scanf("%d%d%d", &op, &l, &r); 
            if(op == 1)
            {
                swap(a[l], a[r]); 
                swap(b[a[l]], b[a[r]]); 
                update(l, a[a[l]], 1); 
                update(b[l], a[l], 1); 
                update(r, a[a[r]], 1); 
                update(b[r], a[r], 1); 
            }
            else printf("%lld\n", query(l, r, 1)); 
        }
    }
    return 0; 
}
```

### Codeforces gym 101116G Ground Defense

区间更新，实现起来比较麻烦。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define root 1, n, 1
const int maxn = 500005;
long long va[maxn << 2], vd[maxn << 2];
long long lazy_a[maxn << 2], lazy_d[maxn << 2];
void build(int l, int r, int rt)
{
    va[rt] = vd[rt] = lazy_a[rt] = lazy_d[rt] = 0;
    if(l == r) return ;
    int m = l + r >> 1;
    build(lson);
    build(rson);
}
void push_down(int rt)
{
    va[rt << 1] += lazy_a[rt];     lazy_a[rt << 1] += lazy_a[rt];
    va[rt << 1 | 1] += lazy_a[rt]; lazy_a[rt << 1 | 1] += lazy_a[rt];
    lazy_a[rt] = 0;

    vd[rt << 1] += lazy_d[rt];     lazy_d[rt << 1] += lazy_d[rt];
    vd[rt << 1 | 1] += lazy_d[rt]; lazy_d[rt << 1 | 1] += lazy_d[rt];
    lazy_d[rt] = 0;
}
void update(long long a, long long d, long long L, long long R, int l, int r, int rt)
{
    if(R == r && L == l)
    {
        va[rt] += a; lazy_a[rt] += a;
        vd[rt] += d; lazy_d[rt] += d;
        return ;
    }
    push_down(rt);
    int m = l + r >> 1;
    if(R <= m) update(a, d, L, R, lson);
    else if(L > m) update(a, d, L, R, rson);
    else
    {
        update(a, d, L, m, lson);
        update(a, d, m + 1, R, rson);
    }
}
long long query(int p, int l, int r, int rt)
{
    if(l == r) return va[rt] + l * vd[rt];
    push_down(rt);
    int m = l + r >> 1;
    if(p <= m) return query(p, lson);
    else return query(p, rson);
}
int main()
{
    int t;
    scanf("%d", &t);
    while(t--)
    {
        int n, m;
        scanf("%d%d", &m, &n);
        build(root);
        while(m--)
        {
            char op[5];
            scanf("%s", op);
            if(op[0] == 'Q')
            {
                int idx;
                scanf("%d", &idx);
                printf("%lld\n", query(idx, root));
            }
            if(op[0] == 'U')
            {
                char dir[5];
                scanf("%s", dir);
                int idx, d, l, r;
                long long s, a;
                scanf("%d%lld%lld%d", &idx, &s, &a, &d);
                if(dir[0] == 'E')
                {
                    l = idx;
                    r = l + d - 1;
                    s -= l * a;
                }
                else
                {
                    r = idx;
                    l = r - d + 1;
                    s += r * a;
                    a = -a;
                }
                update(s, a, l, r, root);
            }
        }
    }
    return 0;
}
```

### HDU 5023 A Corrupt Mayor's Performance Art

区间修改的又一典型例子。

```cpp
#include <bits/stdc++.h>
#ifdef __WINDOWS_
    #include <windows.h>
#endif
using namespace std;
#define showtime printf("time = %.15f\n", clock() / (double)CLOCKS_PER_SEC);
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define root 1, n, 1
const int maxn = 1e6 + 5;
const int maxm = 1005;
const int mod = 1e6 + 3;
const double eps = 1e-8;
const double pi = acos(-1.0);
const int inf = 0x3f3f3f3f;
struct Node
{
    int l, r;
    long long x, lz;
    Node() {}
    Node(int a, int b, long long c, long long d)
    {
        l = a; r = b; x = c; lz = d;
    }
} a[maxn << 2];
long long ans, b[45];
void push_down(int rt)
{
    if(a[rt].l == a[rt].r) return ;
    if(a[rt].lz == 0) return ;
    a[rt << 1].lz = a[rt << 1].x = a[rt].lz;
    a[rt << 1 | 1].lz = a[rt << 1 | 1].x = a[rt].lz;
    a[rt].lz = 0;
}
void build(int l, int r, int rt)
{
    a[rt] = Node(l, r, 2, 0);
    if(l == r) return ;
    int m = (l + r) >> 1;
    build(lson);
    build(rson);
}
void update(int l, int r, int rt, int op)
{
    if(a[rt].l == l && a[rt].r == r)
    {
        a[rt].lz = b[op];
        a[rt].x = b[op];
        return ;
    }
    push_down(rt);
    int m = (a[rt].l + a[rt].r) >> 1;
    if(r <= m) update(l, r, rt << 1, op);
    else if(l > m) update(l, r, rt << 1 | 1, op);
    else
    {
        update(lson, op);
        update(rson, op);
    }
    if(a[rt << 1].x == a[rt << 1 | 1].x)
        a[rt].x = a[rt << 1].x;
    else a[rt].x = -1;
}
void query(int l, int r, int rt)
{
    if(a[rt].x != -1)
    {
        ans |= a[rt].x;
        return ;
    }
    int m = (a[rt].l + a[rt].r) >> 1;
    push_down(rt);
    if(r <= m) query(l, r, rt << 1);
    else if(l > m) query(l, r, rt << 1 | 1);
    else
    {
        query(lson);
        query(rson);
    }
}
void init()
{
    b[1] = 1;
    for(int i = 2; i <= 30; i++)
        b[i] = b[i - 1] * 2;
}
void solve()
{
    init();
    int n, m;
    while(scanf("%d%d", &n, &m) != EOF)
    {
        if(m == 0 && n == 0) break;
        build(root);
        while(m--)
        {
            char op[5];
            scanf("%s", op);
            int l, r;
            scanf("%d%d", &l, &r);
            if(op[0] == 'P')
            {
                int x;
                scanf("%d", &x);
                update(l, r, 1, x);
            }
            if(op[0] == 'Q')
            {
                ans = 0;
                query(l, r, 1);
                int first = 1, x;
                for(int i = 1; i <= 30; i++)
                {
                    if(ans & 1)
                    {
                        if(first == 0) printf(" ");
                        first = 0;
                        printf("%d", i);
                    }
                    ans /= 2;
                }
                printf("\n");
            }
        }
    }
}
int main()
{
    solve();
    return 0;
}
```

### 其他线段树进阶题目

- HDU 3308 LCIS：细节很多，非常容易错；
- POJ 1436 Horizontally Visible Segments：转化为区间染色；
- HDU 4747 Mex： 区间更新，区间求和；
- HDU 4601 Letter Tree：线段树+字典树；
- Codeforces 258E Little Elephant and Tree：DFS+线段树；
- Codeforces 269D Maximum Waterfall：线段树+dp。