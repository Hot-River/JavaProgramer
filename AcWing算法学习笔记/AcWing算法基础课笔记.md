# 一、基础算法

## 1、常用代码模板

### 快速排序算法模板

—— 模板题 AcWing 785. 快速排序

算法步骤：

​	①确定分界点

​	②调整区间

​	③递归处理左右两段

Tips：

​	①如果输入数据量比较大的话，用scanf输入更快，不要使用cin。

​	②只要背诵一种万能模板即可避开所有边界问题。

​	③考试时一般不会用到快排，面试时喜欢考察。

​	④ \#include<bits/stdc++.h>包含了目前c++所包含的所有头文件 。

​	⑤分治一般用不到，只要掌握快速排序和归并排序就行。

```c++
void quick_sort(int q[], int l, int r)
{
    if (l >= r) return;

    int i = l - 1, j = r + 1, x = q[l + r >> 1];
    while (i < j)
    {
        do i ++ ; while (q[i] < x);
        do j -- ; while (q[j] > x);
        if (i < j) swap(q[i], q[j]);
    }
    quick_sort(q, l, j), quick_sort(q, j + 1, r);
}
```

### 归并排序算法模板

—— 模板题 AcWing 787. 归并排序

算法步骤：

​	①确定分界点

​	②递归排序

​	③归并——合二为一

```c++
void merge_sort(int q[], int l, int r)
{
    if (l >= r) return;

    int mid = l + r >> 1;
    merge_sort(q, l, mid);
    merge_sort(q, mid + 1, r);
	
    // 归并的过程
    int k = 0, i = l, j = mid + 1;
    while (i <= mid && j <= r)
        if (q[i] <= q[j]) tmp[k ++ ] = q[i ++ ];
        else tmp[k ++ ] = q[j ++ ];

    while (i <= mid) tmp[k ++ ] = q[i ++ ];
    while (j <= r) tmp[k ++ ] = q[j ++ ];
        
    for (i = l, j = 0; i <= r; i ++, j ++ ) q[i] = tmp[j];
}
```

### 整数二分算法模板

—— 模板题 AcWing 789. 数的范围

Tips：

​	①二分区间一点包含答案。

​	②二分的时候一定是有解的，如果无解就是和题目有关，二分的模板一定保证有解。

```c++
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
// 求左边界点
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
// 求右边界点
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

### 浮点数二分算法模板

—— 模板题 AcWing 790. 数的三次方根

```c++
bool check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求
    while (r - l > eps)
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
```

### 高精度加法

—— 模板题 AcWing 791. 高精度加法

Tips：

​	①高精度只对C++有要求

​	②在笔试时偶尔会出现

```c++
// C = A + B, A >= 0, B >= 0
// 用引用传参会快很多
vector<int> add(vector<int> &A, vector<int> &B)
{
    if (A.size() < B.size()) return add(B, A);

    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ )
    {
        t += A[i];
        if (i < B.size()) t += B[i];
        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}
