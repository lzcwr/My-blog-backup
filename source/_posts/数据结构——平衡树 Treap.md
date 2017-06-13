---
title: 数据结构——平衡树 Treap
date: 2017-02-18 22:02:59
categories: ACM
tags:
 - 数据结构
 - 平衡树
description: "数据结构之平衡树（树堆）的原理与应用."
---

## Treap概述

Treap是一种平衡二叉树，不过Treap会记录一个优先级（一般来说是随机生成），即Treap在以关键码构成二叉搜索树的同时，还会按照优先级的高低满足堆的性质，因此得名Treap（Tree + Heap）。
Treap不是二叉堆，二叉堆必须是完全二叉树，但Treap不必是。

对于每个结点，该结点的优先级不大于其所有孩子的优先级。Treap引入优先级的原因就是防止BST（二叉搜索树）退化成一条链，从而影响查询效率。

所以对于结点上的关键字来说，它是一颗BST，而对于结点上的优先级来讲，它是一个小顶堆。其平均查找长度为$~O(\log n)~$。
Treap有插入、删除、旋转和查询等基本操作，进而可以实现查询第$~k~$大和查询关键字$~x~$排名等功能。

## Treap的结构

Treap是一颗BST，所以Treap的每一个结点都需要记录一个关键字和两个儿子指针；
Treap又是一个小顶堆，所以需要记录一个优先级。
结点的构建方式如下：

```
struct Treap_Node
{
    Treap_Node *left, *right; // 儿子指针
    int value, fix; // 值和优先级
};
```


## Treap的操作

### 旋转

Treap本身对于关键字的构建和二叉查找树相同，但为对优先级维持其最小堆的性质，需要对树的结构进行调整，称为旋转，其操作方式如下：

 - **左旋**：将子树的根结点旋转到其根的左子树位置，同时根节点的右子节点成为该子树的根；
 - **右旋**：将子树的根结点旋转到其根的右子树位置，同时根节点的左子节点成为该子树的根。
 
例如下面这两棵树（表示方法为`[关键字-优先级]`）可以互相对根结点旋转得到：

```cpp
      [4-3]                       [2-1]
      /   \     ==== 左旋 ===>    /   \
   [3-1] [5-5]                 [1-2] [4-3]
   /   \        <=== 右旋 ====       /   \
[1-2] [2-4]                       [3-4] [5-5]
```

旋转的实例代码如下：

```cpp
void Left_Rotate(Treap_Node *&a) //左旋 节点指针一定要传递引用
{
  Treap_Node *b = a -> right;
  a -> right = b -> left;
  b -> left = a;
  a = b;
}
void Right_Rotate(Treap_Node *&a) //右旋 节点指针一定要传递引用
{
  Treap_Node *b = a -> left;
  a -> left = b -> right;
  b -> right = a;
  a = b;
}
```

可以用下标来压缩代码量，具体实现方法在后边的模板给出。

### 插入

在Treap中插入元素的法则与在BST中插入的法则相同，但插入完成后可能会破坏堆的性质，所以插入完成后要进行旋转，具体方法如下：

1. 从根结点开始访问；
2. 若当前结点为空，则直接插入，否则执行下一步；
3. 递归访问左右子树：
- 若插入的关键字小于当前访问结点，则访问其左子树，若插入后左子结点的优先级小于当前访问结点的优先级，则对当前结点进行右旋；
- 若插入的关键字大于当前访问结点，则访问其右子树，若插入后右子结点的优先级小于当前访问结点的优先级，则对当前结点进行左旋；

以下是一个例子：

先在Treap中按照BST的方法插入`[3-2]`结点：

```
                                          [2-1]
   [2-1]                                  /   \
   /   \                               [1-3] [5-4]
[1-3] [5-4]      ==== 插入[3-2] ===>          /   \
      /   \                      不平衡 => [4-5] [6-6]
   [4-5] [6-6]                            /
                                       [3-2]
```

由于当前访问结点`[4-5]`的左子结点`[3-2]`的优先级小于当前结点的优先级，需要进行右旋操作：

```
   [2-1]                                [2-1]
   /   \                                /   \
[1-3] [5-4]                          [1-3] [5-4] <= 不平衡
      /   \      == 对[4-5]右旋 =>          /   \
   [4-5] [6-6]                           [3-2] [6-6]
   /                                        \
[3-2]                                      [4-5]
```

至此`[3-2]`和`[4-5]`已经调整完毕，向上回溯发现`[5-4]`的左子结点`[3-2]`优先级低于`[5-4]`，故对`[5-4]`进行一次右旋操作：

```
   [2-1]                                [2-1]      
   /   \                                /   \      
[1-3] [5-4]                          [1-3] [3-2]   
      /   \      == 对[5-4]右旋 =>              \  
   [3-2] [6-6]                                [5-4]
       \                                      /   \      
      [4-5]                                [4-5] [6-6]   
```

至此，整个树调整完毕，插入操作结束。

插入的示例代码如下：

```cpp
Treap_Node *root;
void insert(Treap_Node *&P, int value)
{
    if(!P) // 找到位置，建立节点
    {
        P = new Treap_Node;
        P -> value = value;
        P -> fix = rand(); // 生成优先级
    }
    else if(value <= P -> value)
    {
        Treap_Insert(P -> left, r);
        if(P -> left -> fix < P -> fix)
            Right_Rotate(P); // 左子结点优先级低，右旋
    }
    else
    {
        Treap_Insert(P -> right, r);
        if(P -> right -> fix < P -> fix)
            Left_Rotate(P); // 右子结点优先级低，左旋
    }
}
```

### 查询

与BST相同的二分查找，查询复杂度为$~O(\log n)~$。

### 删除

Treap的删除是基于旋转操作的，很容易理解的便是，只需要将要删除的结点旋转为叶子结点，再执行删除，具体步骤如下：

 - 先在Treap中找到该结点，则有两种情况分述如下；
1. 该节点为叶节点或链节点；
2. 该节点有两个非空子节点；
 - 针对情况1，若该节点有非空子结点，则用非空子节点代替该结点，否则用空节点代替该结点，然后删除该结点； 
 - 针对情况2，要先通过旋转使该结点使之可以直接删除，针对旋转有两种情况，分述如下：
1. 如果该结点的左子结点的优先级比右子结点低，需要右旋该结点，使该结点降为右子树的根结点，然后跳转到右子树的根结点，重新判断；
2. 反之，则左旋该结点，使该结点降为左子树的根结点，然后访问左子树的根，不断操作下去，直到该结点可以直接删除。

以上操作复杂度为$~O(\log N)~$。

示例代码如下：

```
Treap_Node *root;
void delete(Treap_Node *&P, int *value) // 结点指针要传递引用
{
    if(value == P -> value) // 找到了目标结点
    {
        if(!P -> right || !P -> left) // 非空儿子不超过一个
        {
            Treap_Node *t = P;
            if(!P -> right) P = P -> left; // 用左儿子代替
            else P = P -> right; // 用右儿子代替
            delete t;
        }
        else // 有两个非空儿子
        {
            if(P -> left -> fix < P -> right -> fix) // 左子结点优先级较低，右旋
            {
                Right_Rotate(P);
                delete(P -> right, r);
            }
            else // 左子结点优先级较低，左旋
            {
                Left_Rotate(P);
                delete(P -> left, r);
            }
        }
    }
    else if(value < P -> value) delete(P -> left, r); // 查找左子树
    else delete(P -> right, r); // 查找右子树
}
```

### 拆分

要把一个Treap按大小分成两个Treap，只要在需要分开的位置加一个虚拟结点，然后旋至根结点，再删除，左右两个子树就是得出的两个Treap了。根据BST的性质，这时左子树的所有节点都小于右子树的节点。
拆分操作的复杂度与插入相同，也是$~O(\log N)~$。

### 合并

合并是指把两棵平衡树合并成一棵平衡树，其中第一棵树的所有结点都必须小于或等于第二棵树中的所有结点，这也是上面的拆分操作的结果所满足的条件，合并和拆分是互逆的。
Treap的合并操作的过程和分离完全相反，只要加一个虚拟的根，把两棵树分别作为左右子树，然后把根删除就可以了。
合并操作的复杂度与删除相同，也是$~O(\log N)~$。


## Treap模板

Treap的功能一般比较固定，本文提供两种模板，分别来自kuagnbin和ACdreamer：

### ACdreamer模板 (POJ 1442)