```

### 高精度减法

—— 模板题 AcWing 792. 高精度减法

```c++
// C = A - B, 满足A >= B, A >= 0, B >= 0
vector<int> sub(vector<int> &A, vector<int> &B)
{
    vector<int> C;
    for (int i = 0, t = 0; i < A.size(); i ++ )
    {
        t = A[i] - t;
        if (i < B.size()) t -= B[i];
        C.push_back((t + 10) % 10);
        if (t < 0) t = 1;
        else t = 0;
    }
	
    // 去掉前导0
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
```

### 高精度乘低精度

—— 模板题 AcWing 793. 高精度乘法

```c++
// C = A * b, A >= 0, b >= 0
vector<int> mul(vector<int> &A, int b)
{
    vector<int> C;

    int t = 0; // 进位
    for (int i = 0; i < A.size() || t; i ++ )
    {
        if (i < A.size()) t += A[i] * b;
        C.push_back(t % 10);
        t /= 10;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}
```

### 高精度除以低精度

—— 模板题 AcWing 794. 高精度除法

```c++
// A / b = C ... r, A >= 0, b > 0
vector<int> div(vector<int> &A, int b, int &r)
{
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i -- )
    {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }r= 
    reverse(C.begin(), C.end());
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
```

### 一维前缀和

—— 模板题 AcWing 795. 前缀和

前缀和的作用：快速计算某一段数的和

tips：

​	①ios::sync_with_stdio(false); 提高cin的读取速度、不能再使用scanf了，但依旧没有scanf快。数据输入>=100w，建议使用scanf，否则建议使用cin。

​	②只要定义了i - 1，则下标一般从1开始。

```c++
S[i] = a[1] + a[2] + ... a[i]
a[l] + ... + a[r] = S[r] - S[l - 1]
```

### 二维前缀和

—— 模板题 AcWing 796. 子矩阵的和

```c++
S[i, j] = 第i行j列格子左上部分所有元素的和
以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵的和为：
S[x2, y2] - S[x1 - 1, y2] - S[x2, y1 - 1] + S[x1 - 1, y1 - 1]
```

### 一维差分

—— 模板题 AcWing 797. 差分 

```c++
给区间[l, r]中的每个数加上c：B[l] += c, B[r + 1] -= c
```

### 二维差分

—— 模板题 AcWing 798. 差分矩阵

```c++
给以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵中的所有元素加上c：
S[x1, y1] += c, S[x2 + 1, y1] -= c, S[x1, y2 + 1] -= c, S[x2 + 1, y2 + 1] += c
```

### 位运算

—— 模板题 AcWing 801. 二进制中1的个数

tips：

​	①把第k位移到最后一位 n>>k

​	②看个位是几  x&1

​	③lowbit(x)：x的二进制表达式中最低位的1所对应的值

​	④x&-x = x&(~x+1)      ~x：x取反	简单应用：统计x中有多少个1

​	⑤x = 1010 	原码：1010	反码：0101	补码：0110

```c++
求n的第k位数字: n >> k & 1
返回n的最后一位1：lowbit(n) = n & -n
```

### 双指针算法

—— 模板题 AcWIng 799. 最长连续不重复子序列

—— 模板题 AcWing 800. 数组元素的目标和

tips：

​	①算法复杂度都是O(n）

​	②先想一个朴素算法，观察单调关系，再想双指针算法

```c++
for (int i = 0, j = 0; i < n; i ++ )
{
    while (j < i && check(i, j)) j ++ ;

    // 具体问题的逻辑
}
常见问题分类：
    (1) 对于一个序列，用两个指针维护一段区间
    (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作
```

### 整数离散化

—— 模板题 AcWing 802. 区间和

数据离散化处理

定义：
离散化，把无限空间中有限的个体映射到有限的空间中去，以此提高算法的时空效率。

通俗的说，离散化是在不改变数据相对大小的条件下，对数据进行相应的缩小。例如：

原数据：1,999,100000,15；处理后：1,3,4,2；

原数据：{100,200}，{20,50000}，{1,400}；

处理后：{3,4}，{2,6}，{1,5}；

例如有时需要将数据直接作为数组下标，但因为数据太大而无法开数组时，若数据的总量相对较少时，就可以对其进行离散化处理

tips：

​	①pair将一对值(T1和T2)组合成一个值，这一对值可以具有不同的数据类型（T1和T2），两个值可以分别用pair的两个公有函数first和second访问。

​	②pair排序默认先按左端点排序，后按右端点排序。

​	③c++用vector做离散化	

```c++
vector<int> alls; // 存储所有待离散化的值
sort(alls.begin(), alls.end()); // 将所有值排序
alls.erase(unique(alls.begin(), alls.end()), alls.end());   // 去掉重复元素

// 二分求出x对应的离散化的值
int find(int x) // 找到第一个大于等于x的位置
{
    int l = 0, r = alls.size() - 1;
    while (l < r)
    {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1; // 映射到1, 2, ...n
}
```

### 区间合并

—— 模板题 AcWing 803. 区间合并

```c++
// 将所有存在交集的区间合并
void merge(vector<PII> &segs)
{
    vector<PII> res;

    sort(segs.begin(), segs.end());

    int st = -2e9, ed = -2e9;
    for (auto seg : segs)
        if (ed < seg.first)
        {
            if (st != -2e9) res.push_back({st, ed});
            st = seg.first, ed = seg.second;
        }
        else ed = max(ed, seg.second);

    if (st != -2e9) res.push_back({st, ed});

    segs = res;
}
```

上课：明白主要思想

课下：代码整个流程弄懂、大致的背过、题目默写相关模板、写出来并调试通过

如何提高熟练度：重复写代码（删、写）、一个题目写3-5遍



## 2、课堂笔记

### 快速排序——分治

​	1、确定分界点： q[ll],q[(l+r)/2],q[r],随机	(l,r分别为左右边界)

​	2、调整区间（重难点）：

​	3、递归处理左右两段：

![1619270326504](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619270326504.png)

实现方法：

​	1、暴力做法：

​	![1619270437986](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619270437986.png)

​	2、优美做法：

![1619270621312](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619270621312.png)

 		i为x左边第一个大于x的数，j为x右边第一个小于x的数，交换i和j，直到（i>j）。再递归处理子区间。



TIPS：

1、如果输入数据量比较大的话，用scanf输入，不要用cin。

2、只要背诵一种万能模板即可避开所有边界问题。





### 归并排序——分治

![1619271817482](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619271817482.png)





#二分法

![1619273535699](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619273535699.png)

![1619273564138](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619273564138.png)

​	整数二分：

​		二分的本质并不是单调性

![1619273952099](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619273952099.png)

l=mid 补上 +1	r=mid不用补上+1

![1619274218482](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619274218482.png)

二分的时候一定是有解的，如果无解就是和题目有关，二分的模板一定保证有解



### 高精度

​	大整数的一般范围

![](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619334924031.png)

​	①大整数用数组存储

​		数组从个位开始保存：方便进位

​	②按位计算



### 前缀和差分是一对逆运算



前缀和（小技巧）：

​	①如何求Si：for(i = 1;i <= n;i++) S[i] = S[i-1] + ai

​	②前缀和的作用：快速计算某一段数的和

tips：

​	ios::sync_with_stdio(false); 提高cin的读取速度、不能再使用scanf了，但依旧没有scanf快。

数据输入>=100w，建议使用scanf，否则建议使用cin。



​	差分：

![1619501735237](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619501735237.png)

​	作用：只要知道b数组，则在O（n）时间复杂度内求出a数组

​	二维：构造b矩阵使得a矩阵是b矩阵的前缀和



分治一般用不到，只要掌握快速排序和归并排序就行。

只要定义了i - 1，则下标一般从1开始。



 \#include<bits/stdc++.h>包含了目前c++所包含的所有头文件 



### 双指针算法

常规写法：

![1619746070986](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619746070986.png)

算法复杂度都是O（n）

![1619746208396](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619746208396.png)

先想一个朴素算法，，观察单调关系，再想双指针算法。



### 位运算

​	①、先把第k位移到最后一位 n>>k

​	②、看个位是几  x&1

​	lowbit(x)：x的二进制表达式中最低位的1所对应的值。  x&-x = x&(~x+1)      ~x：x取反	简单应用：统计x中有多少个1



x = 1010 	原码：1010	反码：0101	补码：0110

善于用图分析问题



**pair将一对值(T1和T2)组合成一个值**，

这一对值可以具有不同的数据类型（T1和T2），

两个值可以分别**用pair的两个公有函数first和second访问**。

pair排序默认先按左端点排序，后按右端点排序。



### 数据离散化处理

定义：
离散化，把无限空间中有限的个体映射到有限的空间中去，以此提高算法的时空效率。

通俗的说，离散化是在不改变数据相对大小的条件下，对数据进行相应的缩小。例如：

原数据：1,999,100000,15；处理后：1,3,4,2；

原数据：{100,200}，{20,50000}，{1,400}；

处理后：{3,4}，{2,6}，{1,5}；

例如有时需要将数据直接作为数组下标，但因为数据太大而无法开数组时，若数据的总量相对较少时，就可以对其进行离散化处理



### 区间合并

pair排序默认先按左端点排序，后按右端点排序。

# 二、数据结构

## 1、常用代码模板

### 单链表

 —— 模板题 AcWing 826. 单链表

Tips:

​	①scanf每次%c之前都要加上空格,防止读取空格

```c++
struct Node
{
	int val;
	Node *next;
};//在面试题中用的多，在笔试中用一般不会用，因为new Node()非常慢，用数组模拟链表很快
```

```c++
// head存储链表头，e[]存储节点的值，ne[]存储节点的next指针，idx表示当前用到了哪个节点
int head, e[N], ne[N], idx;

// 初始化
void init()
{
    head = -1;
    idx = 0;
}

// 在链表头插入一个数a，最常用的插入操作
void insert(int a)
{
    e[idx] = a, ne[idx] = head, head = idx ++ ;
}

// 将头结点删除，需要保证头结点存在
void remove()
{
    head = ne[head];
}
```

### 双链表

 —— 模板题 AcWing 827. 双链表

```c++
// e[]表示节点的值，l[]表示节点的左指针，r[]表示节点的右指针，idx表示当前用到了哪个节点
int e[N], l[N], r[N], idx;

// 初始化
void init()
{
    //0是左端点，1是右端点
    r[0] = 1, l[1] = 0;
    idx = 2;
}

// 在节点a的右边插入一个数x
void insert(int a, int x)
{
    e[idx] = x;
    l[idx] = a, r[idx] = r[a];
    l[r[a]] = idx, r[a] = idx ++ ;
}

// 删除节点a
void remove(int a)
{
    l[r[a]] = l[a];
    r[l[a]] = r[a];
}
```

### 栈

 —— 模板题 AcWing 828. 模拟栈

 **——模板题 AcWing 3302. 表达式求值**

```c++
// tt表示栈顶
int stk[N], tt = 0;

// 向栈顶插入一个数
stk[ ++ tt] = x;

// 从栈顶弹出一个数
tt -- ;

// 栈顶的值
stk[tt];

// 判断栈是否为空
if (tt > 0)
{

}
```

### 队列

 —— 模板题 AcWing 829. 模拟队列

```c++
1、普通队列：
// hh 表示队头，tt表示队尾
int q[N], hh = 0, tt = -1;

// 向队尾插入一个数
q[ ++ tt] = x;

// 从队头弹出一个数
hh ++ ;

// 队头的值
q[hh];

// 判断队列是否为空
if (hh <= tt)
{

}
```

```c++
2、循坏队列
// hh 表示队头，tt表示队尾的后一个位置
int q[N], hh = 0, tt = 0;

// 向队尾插入一个数
q[tt ++ ] = x;
if (tt == N) tt = 0;

// 从队头弹出一个数
hh ++ ;
if (hh == N) hh = 0;

// 队头的值
q[hh];

// 判断队列是否为空
if (hh != tt)
{

}
```

### 单调栈

 —— 模板题 AcWing 830. 单调栈

```c++
常见模型：找出每个数左边离它最近的比它大/小的数
int tt = 0;
for (int i = 1; i <= n; i ++ )
{
    while (tt && check(stk[tt], i)) tt -- ;
    stk[ ++ tt] = i;
}
```

### 单调队列

 —— 模板题 AcWing 154. 滑动窗口

​	1、用普通队列该怎么做

​	2、将队列中的没有用的元素删掉——>具有了单调性

​	3、可以用O(1)时间从队头/队尾取出最值

Tips:

​	①适用场景非常有限，但面试笔试喜欢考察

```c++
常见模型：找出滑动窗口中的最大值/最小值
int hh = 0, tt = -1;
for (int i = 0; i < n; i ++ )
{
    while (hh <= tt && check_out(q[hh])) hh ++ ;  // 判断队头是否滑出窗口
    while (hh <= tt && check(q[tt], i)) tt -- ;
    q[ ++ tt] = i;
}
```

### ~KMP~

 —— 模板题 AcWing 831. KMP字符串

[KMP算法详解]: https://blog.csdn.net/yyzsir/article/details/89462339?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161994024716780255274044%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&amp;request_id=161994024716780255274044&amp;biz_id=0&amp;utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-3-89462339.first_rank_v2_pc_rank_v29&amp;utm_term=KMP%E7%AE%97%E6%B3%95
[KMP算法配图详解]: https://blog.csdn.net/weixin_46007276/article/details/104372119?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161994024716780255274044%2522%252C%2522s

​	ne数组作用有两个：一是之前提到的，ne[i]的值表示下标为i的字符前的字符串最长相等前后缀的长度；

​									 二是表示该处字符不匹配时应该回溯到的字符的下标。

```c++
// s[]是长文本，p[]是模式串，n是s的长度，m是p的长度
求模式串的Next数组：
for (int i = 2, j = 0; i <= m; i ++ )
{
    while (j && p[i] != p[j + 1]) j = ne[j];
    if (p[i] == p[j + 1]) j ++ ;
    ne[i] = j;
}

// 匹配
for (int i = 1, j = 0; i <= n; i ++ )
{
    while (j && s[i] != p[j + 1]) j = ne[j]; //当主串S与模式串P失配时，j=next[j]，P向右移动j - next[j]
    if (s[i] == p[j + 1]) j ++ ;
    if (j == m)
    {
        j = ne[j];
        // 匹配成功后的逻辑
    }
}
```

### Trie字典树

 —— 模板题 AcWing 835. Trie字符串统计

```c++
int son[N][26], cnt[N], idx;
// 0号点既是根节点，又是空节点
// son[][]存储树中每个节点的子节点
// cnt[]存储以每个节点结尾的单词数量
// idx存储当前用到的节点下标

// 插入一个字符串
void insert(char *str)
{
    int p = 0;
    for (int i = 0; str[i]; i ++ )
    {
        int u = str[i] - 'a';
        if (!son[p][u]) son[p][u] = ++ idx;
        p = son[p][u];
    }
    cnt[p] ++ ;
}

// 查询字符串出现的次数
int query(char *str)
{
    int p = 0;
    for (int i = 0; str[i]; i ++ )
    {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;
        p = son[p][u];
    }
    return cnt[p];
}
```

### 并查集

 —— 模板题 AcWing 836. 合并集合

 —— 模板题 AcWing 837. 连通块中点的数量

Tips：

​	分类问题用并查集做

```c++
(1)朴素并查集：
int p[N]; //存储每个点的祖宗节点

// 返回x的祖宗节点 + 路径压缩
int find(int x)
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i ++ ) p[i] = i;

// 合并a和b所在的两个集合：
p[find(a)] = find(b);
```

```c++
(2)维护size的并查集：
int p[N], size[N];
//p[]存储每个点的祖宗节点, size[]只有祖宗节点的有意义，表示祖宗节点所在集合中的点的数量

// 返回x的祖宗节点
int find(int x)
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i ++ )
{
    p[i] = i;
    size[i] = 1;
}

// 合并a和b所在的两个集合：
size[find(b)] += size[find(a)];
p[find(a)] = find(b);
```

```c++
(3)维护到祖宗节点距离的并查集：
int p[N], d[N];
//p[]存储每个点的祖宗节点, d[x]存储x到p[x]的距离

// 返回x的祖宗节点
int find(int x)
{
    if (p[x] != x)
    {
        int u = find(p[x]);
        d[x] += d[p[x]];
        p[x] = u;
    }
    return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i ++ )
{
    p[i] = i;
    d[i] = 0;
}

// 合并a和b所在的两个集合：
p[find(a)] = find(b);
d[find(a)] = distance; // 根据具体问题，初始化find(a)的偏移量
```
### 堆

 —— 模板题 AcWing 838. 堆排序

 —— 模板题 AcWing 839. 模拟堆

```c++
// h[N]存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x + 1
// ph[k]存储第k个插入的点在堆中的位置
// hp[k]存储堆中下标是k的点是第几个插入的
int h[N], ph[N], hp[N], size;

// 交换两个点，及其映射关系
void heap_swap(int a, int b)
{
    swap(ph[hp[a]],ph[hp[b]]);
    swap(hp[a], hp[b]);
    swap(h[a], h[b]);
}

void down(int u)
{
    int t = u;
    if (u * 2 <= size && h[u * 2] < h[t]) t = u * 2;
    if (u * 2 + 1 <= size && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
    if (u != t)
    {
        heap_swap(u, t);
        down(t);
    }
}

void up(int u)
{
    while (u / 2 && h[u] < h[u / 2])
    {
        heap_swap(u, u / 2);
        u >>= 1;
    }
}

// O(n)建堆
for (int i = n / 2; i; i -- ) down(i);
```

### 一般哈希

 —— 模板题 AcWing 840. 模拟散列表

```c++
(1) 拉链法
int h[N], e[N], ne[N], idx;

// 向哈希表中插入一个数
void insert(int x)
{
    int k = (x % N + N) % N;
    e[idx] = x;
    ne[idx] = h[k];
    h[k] = idx ++ ;
}

// 在哈希表中查询某个数是否存在
bool find(int x)
{
    int k = (x % N + N) % N;
    for (int i = h[k]; i != -1; i = ne[i])
        if (e[i] == x)
            return true;

    return false;
}
```

```c++
(2) 开放寻址法
int h[N];

// 如果x在哈希表中，返回x的下标；如果x不在哈希表中，返回x应该插入的位置
int find(int x)
{
    int t = (x % N + N) % N;
    while (h[t] != null && h[t] != x)
    {
        t ++ ;
        if (t == N) t = 0;
    }
    return t;
}
```
### 字符串哈希

 —— 模板题 AcWing 841. 字符串哈希

```c++
核心思想：将字符串看成P进制数，P的经验值是131或13331，取这两个值的冲突概率低
小技巧：取模的数用2^64，这样直接用unsigned long long存储，溢出的结果就是取模的结果

typedef unsigned long long ULL;
ULL h[N], p[N]; // h[k]存储字符串前k个字母的哈希值, p[k]存储 P^k mod 2^64

// 初始化
p[0] = 1;
for (int i = 1; i <= n; i ++ )
{
    h[i] = h[i - 1] * P + str[i];
    p[i] = p[i - 1] * P;
}

// 计算子串 str[l ~ r] 的哈希值
ULL get(int l, int r)
{
    return h[r] - h[l - 1] * p[r - l + 1];
}
```

### C++ STL简介

```c++
vector, 变长数组，倍增的思想
    size()  返回元素个数
    empty()  返回是否为空
    clear()  清空
    front()/back()
    push_back()/pop_back()
    begin()/end()
    []
    支持比较运算，按字典序

pair<int, int>
    first, 第一个元素
    second, 第二个元素
    支持比较运算，以first为第一关键字，以second为第二关键字（字典序）

string，字符串
    size()/length()  返回字符串长度
    empty()
    clear()
    substr(起始下标，(子串长度))  返回子串
    c_str()  返回字符串所在字符数组的起始地址
    支持 a += “def“	a += ‘c’

queue, 队列
    size()
    empty()
    push()  向队尾插入一个元素
    front()  返回队头元素
    back()  返回队尾元素
    pop()  弹出队头元素

priority_queue, 优先队列，默认是大根堆
    size()
    empty()
    push()  插入一个元素
    top()  返回堆顶元素
    pop()  弹出堆顶元素
    定义成小根堆的方式：priority_queue<int, vector<int>, greater<int>> q;

stack, 栈
    size()
    empty()
    push()  向栈顶插入一个元素
    top()  返回栈顶元素
    pop()  弹出栈顶元素

deque, 双端队列
    用的不多，速度慢，效率不高
    size()
    empty()
    clear()
    front()/back()
    push_back()/pop_back()
    push_front()/pop_front()
    begin()/end()
    []

set, map, multiset, multimap, 基于平衡二叉树（红黑树），动态维护有序序列
    size()
    empty()
    clear()
    begin()/end()
    ++, -- 返回前驱和后继，时间复杂度 O(logn)

set/multiset
    insert()  插入一个数
    find()  查找一个数
    count()  返回某一个数的个数
    erase()
        (1) 输入是一个数x，删除所有x   O(k + logn)
        (2) 输入一个迭代器，删除这个迭代器
    lower_bound()/upper_bound()
        lower_bound(x)  返回大于等于x的最小的数的迭代器
        upper_bound(x)  返回大于x的最小的数的迭代器
map/multimap
    insert()  插入的数是一个pair
    erase()  输入的参数是pair或者迭代器
    find()
    []  注意multimap不支持此操作。 时间复杂度是 O(logn)
    lower_bound()/upper_bound()

unordered_set, unordered_map, unordered_multiset, unordered_multimap, 哈希表
    和上面类似，增删改查的时间复杂度是 O(1)
    不支持 lower_bound()/upper_bound()， 迭代器的++，--

bitset, 圧位
    bitset<10000> s;
    ~, &, |, ^
    >>, <<
    ==, !=
    []

count()  返回有多少个1

any()  判断是否至少有一个1
none()  判断是否全为0

set()  把所有位置成1
set(k, v)  将第k位变成v
reset()  把所有位变成0
flip()  等价于~
flip(k) 把第k位取反
```

## 2、课堂笔记

### 链表

```c++
struct Node
{
	int val;
	Node *next;
};//在面试题中用的多，在笔试中用一般不会用，因为new Node()非常慢
```

用数组模拟链表很快

scanf每次%c之前都要加上空格,防止读取空格

单链表：

应用：邻接表：常用于存储树和图

双链表：

应用：优化某些问题



### 栈

特点：先进后出



### 队列

特点：先进先出

单调队列：

适用场景非常有限，但面试笔试喜欢考察

1、用普通队列该怎么做

2、将队列中的没有用的元素删掉——>具有了单调性

3、可以用O(1)时间从队头/队尾取出最值



### KMP

kmp习惯上下标从一开始



### Trie树

应用：高效地存储和查找字符串集合的数据结构  

![1620007584840](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620007584840.png)



### 并查集

面试喜欢考察

近乎O(1)完成下面操作

1、将两个集合合并

2、询问两个元素是否在一个集合当中

基本原理：每个集合用一颗树来表示，树根的编号就是整个集合的编号，每个节点存储它的父节点，p[x]表示x的父节点。

如何判断树根？if(p[x] == x)

如何求x的集合编号？ while(p[x] != x) x = p[x];	 优化：路径压缩

如何合并两个集合？px是x的集合编号，py是y的集合编号，p[x] = y。

Tips：读单个字符最好定义成字符串来读，防止读入空格等莫名其妙的字符。



### 堆

堆是一个完全二叉树

小根堆：每个根节点都小于左右孩子



如何手写一个堆？（下标从一开始）

1、插入一个数	heap[ ++ size] = x; up(size);

2、求集合当中的最小值	heap[1];

3、删除最小值	heap[1] = heap[size]; size -- ; down(1);

4、删除任意一个元素（STL中的堆无法直接实现） heap[k] = heap[size]; size -- ; down(k); up(k) ;

5、修改任意一个元素（STL中的堆无法直接实现）heap[k] = x ; down[k]; up[k];



存储：用一维数组存储

![1620022932904](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620022932904.png)

关键操作：down()、up()，其他操作可由关键操作组合完成



### 哈希表

存储结构：开放寻址法、拉链法



在算法题中一般只有添加和查找操作

取模的数尽量定义成质数 100003

用开放寻址法数组长度要开到数据长度的2-3倍

memset按字节赋值，memset不能处理vector

0x3f3f3f3f的十进制是1061109567，也就是10^9级别的（和0x7fffffff一个数量级），而一般场合下的数据都是小于10^9的，所以它可以作为无穷大使用而不致出现数据大于无穷大的情形。

0x3f3f3f3f还能给我们带来一个意想不到的额外好处：如果我们想要将某个数组清零，我们通常会使用memset(a,0,sizeof(a))这样的代码来实现（方便而高效），但是当我们想将某个数组全部赋值为无穷大时（例如解决图论问题时邻接矩阵的初始化），就不能使用memset函数而得自己写循环了（写这些不重要的代码真的很痛苦），我们知道这是因为memset是按字节操作的，它能够对数组清零是因为0的每个字节都是0，现在好了，如果我们将无穷大设为0x3f3f3f3f，那么奇迹就发生了，0x3f3f3f3f的每个字节都是0x3f！所以要把一段内存全部置为无穷大，我们只需要
 memset(a,0x3f,sizeof(a))。 



字符串哈希方式——字符串前缀哈希法：解决困难的字符串问题，比如快速判断两个字符串是否相等

1、先预处理出所有前缀的哈希（特殊处理h(0) = 0，h存储字符串下标从1开始）

![1620133422025](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620133422025.png)

2、用已求得的前缀哈希值，通过一个公式求出所有子段的哈希值

![1620133321144](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620133321144.png)

如何定义某个前缀的哈希值？把字符串（ASIC码）看成P进制的数，每一位的字母表示P进制上每一位的数字 （最关键）

h[]表示每个前缀的哈希值 	P表示P进制

p = 131 或 13331	Q = 2^64（unsigned long long，溢出等价于mod Q)	则99.99%情况下不会出现冲突

![1620131521099](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620131521099.png)



### STL

现用现查

**vetor**

变长数组，倍增的思想

size（）返回元素个数

empty（）返回是否为空

clear（）清空 

front（）/back（）

push_back（）/pop_back（）

begin（）/end（）

系统为某一程序分配空间时，所需时间与空间大小，与申请次数有关

支持比较运算，按字典序



**pair<T, T>**

first是第一个元素

second是第二个元素

支持比较运算，以first为第一关键字，以second为第二关键字，按字典序 

初始化方式： p = make_pair（10，“yxc”）；	 p = {20， “abc”}；



**string**

字符串

size()/length（）返回字符串长度

empty()

clear()

substr（1, 2）返回某一个子串 	1表示字串开始的位置，2表示字串的长度

c_str（）返回string对应字符数组的头指针，即起始地址

支持 a += “def“	a += ‘c’



**queue**

队列

size（）

empty（）

push（）向队尾插入一个元素

front（）返回队头元素

pop（）弹出队头元素

back（）返回队尾元素



**priority_queue**

优先权队列，默认是大根堆

push（）插入一个元素

pop（）弹出堆顶元素

top（）返回堆顶元素

定义小根堆：priority_queue<int, vector<int>, greater<int>> heap;



**stack**

栈

size（）

empty（）

push（）向栈顶插入一个元素

pop（）弹出栈顶元素

top（）返回栈顶元素



**deque**

双端队列，用的不多，速度慢，效率不高

size（）

empty（）

clear（）

front（）/back（）返回第/最后一个元素

push_back（）/pop_back（）

push_front（）/pop_front（）

begin（）/end（）





**set, map, mutiset, multimap**

基于平衡二叉树（红黑树）实现，动态维护有序序列

size（）

empty（）

clear（）

begin（）/end（）	++， --  返回前驱和后继，时间复杂度O（logn），end()函数返回一个[迭代器](iterators.html)指向map的尾部。

set/multiset 

​	insert（）插入一个数

​	find（）查找一个数	find()函数返回一个[迭代器](iterators.html)指向键值为key的元素，如果没找到就返回指向map尾部的[迭代器](iterators.html)。

​	count（）返回某一个数的个数

​	erase（）

​		（1）输入一个数x，删除所有x	O（k+ logn）

​		（2）输入一个迭代器，删除这个迭代器

​	lower_bound（）/upper_bound（）

​			lower_bound（x）	返回大于等于x的最小的数的迭代器

​			upper_bound（）	 返回大于x 的最小的数的迭代器

map/multimap

​	insert（）	插入的数是一个pair

​	erase（）	输入的参数是pair或者迭代器

​	find（）	

​	使用数组一样使用map	map<string, int> a;	a["yxc"] = 1;	时间复杂度是O（logn）

​	lower_bound（）/upper_bound（）



**unordered_set, unordered_map, unordered_multiset, unordered_multimap**

哈希表

和上面类似，但增删改查的时间复杂度是O（1)

不支持lower_bound（）/upper_bound（）、迭代器的++ -- 



**bitset**

压位

bitset<10000> S 定义一个长度为10000的bitset

~， &， |，^

左移、右移

==， ！=

[]

count（） 返回有多少个1

any（） 判断是否至少有一个1

none（）判断是否全为0

set（） 把所有位置成1

set（k， v） 将第k位变成v

reset（） 把所有位变成0

flip（）等价于~

flip（k） 把第k位取反

 

# 三、搜索与图论

## 1、常用代码模板

### 树与图的存储

树是一种特殊的图，与图的存储方式相同。
对于无向图中的边ab，存储两条有向边a->b, b->a。
因此我们可以只考虑有向图的存储。

(1) 邻接矩阵：g[a][b] 存储边a->b

(2) 邻接表：

```c++
// 对于每个点k，开一个单链表，存储k所有可以走到的点。h[k]存储这个单链表的头结点
int h[N], e[N], ne[N], idx;

// 添加一条边a->b
void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ; 
}

// 初始化
idx = 0;
memset(h, -1, sizeof h);
```

### 树与图的遍历

**时间复杂度 O(n+m), n 表示点数，m 表示边数**

(1) 深度优先遍历 —— 模板题 AcWing 846. 树的重心

```c++
int dfs(int u)
{
    st[u] = true; // st[u] 表示点u已经被遍历过

    for (int i = h[u]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (!st[j]) dfs(j);
    }
}
```

(2) 宽度优先遍历 —— 模板题 AcWing 847. 图中点的层次

```c++
queue<int> q;
st[1] = true; // 表示1号点已经被遍历过
q.push(1);

while (q.size())
{
    int t = q.front();
    q.pop();

    for (int i = h[t]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (!st[j])
        {
            st[j] = true; // 表示点j已经被遍历过
            q.push(j);
        }
    }
}
```

### 拓扑排序

 —— 模板题 AcWing 848. 有向图的拓扑序列

**时间复杂度 O(n+m), n 表示点数，m 表示边数**

```c++
bool topsort()
{
    int hh = 0, tt = -1;

    // d[i] 存储点i的入度
    for (int i = 1; i <= n; i ++ )
        if (!d[i])
            q[ ++ tt] = i;

    while (hh <= tt)
    {
        int t = q[hh ++ ];

         for (int i = h[t]; i != -1; i = ne[i])
         {
             int j = e[i];
             if (-- d[j] == 0)
                 q[ ++ tt] = j;
         }
    }

    // 如果所有点都入队了，说明存在拓扑序列；否则不存在拓扑序列。
    return tt == n - 1;
}
```

### 朴素dijkstra算法

 —— 模板题 AcWing 849. Dijkstra求最短路 I

**时间复杂度是**![1619577733650](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619577733650.png), **nn 表示点数，mm 表示边数**

```c++
int g[N][N];  /	/ 存储每条边
int dist[N];  // 存储1号点到每个点的最短距离
bool st[N];   // 存储每个点的最短路是否已经确定

// 求1号点到n号点的最短路，如果不存在则返回-1
int dijkstra()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    for (int i = 0; i < n - 1; i ++ )
    {
        int t = -1;     // 在还未确定最短路的点中，寻找距离最小的点
        for (int j = 1; j <= n; j ++ )
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;

         // 用t更新其他点的距离
         for (int j = 1; j <= n; j ++ )
             dist[j] = min(dist[j], dist[t] + g[t][j]);

         st[t] = true;
    }

    if (dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}
```

### 堆优化版dijkstra算法

 —— 模板题 AcWing 850. Dijkstra求最短路 II

**时间复杂度 O(mlogn), n 表示点数，m 表示边数**

```c++
typedef pair<int, int> PII;

int n;      // 点的数量
int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N];        // 存储所有点到1号点的距离
bool st[N];     // 存储每个点的最短距离是否已确定

// 求1号点到n号点的最短距离，如果不存在，则返回-1
int dijkstra()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;
    priority_queue<PII, vector<PII>, greater<PII>> heap;
    heap.push({0, 1});      // first存储距离，second存储节点编号

    while (heap.size())
    {
        auto t = heap.top();
        heap.pop();

         int ver = t.second, distance = t.first;

         if (st[ver]) continue;
         st[ver] = true;

         for (int i = h[ver]; i != -1; i = ne[i])
         {
             int j = e[i];
             if (dist[j] > distance + w[i])
             {
                 dist[j] = distance + w[i];
                 heap.push({dist[j], j});
             }
         } 	
    }

    if (dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}
```

### Bellman-Ford算法

 —— 模板题 AcWing 853. 有边数限制的最短路

**时间复杂度 O(nm), n 表示点数，m 表示边数**

注意在模板题中需要对下面的模板稍作修改，加上备份数组，详情见模板题。

```c++
int n, m;       // n表示点数，m表示边数
int dist[N];        // dist[x]存储1到x的最短路距离

struct Edge     // 边，a表示出点，b表示入点，w表示边的权重
{
    int a, b, w;
}edges[M];

// 求1到n的最短路距离，如果无法从1走到n，则返回-1。
int bellman_ford()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    // 如果第n次迭代仍然会松弛三角不等式，就说明存在一条长度是n+1的最短路径，由抽屉原理，路径中至少存在两个相同的点，说明图中存在负权回路。
    // 假设 1 号点到 n 号点是可达的，每一个点同时向指向的方向出发，更新相邻的点的最短距离，通过循环 n-1 次操作，若图中不存在负环，则 1 号点一定会到达 n 号点，若图中存在负环，则在 n-1 次松弛后一定还会更新
    for (int i = 0; i < n; i ++ )
    {
        for (int j = 0; j < m; j ++ )
        {
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            if (dist[b] > dist[a] + w)
                dist[b] = dist[a] + w;
        }
    }

    if (dist[n] > 0x3f3f3f3f / 2) return -1;
    return dist[n];
}
```

### spfa 算法（队列优化的Bellman-Ford算法）

 —— 模板题 AcWing 851. spfa求最短路

**时间复杂度 平均情况下 O(m)，最坏情况下 O(nm), n 表示点数，m 表示边数**

```c++
int n;      // 总点数
int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N];        // 存储每个点到1号点的最短距离
bool st[N];     // 存储每个点是否在队列中