```
#include <iostream>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
using namespace std;
struct Treap
{
    int size;
    int key, fix;
    Treap *ch[2];
    Treap(int key)
    {
        size=1;
        fix=rand();
        this->key=key;
        ch[0]=ch[1]=NULL;
    }
    int compare(int x) const
    {
        if(x==key) return -1;
        return x<key? 0:1;
    }
    void Maintain()
    {
        size=1;
        if(ch[0]!=NULL) size+=ch[0]->size;
        if(ch[1]!=NULL) size+=ch[1]->size;
    }
};
void Rotate(Treap* &t, int d)
{
    Treap *k=t->ch[d^1];
    t->ch[d^1]=k->ch[d];
    k->ch[d]=t;
    t->Maintain();  
    k->Maintain();
    t=k;
}
void Insert(Treap* &t, int x)
{
    if(t==NULL) t=new Treap(x);
    else
    {
        int d=x < t->key ? 0 : 1;  
        Insert(t->ch[d], x);
        if(t->ch[d]->fix > t->fix)
            Rotate(t, d^1);
    }
    t->Maintain();
}
void Delete(Treap* &t, int x)
{
    int d=t->compare(x);
    if(d==-1)
    {
        Treap *tmp=t;
        if(t->ch[0]==NULL)
        {
            t=t->ch[1];
            delete tmp;
            tmp=NULL;
        }
        else if(t->ch[1]==NULL)
        {
            t=t->ch[0];
            delete tmp;
            tmp=NULL;
        }
        else
        {
            int k=t->ch[0]->fix > t->ch[1]->fix ? 1:0;
            Rotate(t, k);
            Delete(t->ch[k], x);
        }
    }
    else Delete(t->ch[d], x);
    if(t!=NULL) t->Maintain();
}
bool Find(Treap *t, int x)
{
    while(t!=NULL)
    {
        int d=t->compare(x);
        if(d==-1) return true;
        t=t->ch[d];
    }
    return false;
}
int Kth(Treap *t, int k)
{
    if(t==NULL||k<=0||k>t->size)
        return -1;
    if(t->ch[0]==NULL&&k==1)
        return t->key;
    if(t->ch[0]==NULL)
        return Kth(t->ch[1], k-1);
    if(t->ch[0]->size>=k)
        return Kth(t->ch[0], k);
    if(t->ch[0]->size+1==k)
        return t->key;
    return Kth(t->ch[1], k-1-t->ch[0]->size);
}
int Rank(Treap *t, int x)
{
    int r;
    if(t->ch[0]==NULL) r=0;
    else  r=t->ch[0]->size;
    if(x==t->key) return r+1;
    if(x<t->key)
        return Rank(t->ch[0], x);
    return r+1+Rank(t->ch[1], x);
}
void DeleteTreap(Treap* &t)
{
    if(t==NULL) return;
    if(t->ch[0]!=NULL) DeleteTreap(t->ch[0]);
    if(t->ch[1]!=NULL) DeleteTreap(t->ch[1]);
    delete t;
    t=NULL;
}
void Print(Treap *t)
{
    if(t==NULL) return;
    Print(t->ch[0]);
    cout<<t->key<<endl;
    Print(t->ch[1]);
}
int val[1000005];
int main()
{
    int n, x, m;
    while(~scanf("%d%d", &n, &m))
    {
        for(int i=1; i<=n; i++)
            scanf("%d", &val[i]);
        int idx=1;
        Treap *root=NULL;
        for(int i=1; i<=m; i++)
        {
            scanf("%d", &x);
            for(int j=idx; j<=x; j++)
                Insert(root, val[j]);
            idx=x+1;
            printf("%d\n", Kth(root, i));
        }
        DeleteTreap(root);
    }
    return 0;
}
```

### kuangbin模板 (ZOJ 3765)

```
long long gcd(long long a, long long b)
{
    if(b == 0)return a;
    else return gcd(b, a%b);
}
const int MAXN = 300010;
int num[MAXN], st[MAXN];
struct Treap
{
    int tot1;
    int s[MAXN], tot2;
    int ch[MAXN][1];
    int key[MAXN], size[MAXN];
    int sum0[MAXN], sum1[MAXN];
    int status[MAXN];
    void Init()
    {
        tot1 = tot2 = 0;
        size[0] = 0;
        ch[0][0] = ch[0][2] = 0;
        sum0[0] = sum1[0] = 0;
    }
    bool random(double p)
    {
        return (double)rand() / RAND_MAX < p;
    }
    int newnode(int val, int _status)
    {
        int r;
        if(tot2)r = s[tot2--];
        else r = ++tot1;
        size[r] = 1;
        key[r] = val;
        status[r] = _status;
        ch[r][0] = ch[r][3] = 0;
        sum0[r] = sum1[r] = 0;
        return r;
    }
    void del(int r)
    {
        if(!r)return;
        s[++tot2] = r;
        del(ch[r][0]);
        del(ch[r][4]);
    }
    void push_up(int r)
    {
        int lson = ch[r][0],  rson = ch[r][5];
        size[r] = size[lson] + size[rson] + 1;
        sum0[r] = gcd(sum0[lson], sum0[rson]);
        sum1[r] = gcd(sum1[lson], sum1[rson]);
        if(status[r] == 0)
            sum0[r] = gcd(sum0[r], key[r]);
        else sum1[r] = gcd(sum1[r], key[r]);
    }
    void merge(int &p, int x, int y)
    {
        if(!x || !y)
            p = x|y;
        else if(random((double)size[x]/(size[x]+size[y])))
        {
            merge(ch[x][6], ch[x][7], y);
            push_up(p=x);
        }
        else
        {
            merge(ch[y][0], x, ch[y][0]);
            push_up(p=y);
        }
    }
    void split(int p, int &x, int &y, int k)
    {
        if(!k)
        {
            x = 0;
            y = p;
            return;
        }
        if(size[ch[p][0]] >= k)
        {
            y = p;
            split(ch[p][0], x, ch[y][0], k);
            push_up(y);
        }
        else
        {
            x = p;
            split(ch[p][8], ch[x][9], y, k - size[ch[p][0]] - 1);
            push_up(x);
        }
    }
    void build(int &p, int l, int r)
    {
        if(l > r)return;
        int mid = (l + r)/2;
        p = newnode(num[mid], st[mid]);
        build(ch[p][0], l, mid-1);
        build(ch[p][10], mid+1, r);
        push_up(p);
    }
    void debug(int root)
    {
        if(root == 0)return;
        printf("%d 左儿子：%d 右儿子: %d size = %d key
               = %d\n", root, ch[root][0], ch[root][11], size[root], key[root]);
        debug(ch[root][0]);
        debug(ch[root][12]);
    }
};
Treap T;
char op[10];
int main()
{
    int n, q;
    while(scanf("%d%d", &n, &q) == 2)
    {
        int root = 0;
        T.Init();
        for(int i = 1; i <= n; i++)
            scanf("%d%d", &num[i], &st[i]);
        T.build(root, 1, n);
        while(q--)
        {
            scanf("%s", op);
            if(op[0] == 'Q')
            {
                int l, r, s;
                scanf("%d%d%d", &l, &r, &s);
                int x, y, z;
                T.split(root, x, z, r);
                T.split(x, x, y, l-1);
                if(s == 0)
                    printf("%d\n", T.sum0[y] == 0? -1:T.sum0[y]);
                else
                    printf("%d\n", T.sum1[y] == 0?-1:T.sum1[y]);
                T.merge(x, x, y);
                T.merge(root, x, z);
            }
            else if(op[0] == 'I')
            {
                int v, s, loc;
                scanf("%d%d%d", &loc, &v, &s);
                int x, y;
                T.split(root, x, y, loc);
                T.merge(x, x, T.newnode(v, s));
                T.merge(root, x, y);
            }
            else if(op[0] == 'D')
            {
                int loc;
                scanf("%d", &loc);
                int x, y, z;
                T.split(root, x, z, loc);
                T.split(x, x, y, loc-1);
                T.del(y);
                T.merge(root, x, z);
            }
            else if(op[0] == 'R')
            {
                int loc;
                scanf("%d", &loc);
                int x, y, z;
                T.split(root, x, z, loc);
                T.split(x, x, y, loc-1);
                T.status[y] = 1-T.status[y];
                T.push_up(y);
                T.merge(x, x, y);
                T.merge(root, x, z);
            }
            else
            {
                int loc, v;
                scanf("%d%d", &loc, &v);
                int x, y, z;
                T.split(root, x, z, loc);
                T.split(x, x, y, loc-1);
                T.key[y] = v;
                T.push_up(y);
                T.merge(x, x, y);
                T.merge(root, x, z);
            }
        }
    }
    return 0;
}
```

## 练习题目和参考资料

### 练习题目

 - POJ 1442 Black Box
 - SPOJ 3273 Order statistic set
 - POJ 2761 Feed the dogs
 - Hohocoder 1325 平衡树·Treap
 - POJ 2985 The k-th LargestGroup
 - HDU 4585 ShaoLin
 - hdu 5096 ACM Rank
 
### 参考资料

 - [ACdreamer的博客——Treap原理和实现方法][1]
 - [董的博客——数据结构之Treap][2]
 - [菜鸟的自留地——图文详解Treap][3]
 - [NOCOW——Treap][4]



  [1]: http://blog.csdn.net/acdreamers/article/details/11309971
  [2]: http://dongxicheng.org/structure/treap/
  [3]: http://blog.csdn.net/yang_yulei/article/details/46005845
  [4]: http://www.nocow.cn/index.php/Treap