// 求1号点到n号点的最短路距离，如果从1号点无法走到n号点则返回-1
int spfa()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    queue<int> q;
    q.push(1);
    st[1] = true;

    while (q.size())
    {
        auto t = q.front();
        q.pop();

         st[t] = false;

         for (int i = h[t]; i != -1; i = ne[i])
         {
             int j = e[i];
             if (dist[j] > dist[t] + w[i])
             {
                 dist[j] = dist[t] + w[i];
                 if (!st[j])     // 如果队列中已存在j，则不需要将j重复插入
                 {
                     q.push(j);
                     st[j] = true;
                 }
             }
        }
    }

    if (dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}
```

### spfa判断图中是否存在负环

 —— 模板题 AcWing 852. spfa判断负环

**时间复杂度是 O(nm）, n 表示点数，m 表示边数**

```c++
int n;      // 总点数
int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N], cnt[N];        // dist[x]存储1号点到x的最短距离，cnt[x]存储1到x的最短路中经过的点数
bool st[N];     // 存储每个点是否在队列中

// 如果存在负环，则返回true，否则返回false。
bool spfa()
{
    // 不需要初始化dist数组
    // 原理：如果某条最短路径上有n个点（除了自己），那么加上自己之后一共有n+1个点，由抽屉原理一定有两个点相同，所以存在环。

    queue<int> q;
    for (int i = 1; i <= n; i ++ )
    {
        q.push(i);
        st[i] = true;
    }

    while (q.size())
    {
        auto t = q.front();
        q.pop();

         st[t] = false;

         for (int i = h[t]; i != -1; i = ne[i])
         {
             int j = e[i];
             if (dist[j] > dist[t] + w[i])
             {
                 dist[j] = dist[t] + w[i];
                 cnt[j] = cnt[t] + 1;
                 if (cnt[j] >= n) return true;       // 如果从1号点到x的最短路中包含至少n个点（不包括自己），则说明存在环
                 if (!st[j])
                 {
                     q.push(j);
                     st[j] = true;
                 }
             }
         }
    }

    return false;
}
```

### floyd算法

 —— 模板题 AcWing 854. Floyd求最短路

**时间复杂度是 ![1619579361558](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619579361558.png), n 表示点数**

```c++
初始化：
    for (int i = 1; i <= n; i ++ )
        for (int j = 1; j <= n; j ++ )
            if (i == j) d[i][j] = 0;
            else d[i][j] = INF;

// 算法结束后，d[a][b]表示a到b的最短距离
void floyd()
{
    for (int k = 1; k <= n; k ++ )
        for (int i = 1; i <= n; i ++ )
            for (int j = 1; j <= n; j ++ )
                d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
}
```

### 朴素版prim算法

 —— 模板题 AcWing 858. Prim算法求最小生成树

**时间复杂度是 ![1619579440126](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1619579440126.png), n 表示点数，m 表示边数**

```c++
int n;      // n表示点数
int g[N][N];        // 邻接矩阵，存储所有边
int dist[N];        // 存储其他点到当前最小生成树的距离
bool st[N];     // 存储每个点是否已经在生成树中

// 如果图不连通，则返回INF(值是0x3f3f3f3f), 否则返回最小生成树的树边权重之和
int prim()
{
    memset(dist, 0x3f, sizeof dist);

    int res = 0;
    for (int i = 0; i < n; i ++ )
    {
        int t = -1;
        for (int j = 1; j <= n; j ++ )
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;

         if (i && dist[t] == INF) return INF;
        
         if (i) res += dist[t];
         st[t] = true;

         for (int j = 1; j <= n; j ++ ) dist[j] = min(dist[j], g[t][j]);
    }

    return res;
}
```

### Kruskal算法

 —— 模板题 AcWing 859. Kruskal算法求最小生成树

**时间复杂度是 O(mlogm), n 表示点数，m 表示边数**

```c++
int n, m;       // n是点数，m是边数
int p[N];       // 并查集的父节点数组

struct Edge     // 存储边
{
    int a, b, w;

bool operator< (const Edge &W)const
{
    return w < W.w;
}

}edges[M];

int find(int x)     // 并查集核心操作
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

int kruskal()
{
    sort(edges, edges + m);

    for (int i = 1; i <= n; i ++ ) p[i] = i;    // 初始化并查集

    int res = 0, cnt = 0;
    for (int i = 0; i < m; i ++ )
    {
        int a = edges[i].a, b = edges[i].b, w = edges[i].w;

         a = find(a), b = find(b);
         if (a != b)     // 如果两个连通块不连通，则将这两个连通块合并
         {
             p[a] = b;
             res += w;
             cnt ++ ;
         }
    }

    if (cnt < n - 1) return INF;
    return res;
}
```

### 染色法判别二分图

 —— 模板题 AcWing 860. 染色法判定二分图

**时间复杂度是 O(n+m), n 表示点数，m 表示边数**

```c++
int n;      // n表示点数
int h[N], e[M], ne[M], idx;     // 邻接表存储图
int color[N];       // 表示每个点的颜色，-1表示未染色，0表示白色，1表示黑色

// 参数：u表示当前节点，c表示当前点的颜色
bool dfs(int u, int c)
{
    color[u] = c;
    for (int i = h[u]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (color[j] == -1)
        {
            if (!dfs(j, !c)) return false;
        }
        else if (color[j] == c) return false;
    }

	return true;
}

bool check()
{
    memset(color, -1, sizeof color);
    bool flag = true;
    for (int i = 1; i <= n; i ++ )
        if (color[i] == -1)
            if (!dfs(i, 0))
            {
                flag = false;
                break;
            }
    return flag;
}
```

### 匈牙利算法

 —— 模板题 AcWing 861. 二分图的最大匹配

**时间复杂度是 O(nm) ,n 表示点数，m 表示边数**

```c++
int n1, n2;     // n1表示第一个集合中的点数，n2表示第二个集合中的点数
int h[N], e[M], ne[M], idx;     // 邻接表存储所有边，匈牙利算法中只会用到从第一个集合指向第二个集合的边，所以这里只用存一个方向的边
int match[N];       // 存储第二个集合中的每个点当前匹配的第一个集合中的点是哪个
bool st[N];     // 表示第二个集合中的每个点是否已经被遍历过

bool find(int x)
{
    for (int i = h[x]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (!st[j])
        {
            st[j] = true;
            if (match[j] == 0 || find(match[j]))
            {
                match[j] = x;
                return true;
            }
        }
    }

	return false;
}

// 求最大匹配数，依次枚举第一个集合中的每个点能否匹配第二个集合中的点
int res = 0;
for (int i = 1; i <= n1; i ++ )
{
    memset(st, false, sizeof st);
    if (find(i)) res ++ ;
}
```



## 2、课堂笔记

图论侧重于实现，难点在于建图

### DFS与BFS

深度优先搜索 与 宽度优先搜索

![1620473765817](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620473765817.png)

凡是算法思路比较奇怪、对空间要求比较高的用DFS来做

凡是求最短的最小的都用BFS来做，前提是边的权重都是1

DFS重要是的思路

DFS回溯时要注意恢复状态

BFS用队列实现

BFS在权重为1时，每个首次被访问的点离出发点的距离都是最短的

### 树和图的存储

树是无环连通 图

有向图：

​	存储：

​		邻接矩阵：用的比较少，比较费空间，适合稠密图

​		邻接表：每个结点指向一个链表，表示从该节点能到达的结点

### 树与图的深度优先遍历

写代码时，最好以一个点为例去写

### 树与图的宽度优先遍历

队列模拟

### 拓扑序列

针对有向图，有环一定不存在拓扑序列

有向无环图（DAG拓扑图）一定存在拓扑序列

图的宽度优先遍历

入度为0的点即可作为起点

![1620482644203](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620482644203.png)

答案可能不唯一

### 最短路

根据题目要求选择算法，即题目给的数据范围

难点在于建图，将问题抽象成图（点和边）

![1620729861502](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620729861502.png)

#### 单源最短路

Dijkstra算法要求一定不存在负边

1、所有边权都是正数（Dijkstra算法基于贪心）	

**朴素Dijkstra算法**：时间复杂度O(n^2)。适用于稠密图，图用邻接矩阵存储，有向图和无向图做法是一样的			

①初始化距离

②循环迭代

s:当前已确定最短距离的点

​		找到不在s中的距离最近的点

​		用其来更新其他点的距离

​		将其加入s中		

每次循环都能确定一个点的最短距离	

![1620730715240](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620730715240.png)

**堆优化版Dijksra算法**：时间复杂度O(mlogn)，适用于稀疏图

堆用优先权队列来实现

2、存在负权边	

99.9%的题目不存在负环

**Bellman-Fold算法**：时间复杂度O(mn)	（基于离散数学）

**适用条件&范围：**

​		单源最短路径(从源点s到其它所有顶点v);

​		有向图&无向图(无向图可以看作(u,v),(v,u)同属于边集E的有向图);

​		边权可正可负(如有负权回路输出错误提示);

​		差分约束系统;

​		有边数限制的最短路问题

**Bellman-Ford算法的流程如下：**

​		给定图G(V, E)（其中V、E分别为图G的顶点集与边集），源点s，数组Distant[i]记录从源点s到顶点i的路径长度，初始化数组Distant[n]为, Distant[s]为0；

​		以下操作循环执行至多n-1次，n为顶点数：
​		对于每一条边e(u, v)，如果Distant[u] + w(u, v) < Distant[v]，则令Distant[v] = Distant[u]+w(u, v)。w(u, v)为边e(u,v)的权值；
​		若上述操作没有对Distant进行更新，说明最短路径已经查找完毕，或者部分点不可达，跳出循环。否则执行下次循环；

​		为了检测图中是否存在负环路，即权值之和小于0的环路。对于每一条边e(u, v)，如果存在Distant[u] + w(u, v) < Distant[v]的边，则图中存在负环路，即是说改图无法求出单源最短路径。否则数组Distant[n]中记录的就是源点s到各顶点的最短路径长度。

​	可知，Bellman-Ford算法寻找单源最短路径的时间复杂度为O(V*E).

**Bellman－Ford算法可以大致分为三个部分**
		第一，初始化所有点。每一个点保存一个值，表示从原点到达这个点的距离，将原点的值设为0，其它的点的值设为无穷大（表示不可达）。
		第二，进行循环，循环下标为从1到n－1（n等于图中点的个数）。在循环内部，遍历所有的边，进行松弛计算。
		第三，遍历途中所有的边（edge（u，v）），判断是否存在这样情况：d（v） > d (u) + w(u,v)，则返回false，表示途中存在从源点可达的权为负的回路。 

有负权回路，则最短路不一定存在，如果负环不在最短路径中，则没有影响

迭代k次表示经过不超过k条边的最短路的距离

如果迭代n次依然有更新，则说明存在一条最短路径有n条边，即有n+1条边，根据抽屉原理，一定存在负环。

![1620737908952](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620737908952.png)

**SPFA算法**：一般时间复杂度O(m)、最坏时间复杂度O(mn)

要求图中一定不含负环，但可以用来求负环	

对bellman-ford算法的优化，一般情况下都优于Bellman-Ford算法

spfa也能解决权值为正的图的最短距离问题，且一般情况下比Dijkstra算法还好

Bellman_ford算法会遍历所有的边，但是有很多的边遍历了其实没有什么意义，我们只用遍历那些到源点距离变小的点所连接的边即可，只有当一个点的前驱结点更新了，该节点才会得到更新；因此考虑到这一点，我们将创建一个队列每一次加入距离被更新的结点。

**spfa算法步骤**
	queue <– 1
	while queue 不为空
		 (1) t <– 队头
			 queue.pop()
		 (2)用 t 更新所有出边 t –> b，权值为w
			 queue <– b (若该点被更新过，则拿该点更新其他点)

![1620739959904](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620739959904.png)

SPFA算法判负环

![1620740838294](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620740838294.png)

#### 多源汇最短路	

**Floyd算法**：时间复杂度O(n^3)	（基于动态规划）

算法思想csdn了解一下

用邻接矩阵存储图

![1620741774072](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620741774072.png)

### 最小生成树

对应的图都是无向图

![1620959159539](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620959159539.png)

#### Prim算法

朴素版Prim算法 ：稠密图 O(n^2)		![1620959514131](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620959514131.png)

堆优化版Prim算法：稀疏图  O(mlogn) 一般不常用

#### Kruskal算法

时间复杂度：O(mlogm）稀疏图

![1620961302018](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620961302018.png)

### 二分图

![1620959172851](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620959172851.png)

#### 染色法

时间复杂度O(n + m)

判断一个图是不是二分图

一个图是二分图当且仅当图中不含奇数环（环中边的条数是奇数）

一般基于深度优先遍历实现

![1620991557823](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620991557823.png) 

#### 匈牙利算法

时间复杂度：最坏O(mn)，实际运行时间一般远小于O(mn)

匹配：在图论中，一个「匹配」是一个边的集合，其中任意两条边都没有公共顶点。

最大匹配：一个图所有匹配中，所含匹配边数最多的匹配，称为这个图的最大匹配。

![1620992870821](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1620992870821.png)



# 四、数学知识

## 1、常用代码模板

### 试除法判定质数

 —— 模板题 AcWing 866. 试除法判定质数

```c++
bool is_prime(int x)
{
    if (x < 2) return false;
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
            return false;
    return true;
}
```

### 试除法分解质因数

 —— 模板题 AcWing 867. 分解质因数

```c++
void divide(int x)
{
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
        {
            int s = 0;
            while (x % i == 0) x /= i, s ++ ;
            cout << i << ' ' << s << endl;
        }
    if (x > 1) cout << x << ' ' << 1 << endl;
    cout << endl;
}
```

### 朴素筛法求素数

 —— 模板题 AcWing 868. 筛质数

```c++
int primes[N], cnt;     // primes[]存储所有素数
bool st[N];         // st[x]存储x是否被筛掉

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        if (st[i]) continue;
        primes[cnt ++ ] = i;
        for (int j = i + i; j <= n; j += i)
            st[j] = true;
    }
}
```

### 线性筛法求素数

 —— 模板题 AcWing 868. 筛质数

```c++
int primes[N], cnt;     // primes[]存储所有素数
bool st[N];         // st[x]存储x是否被筛掉

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}
```

### 试除法求所有约数

 —— 模板题 AcWing 869. 试除法求约数

```c++
vector<int> get_divisors(int x)
{
    vector<int> res;
    for (int i = 1; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res.push_back(i);
            if (i != x / i) res.push_back(x / i);
        }
    sort(res.begin(), res.end());
    return res;
}
```

### 约数个数和约数之和

 —— 模板题 AcWing 870. 约数个数, AcWing 871. 约数之和

```c++
如果 N = p1^c1 * p2^c2 * ... *pk^ck
约数个数： (c1 + 1) * (c2 + 1) * ... * (ck + 1)
约数之和： (p1^0 + p1^1 + ... + p1^c1) * ... * (pk^0 + pk^1 + ... + pk^ck)
```

### 欧几里得算法

 —— 模板题 AcWing 872. 最大公约数

```c++
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
}
```

### 求欧拉函数

 —— 模板题 AcWing 873. 欧拉函数

```c++
int phi(int x)
{
    int res = x;
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res = res / i * (i - 1);
            while (x % i == 0) x /= i;
        }
    if (x > 1) res = res / x * (x - 1);

	return res;
}
```

### 筛法求欧拉函数

 —— 模板题 AcWing 874. 筛法求欧拉函数

```c++
int primes[N], cnt;     // primes[]存储所有素数
int euler[N];           // 存储每个数的欧拉函数
bool st[N];         // st[x]存储x是否被筛掉

void get_eulers(int n)
{
    euler[1] = 1;
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i])
        {
            primes[cnt ++ ] = i;
            euler[i] = i - 1;
        }
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            int t = primes[j] * i;
            st[t] = true;
            if (i % primes[j] == 0)
            {
                euler[t] = euler[i] * primes[j];
                break;
            }
            euler[t] = euler[i] * (primes[j] - 1);
        }
    }
}
```

### 快速幂

 —— 模板题 AcWing 875. 快速幂

```c++
求 m^k mod p，时间复杂度 O(logk)。

int qmi(int m, int k, int p)
{
    int res = 1 % p, t = m;
    while (k)
    {
        if (k&1) res = res * t % p;
        t = t * t % p;
        k >>= 1;
    }
    return res;
}
```

### 扩展欧几里得算法

 —— 模板题 AcWing 877. 扩展欧几里得算法

```c++
// 求x, y，使得ax + by = gcd(a, b)
int exgcd(int a, int b, int &x, int &y)
{
    if (!b)
    {
        x = 1; y = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y -= (a/b) * x;
    return d;
}
```

### 中国剩余定理

```c++
// 根据中国剩余定理，如果m1,m2,…,mn不是两两互质的，那么就不存在这样的x
// 如果满足上述条件，则x = a1·M1·M1^(-1) + a2·M2·M2^(-1) + …… + an·Mn·Mn^(-1),
// 其中Mi = M / mi，M = m1·m2·…·mn，Mi^(-1)表示Mi模mi的逆
```



### 高斯消元

 —— 模板题 AcWing 883. 高斯消元解线性方程组

```c++
// a[N][N]是增广矩阵
int gauss()
{
    int c, r;
    for (c = 0, r = 0; c < n; c ++ )
    {
        int t = r;
        for (int i = r; i < n; i ++ )   // 找到绝对值最大的行
            if (fabs(a[i][c]) > fabs(a[t][c]))
                t = i;

         if (fabs(a[t][c]) < eps) continue;

         for (int i = c; i <= n; i ++ ) swap(a[t][i], a[r][i]);      // 将绝对值最大的行换到最顶端
         for (int i = n; i >= c; i -- ) a[r][i] /= a[r][c];      // 将当前行的首位变成1
         for (int i = r + 1; i < n; i ++ )       // 用当前行将下面所有的列消成0
             if (fabs(a[i][c]) > eps)
                 for (int j = n; j >= c; j -- )
                     a[i][j] -= a[r][j] * a[i][c];

         r ++ ;
    }

    if (r < n)
    {
        for (int i = r; i < n; i ++ )
            if (fabs(a[i][n]) > eps)
                return 2; // 无解
        return 1; // 有无穷多组解
    }

    for (int i = n - 1; i >= 0; i -- )
        for (int j = i + 1; j < n; j ++ )
            a[i][n] -= a[i][j] * a[j][n];

    return 0; // 有唯一解
}
```

### 递归法求组合数

 —— 模板题 AcWing 885. 求组合数 I

```c++
// c[a][b] 表示从a个苹果中选b个的方案数
for (int i = 0; i < N; i ++ )
    for (int j = 0; j <= i; j ++ )
        if (!j) c[i][j] = 1;
        else c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod;
```

### 通过预处理逆元的方式求组合数

 —— 模板题 AcWing 886. 求组合数 II

```c++
首先预处理出所有阶乘取模的余数fact[N]，以及所有阶乘取模的逆元infact[N]
如果取模的数是质数，可以用费马小定理求逆元
int qmi(int a, int k, int p)    // 快速幂模板
{
    int res = 1;
    while (k)
    {
        if (k & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        k >>= 1;
    }
    return res;
}

// 预处理阶乘的余数和阶乘逆元的余数
fact[0] = infact[0] = 1;
for (int i = 1; i < N; i ++ )
{
    fact[i] = (LL)fact[i - 1] * i % mod;
    infact[i] = (LL)infact[i - 1] * qmi(i, mod - 2, mod) % mod;
}
```

### Lucas定理

 —— 模板题 AcWing 887. 求组合数 III

```c++
若p是质数，则对于任意整数 1 <= m <= n，有：
    C(n, m) = C(n % p, m % p) * C(n / p, m / p) (mod p)

int qmi(int a, int k, int p)  // 快速幂模板
{
    int res = 1 % p;
    while (k)
    {
        if (k & 1) res = (LL)res * a % p;
        a = (LL)a * a % p;
        k >>= 1;
    }
    return res;
}

int C(int a, int b, int p)  // 通过定理求组合数C(a, b)
{
    if (a < b) return 0;

    LL x = 1, y = 1;  // x是分子，y是分母
    for (int i = a, j = 1; j <= b; i -- , j ++ )
    {
        x = (LL)x * i % p;
        y = (LL)y * j % p;
    }

    return x * (LL)qmi(y, p - 2, p) % p;

}

int lucas(LL a, LL b, int p)
{
    if (a < p && b < p) return C(a, b, p);
    return (LL)C(a % p, b % p, p) * lucas(a / p, b / p, p) % p;
}
```

### 分解质因数法求组合数

 —— 模板题 AcWing 888. 求组合数 IV

```c++
当我们需要求出组合数的真实值，而非对某个数的余数时，分解质因数的方式比较好用：

       1. 筛法求出范围内的所有质数
       2. 通过 C(a, b) = a! / b! / (a - b)! 这个公式求出每个质因子的次数。 n! 中p的次数是 n / p + n / p^2 + n / p^3 + ...
       3. 用高精度乘法将所有质因子相乘

int primes[N], cnt;     // 存储所有质数
int sum[N];     // 存储每个质数的次数
bool st[N];     // 存储每个数是否已被筛掉


void get_primes(int n)      // 线性筛法求素数
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}


int get(int n, int p)       // 求n！中的次数
{
    int res = 0;
    while (n)
    {
        res += n / p;
        n /= p;
    }
    return res;
}


vector<int> mul(vector<int> a, int b)       // 高精度乘低精度模板
{
    vector<int> c;
    int t = 0;
    for (int i = 0; i < a.size(); i ++ )
    {
        t += a[i] * b;
        c.push_back(t % 10);
        t /= 10;
    }

    while (t)
    {
        c.push_back(t % 10);
        t /= 10;
    }

    return c;

}

get_primes(a);  // 预处理范围内的所有质数

for (int i = 0; i < cnt; i ++ )     // 求每个质因数的次数
{
    int p = primes[i];
    sum[i] = get(a, p) - get(b, p) - get(a - b, p);
}

vector<int> res;
res.push_back(1);

for (int i = 0; i < cnt; i ++ )     // 用高精度乘法将所有质因子相乘
    for (int j = 0; j < sum[i]; j ++ )
        res = mul(res, primes[i]);	
```

### 卡特兰数

 —— 模板题 AcWing 889. 满足条件的01序列

```c++
给定n个0和n个1，它们按照某种顺序排成长度为2n的序列，满足任意前缀中0的个数都不少于1的个数的序列的数量为： Cat(n) = C(2n, n) / (n + 1)
```

### NIM游戏

 —— 模板题 AcWing 891. Nim游戏 

给定N堆物品，第i堆物品有Ai个。两名玩家轮流行动，每次可以任选一堆，取走任意多个物品，可把一堆取光，但不能不取。取走最后一件物品者获胜。两人都采取最优策略，问先手是否必胜。

我们把这种游戏称为NIM博弈。把游戏过程中面临的状态称为局面。整局游戏第一个行动的称为先手，第二个行动的称为后手。若在某一局面下无论采取何种行动，都会输掉游戏，则称该局面必败。
所谓采取最优策略是指，若在某一局面下存在某种行动，使得行动后对面面临必败局面，则优先采取该行动。同时，这样的局面被称为必胜。我们讨论的博弈问题一般都只考虑理想情况，即两人均无失误，都采取最优策略行动时游戏的结果。
NIM博弈不存在平局，只有先手必胜和先手必败两种情况。

**定理**： NIM博弈先手必胜，当且仅当 A1 ^ A2 ^ … ^ An != 0

### 公平组合游戏ICG

若一个游戏满足：

由两名玩家交替行动；
在游戏进程的任意时刻，可以执行的合法行动与轮到哪名玩家无关；
不能行动的玩家判负；
则称该游戏为一个公平组合游戏。
NIM博弈属于公平组合游戏，但城建的棋类游戏，比如围棋，就不是公平组合游戏。因为围棋交战双方分别只能落黑子和白子，胜负判定也比较复杂，不满足条件2和条件3。

### 有向图游戏

给定一个有向无环图，图中有一个唯一的起点，在起点上放有一枚棋子。两名玩家交替地把这枚棋子沿有向边进行移动，每次可以移动一步，无法移动者判负。该游戏被称为有向图游戏。
任何一个公平组合游戏都可以转化为有向图游戏。具体方法是，把每个局面看成图中的一个节点，并且从每个局面向沿着合法行动能够到达的下一个局面连有向边。

### Mex运算

设S表示一个非负整数集合。定义mex(S)为求出不属于集合S的最小非负整数的运算，即：
mex(S) = min{x}, x属于自然数，且x不属于S

### SG函数

在有向图游戏中，对于每个节点x，设从x出发共有k条有向边，分别到达节点y1, y2, …, yk，定义SG(x)为x的后继节点y1, y2, …, yk 的SG函数值构成的集合再执行mex(S)运算的结果，即：
SG(x) = mex({SG(y1), SG(y2), …, SG(yk)})
特别地，整个有向图游戏G的SG函数值被定义为有向图游戏起点s的SG函数值，即SG(G) = SG(s)。

### 有向图游戏的和 

—— 模板题 AcWing 893. 集合-Nim游戏

设G1, G2, …, Gm 是m个有向图游戏。定义有向图游戏G，它的行动规则是任选某个有向图游戏Gi，并在Gi上行动一步。G被称为有向图游戏G1, G2, …, Gm的和。
有向图游戏的和的SG函数值等于它包含的各个子游戏SG函数值的异或和，即：
SG(G) = SG(G1) ^ SG(G2) ^ … ^ SG(Gm)

### 定理

有向图游戏的某个局面必胜，当且仅当该局面对应节点的SG函数值大于0。
有向图游戏的某个局面必败，当且仅当该局面对应节点的SG函数值等于0。

## 2、课堂笔记

### 数论

#### 质数

在大于1的整数中，如果只包含1和本身这两个约数，就被称为质数，或者叫素数。

#### **质数的判定——试除法**

时间复杂度：O（sqrt(n)）

![1621217556715](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621217556715.png)

**分解质因数——试除法**

时间复杂度：O（sqrt(n)）

n中最多只包含一个大于sqrt(n)的质因子

![1621218303607](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621218303607.png)

**埃氏筛法**

时间复杂度：O（nloglogn）

把每个数的倍数删掉，剩下来的一定是质数

当一个数不是质数的时候，就不需要删掉它的倍数

![1621218526382](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621218526382.png)

质数定理：

![1621218804609](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621218804609.png)

![1621218996521](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621218996521.png)

**线性筛法**

n只会被最小质因子筛掉

![1621219575166](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621219575166.png)

![1621219123615](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621219123615.png)

#### 约数

**试除法求一个数的所有约数**

时间复杂度：O（sqrt(n)）

![1621220228083](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621220228083.png)

**约束个数**

在int范围内，约数最多1536个

![1621220485772](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621220485772.png)

![1621220940673](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621220940673.png)

![1621220497079](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621220497079.png)

![1621221893397](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621221893397.png)

**约束之和**

![1621221100765](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621221100765.png)

![1621221110544](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621221110544.png)

![1621222027541](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621222027541.png)

![1621222058061](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621222058061.png)

**欧几里得算法（辗转相除法）**

“|”表示整除

![1621222215658](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621222215658.png)

![1621222425804](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621222425804.png)

![1621222263797](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621222263797.png)

#### 欧拉函数

时间复杂度：O（sqr(n)）

![1621257845033](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621257845033.png)

![1621256862280](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621256862280.png)

![1621256972511](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621256972511.png)

![1621257887220](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621257887220.png)

![1621257786660](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621257786660.png)

![1621258112769](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621258112769.png)

#### 筛法求欧拉函数

i为质因子时：

![1621259211926](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621259211926.png)

![1621258943022](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621258943022.png)

![1621259070214](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621259070214.png)

线性筛法中顺便求欧拉函数

![1621259157733](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621259157733.png)

**欧拉定理：**

![1621259353312](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621259353312.png)

p为质数

![1621259835323](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621259835323.png)



#### 快速幂

用的比较多

快速求出	a^k mod p	O(logk)

预处理出以下结果：

![1621260126802](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621260126802.png)

![1621260380977](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621260380977.png)

![1621260721822](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621260721822.png)

![1621261012810](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621261012810.png)

乘法逆元：

两个整数相除不一定是整数，但两个整数相乘一定是整数。

b^(-1)不是指b的负一次方，是指b的逆元。

![1621261501190](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621261501190.png)

根据费马定理：

![1621261798485](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621261798485.png)

![1621262157733](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621262157733.png)



#### 扩展欧几里得算法

（a，b）表示a，b的最大公约数 

![1621262509229](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621262509229.png)

x，y不唯一

![1621263322087](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621263322087.png)

![1621263073111](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621263073111.png)

![1621263136392](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621263136392.png)

解线性同余方程

![1621263758838](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621263758838.png)

![1621263859394](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621263859394.png)



#### 中国剩余定理

// 根据中国剩余定理，如果m1,m2,…,mn不是两两互质的，那么就不存在这样的x
// 如果满足上述条件，则x = a1·M1·M1^(-1) + a2·M2·M2^(-1) + …… + an·Mn·Mn^(-1),
// 其中Mi = M / mi，M = m1·m2·…·mn，Mi^(-1)表示Mi模mi的逆

![1621264356549](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621264356549.png)



### 组合计数

选用哪种方式要根据给的数据范围来确定

![1621338635135](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621338635135.png)

①递推

![1621337414071](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621337414071.png)

![1621337731437](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621337731437.png)

②预处理

infeat存储逆元

![1621338157857](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621338157857.png)

③Lucas卢卡斯定理

![1621338587310](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621338587310.png)

④卡特兰数

每个排列对应一条路径

![1621343499851](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621343499851.png)

![1621343835141](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621343835141.png)

### 高斯消元

时间复杂度：O(n^3)

![1621334163236](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621334163236.png)

解的情况：无解、无穷解、唯一解

![1621334675574](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621334675574.png)

![1621335619195](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621335619195.png)



### 容斥原理

![1621432359346](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621432359346.png)

![1621432731385](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621432731385.png)

### 简单博弈论

#### Nim游戏

先手必胜状态：可以走到某一个必败状态

先手必败状态：走不到任何一个必败状态

![1621436120206](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621436120206.png)

![1621436586525](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621436586525.png)

#### SG函数

解决博弈论问题的一把利器

![1621437515976](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621437515976.png)

![1621437819950](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621437819950.png)

![1621438215143](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621438215143.png)

# 五、动态规划

## 1、课堂笔记

**DP时间复杂度 = 状态数量 * 转移计算量**

**一般做法： 分析状态表示（集合、属性），写出状态计算（转移方程）**

### 01背包问题

——模板题 AcWing 2. 01背包问题

每件物品最多只用一次

**结合题目理解**

![1621575885953](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621575885953.png)

**状态表示 f(i, j)：**

​		集合：所有选法的集合 ，只从前 i 个物品中，总体积 <= j

​		属性：集合最大值，集合最小值，数量

**状态计算：**

​		集合划分：不含 i ，包含 i ，不重复（不一定要满足），不漏（一定满足）

![1621575866113](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621575866113.png)



### 完全背包问题

——模板题 AcWing 3. 完全背包问题

每件物品有无限个

**朴素算法：**

​		可能超时

​		f[i, j] = f[i - 1, j - v[i] * k]  + w[i] * k

​		k不能无限大，超过背包容量终止

![1621583437288](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621583437288.png)

**优化算法：**

![1621584188626](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621584188626.png)

之后与01背包问题类似优化

![1621584428104](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621584428104.png)



### 多重背包问题

——模板题 AcWing 4. 多重背包问题

——模板题 AcWing 5. 多重背包问题 II

每个物品有 Si 个

**朴素算法与完全背包问题类似**

**不能直接用完全背包问题的优化方法进行优化**

![1621585489228](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621585489228.png)

**采用二进制进行优化**



### 分组背包问题

——模板题 AcWing 9. 分组背包问题

每组物品有若干个，每组最多只能选一个

![1621595126358](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621595126358.png)



###  线性DP

**数字三角形**

——模板题 AcWing 898. 数字三角形

![1621662232407](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621662232407.png)

从上往下做：

![1621665344162](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621665344162.png)

从下往上做：

![1621686263390](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621686263390.png)

**最长上升子序列**

——模板题 AcWing 895. 最长上升子序列

![1621666260274](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621666260274.png)

![1621666427531](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621666427531.png)

**求最长上升子序列：**

——模板题 AcWing 896. 最长上升子序列 II

![1621666907994](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621666907994.png)

**最长公共子序列**

——模板题 AcWing 897. 最长公共子序列

![1621668286379](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621668286379.png)

![1621668649153](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621668649153.png)



**最短编辑距离**

——模板题 AcWing 902. 最短编辑距离

![1621752325952](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621752325952.png)

![1621752637215](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621752637215.png)



**编辑距离**

——模板题 AcWing 899. 编辑距离

思路与最短编辑距离类似

![1621753136926](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621753136926.png)



### 区间DP

——模板题 AcWing 282. 石子合并

![1621669976691](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621669976691.png)

![1621669954547](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621669954547.png)

![1621670620028](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621670620028.png)



### 计数类DP

——模板题 AcWing 900. 整数划分

**整数问题**

可以看作一个完全背包问题

![1621754385795](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621754385795.png)

![1621754163927](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621754163927.png)

![1621754718599](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621754718599.png)

![1621754888190](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621754888190.png)



### 数位统计DP

——模板题 AcWing 338. 计数问题

**分情况讨论！**

![1621855224336](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621855224336.png)

![1621855822710](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621855822710.png)

![1621866848752](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621866848752.png)

![1621866578777](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621866578777.png)

![1621866691635](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621866691635.png)

### 状态压缩DP

**用一个二进制整数来表示某种状态**

**蒙德里安的梦想**

——模板题 AcWing 291. 蒙德里安的梦想

j 存储i - 1列哪些行横放了1 * 2的矩形

①和②判断状态是否能够转移过来

只枚举了横放的矩形，所以列空白必须不存在连续奇数个0

![1621858153095](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621858153095.png)

![1621863232787](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621863232787.png)

**最短Hamiltan路径**

——模板题 AcWing 91. 最短Hamilton路径

![1621861678793](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621861678793.png)

![1621860774927](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621860774927.png)

​	

### 树形DP

——模板题 AcWing 285. 没有上司的舞会

![1621864250097](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621864250097.png)

![1621864191690](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621864191690.png)

![1621864510648](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621864510648.png)



### 记忆化搜索

**要求不存在环**

**代码复杂度低**

**已经算过的值不会重复计算**

**滑雪游戏**

——模板题 AcWing 901. 滑雪

![1621865222415](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621865222415.png)

![1621865527585](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1621865527585.png)

# 六、贪心

## 1、课堂笔记

**常用数学归纳法和反证法进行证明**

### 区间选点

——模板题 AcWing 905. 区间选点

![1622011247854](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622011247854.png)

### 最大不相交区间数量

——模板题 AcWing 908. 最大不相交区间数量

![1622012253358](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622012253358.png)



### 区间分组

——模板题 AcWing 906. 区间分组

![1622013108961](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622013108961.png)



### 区间覆盖

——模板题 AcWing 907. 区间覆盖

![1622014590366](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622014590366.png)



### 合并果子

——模板题 AcWing 148. 合并果子

**哈夫曼树：每次挑最小的两堆进行合并**

![1622017640229](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622017640229.png)

### 排序不等式

——模板题 AcWing 913. 排队打水

**排队打水**

![1622469752652](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622469752652.png)

### 绝对值不等式

——模板题 AcWing 104. 货仓选址

**货仓选址**

![1622470582307](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622470582307.png)

### 推公式

——模板题 AcWing 125. 耍杂技的牛

**耍杂技的牛**

![1622471824412](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622471824412.png)

![1622471999879](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1622471999879.png)

# 七、回溯

![1623939220332](C:\Users\艾虎\AppData\Roaming\Typora\typora-user-images\1623939220332.png)

```c++
// 回朔代码模板
void backward():
    if (回朔点）：// 这条路走到底的条件。也是递归出口
        保存该结果
        return  ;
    else:
        for (route : all_route_set)  // 逐步选择当前节点下的所有可能route
            if 剪枝条件：
                剪枝前的操作
                return  ; // 不继续往下走了，退回上层，换个路再走
            else：// 当前路径可能是条可行路径
                保存当前数据  // 向下走之前要记住已经走过这个节点了。例如push当前节点
                backward(); // 递归发生，继续向下走一步了。
                回朔清理     // 该节点下的所有路径都走完了，清理堆栈，准备下一个递归。例如弹出当前节点
```

