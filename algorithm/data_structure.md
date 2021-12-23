# 常用数据结构

这里用**数组**的方式实现以下各个常用数据结构

## 单链表

含以下操作：
- `addToHead` : 往头结点后面插入结点 （头插法）
- `add` : 往某个结点后面插入结点
- `delete` : 删除某结点

实现时最好结合画图更好理解
<details>
<summary>实现模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
// e: 结点 i 的值
// ne: 结点 i 的 next 值
// head: 头结点指向哪个下标
// idx: 当前用到了哪个点
static int[] e = new int[N], ne = new int[N];
static int head = -1, idx = 0;

public static void addToHead(int x) {
    e[idx] = x;
    ne[idx] = head;
    head = idx;
    idx ++;
}

public static void add(int k, int x) {
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx;
    idx++;
}

public static void delete(int k) {
    ne[k] = ne[ne[k]];
}
```

</details>

## 双链表

含以下操作：
- `addToRight` : 往某结点的右侧插入结点
- `addToLeft` : 不用单独实现，只需对该节点的左边结点做 `addToRight` 操作
- `delete` : 删除某结点

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开

// e: 结点 i 的值
// l: 结点 i 的 left 值 (即左侧结点)
// r: 结点 i 的 right 值 (即右侧结点)
// head: 头结点指向哪个下标
// idx: 当前用到了哪个点
static int[] e = new int[N], l = new int[N], r = new int[N];
static int idx;

public static void init() {
    // 注：这里idx = 0 为左端点， idx = 1 为右端点, 所以 idx 从 2 开始
    r[0] = 1;
    l[1] = 0;
    idx = 2;
}

// 在节点 k 的右侧插入
public static void insertToRight(int k, int x) {
    e[idx] = x;
    r[idx] = r[k];
    l[idx] = k;
    l[r[k]] = idx;
    r[k] = idx;
    idx++;
}

// 删除节点 k
public static void delete(int k) {
    l[r[k]] = l[k];
    r[l[k]] = r[k];
}
```

</details>

## 栈

先进后出的数据结构，只能从栈顶放入元素并从栈顶弹出元素

含以下操作：
- `push` : 将元素推入栈中 （放入栈顶）
- `pop` : 将栈顶元素出栈 
- `isEmpty` : 判断栈是否为空
- `peak` : 查看栈顶元素

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] stk = new int[N]; // 栈底层的数组
static int tt = 0; // 指向栈顶的指针，这里以 tt = 1 开始表示栈中有元素

public static void push(int x) {
    stk[++tt] = x;
}

public static void pop() {
    tt--;
}

public static boolean isEmpty() {
    return tt <= 0;
}

public static int peak() {
    return stk[tt];
}
```

</details>

## 队列

先进先出的数据结构，只能从队尾放入元素并从队头拿出元素

含以下操作：
- `push` : 将元素加入队列中 （放入队尾）
- `pop` : 将队头元素出队 
- `isEmpty` : 判断队列是否为空
- `peak` : 查看队头元素

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] q = new int[N]; // 队列底层的数组
static int hh = 0, tt = -1; // hh 代表指向队头元素，tt 代表指向队尾元素 （tt 从 0 开始表示队列有元素）

public static void push(int x) {
    q[++tt] = x;
}

public static int pop() {
    return (hh++);
}

public static boolean isEmpty () {
    return hh > tt;
}

public static int peak() {
    return q[hh];
}
```

</details>


## 单调栈

快速找到某个元素左边（或右边）离它最近的比它大（小）的元素

以下以找到数组中每个元素**左边第一个比他小的**元素为例，则需要维护一个单调递增的栈，具体原因可以看代码中的注释。
<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] q = new int[N]; // 栈底层的数组，模拟栈
static int tt = -1; // 指向栈顶的指针，这里以 tt = 1 开始表示栈中有元素）

// 遍历 a 数组
for (int i = 0; i < n; i++) {
    int x = a[i];
    
    // 若当前元素比栈顶元素小或相等则弹出栈顶元素
    // 因为 当前元素 比起 栈顶元素 而言，比栈顶元素离 x 近且又比它小，栈顶元素对之后的元素就无用了
    while (tt >= 0 && q[tt] >= x) tt--; 
    
    if (tt < 0) System.out.print(-1 + " "); // 若栈中无元素，返回 -1
    else System.out.print(q[tt] + " ");
    
    q[++tt] = x;
}
```

</details>

## 单调队列

可以模拟滑动窗口，统计窗口中的最大/最小值或者用二分查找窗口中的某个值。

以下以查找数组中以 k 为窗口大小，输出每个窗口的最小值为例。这则需要维护一个单调递增的队列，保证最小值始终在队列的头部。

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] q = new int[N]; // 队列底层的数组，注：存放的是下标，而非元素本身
static int hh = 0, tt = -1; // hh 代表指向队头元素，tt 代表指向队尾元素 （tt 从 0 开始表示队列有元素）
        
hh = 0; tt = -1;
for (int i = 0; i < n; i++) {
    // 若队列大于窗口大小，则从头部开始出队直到符合窗口大小
    if (hh <= tt && i - k + 1 > q[hh]) hh++;
    

    // 从队尾开始淘汰比 当前元素a[i] 大的元素，维护一个单调增队列
    while (hh <= tt && a[q[tt]] >= a[i]) tt--;
    q[++tt] = i;
    
    if (i >= k - 1) wr.write(a[q[hh]] + " "); // 单调增队列最小值在队头
}
```

</details>

### 例题
- [LC 239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

## Trie

Trie树可以用来高效地插入和查找字符串。

<details>
<summary>算法模板 (java 数组实现)</summary>

```java
static int N = 100010;
static int[][] son = new int[N][26];
static int[] cnt = new int[N];
static int idx = 0;

public static void insert(String word) {
    int p = 0;
    
    for (char c : word.toCharArray()) {
        int u = c -'a';
        if (son[p][u] == 0) son[p][u] = ++idx;
        p = son[p][u];
    }
    
    cnt[p]++;
}

public static int query(String word) {
    int p = 0;
    
    for (char c : word.toCharArray()) {
        int u = c - 'a';
        if (son[p][u] == 0) return 0;
        p = son[p][u];
    }
    
    return cnt[p];
}
```

</details>

<details>
<summary>算法模板 (java 内部类实现)</summary>

```java
static class Node {
    Node[] children = new Node[26];
    int cnt = 0;
}

public void insert(Node root, String word) {
    Node p = root;
    
    for (char c : word.toCharArray()) {
        int u = c -'a';
        if (p.children[u] == null) p.children[u] = new Node();
        p = p.children[u];
    }
    
    p.cnt++;
}

public int query(Node root, String word) {
    Node p = root;
    
    for (char c : word.toCharArray()) {
        int u = c - 'a';
        if (p.children[u] == null) return 0;
        p = p.children[u];
    }
    
    return p.cnt;
}
```

</details>

## 并查集

可以以近乎 O(1) 的时间将
1. 两个集合合并
2. 查找两个元素是否在一个集合中

原理：每个集合用一棵树表示，树根即整个集合的编号（代表），每个结点存储它的父结点，以 `p[x]` 代表 `x` 的父结点

每个结点调用 `find()` 便可以迅速的找到该结点所在的树的树根（代表）编号。额外地，使用**路径压缩**来进一步降低时间复杂度。

**路径压缩**：即让每个结点的父节点直接变成所在树的根节点，具体可以看 `find()` 实现

`find()`
```java
public static int find(int x) {
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}
```

1. 如何判断是否为树根？

    `if (p[x] == x)`

2. 如何求 x 的集合编号

    `find(x)`

3. 如何合并两集合

    如合并 x 与 y 两个结点所在的集合
    ```java
    int px = find(x), py = find(y);
    p[px] = py;

4. 如何判断两个结点是否在一个集合中

    `if (find(x) == find(y))`

### 带权并查集

使用额外的数组保存一些**额外信息**，如集合中总共有多少元素等。

例题：
- [食物链](https://www.acwing.com/problem/content/242/)

## 堆

即一个完全二叉树，其保证父节点小于（或大于）其任意一个子结点（或子结点的子结点...）。

（以最小堆为例）
实现一个堆需要什么？

1. 插入一个数 x
    <details>
    <summary>MASK</summary>

    ```java
    heap[++size] = x;
    up(x); // 上浮
    ```

    </details>
    
2. 求当前堆中最小值（即堆顶）
    <details>
    <summary>MASK</summary>
    
    `return heap[1]; // 默认下标从 1 开始`

    </details>

3. 删除最小值（即弹出堆顶）
    <details>
    <summary>MASK</summary>
    
    ```java
    heap[1] = heap[size];
    size--;
    down(1); // 下沉
    ```

    </details>

4. 删除任意一个位置 k
    <details>
    <summary>MASK</summary>
    
    ```java
    heap[k] = heap[size];
    size--;
    down(k); // 下沉
    ```

    </details>
    
5. 修改任意一个位置 k 为 x
    <details>
    <summary>MASK</summary>
    
    ```java
    heap[k] = x;
    // 因为不知道当前 x 应该上浮还是下沉，所以索性都做一遍，不会对结果造成影响
    down(k); // 下沉
    up(k); // 上浮
    ```

    </details>

<details>
<summary>核心函数</summary>

```java
int[] h; // 存储堆的底层数组，默认下标从 1 开始
int size; // 堆的大小

// 将位置 u 下沉
public static void down(int u) {
    int t = u;
    if (2*u <= size && h[2*u] < h[t]) t = 2*u;
    if (2*u + 1 <= size && h[2*u+1] < h[t]) t = 2*u + 1;
    
    if (u != t) {
        swap(u, t);
        down(t);
    }
}

public static void swap(int x, int y) {
    int tmp = h[x];
    h[x] = h[y];
    h[y] = tmp;
}
```

</details>

## 散列表

可以快速地
1. 插入元素
2. 判断元素是否在其中

两种经典的解决冲突实现方式：**开放寻址法**和**拉链法**

<details>
<summary>模拟散列表 (开放寻址法)</summary>

```java
import java.util.Scanner;
import java.util.Arrays;

class Main {
    
    static final int N = 200003; // 通常要开本来数据限制的 2~3 倍，为了减少冲突
    static final int INF = 0x3f; // 大于 1e9 的量级，可以看作无穷
    static int[] h = new int[N];
    
    public static int find(int x) {
        int k = (x % N + N) % N; // 额外 +N 再 %N 是为了防止出现负数
        
        while (h[k] != INF && h[k] != x) {
            k++;
            if (k == N) k = 0;
        }
        
        return k;
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        int n = Integer.parseInt(sc.nextLine());
        Arrays.fill(h, INF);
        
        while ((n--) > 0) {
            String[] line = sc.nextLine().split(" ");
            char od = line[0].charAt(0);
            int x = Integer.parseInt(line[1]);
            int k = find(x);
            
            if (od == 'I') {
                // 1. 插入元素
                h[k] = x;
            } else {
                // 2. 查找是否包含元素
                if (h[k] != INF) System.out.println("Yes");
                else System.out.println("No");
            }
        }
    }
}
```

</details>

<details>
<summary>模拟散列表 (拉链法)</summary>

```java
import java.util.Scanner;
import java.util.Arrays;

class Main {
    
    static int N = 100003; // 为了减少冲突，最好取质数 （某研究的结论）
    static int[] e = new int[N], ne = new int[N], h = new int[N];
    static int idx = 0;
    
    public static void insert(int x) {
        int k = (x % N + N) % N; // 额外 +N 再 %N 是为了防止出现负数
        
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
    
    public static boolean query(int x) {
        int k = (x % N + N) % N;
        
        for (int i = h[k]; i != -1; i = ne[i]) {
            if (e[i] == x) return true;
        }
        
        return false;
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        int m = sc.nextInt();
        sc.nextLine();
        
        Arrays.fill(h, -1);
        
        while ((m--) > 0) {
            String[] line = sc.nextLine().split(" ");
            char od = line[0].charAt(0);
            int x = Integer.parseInt(line[1]);
            
            if (od == 'I') {
                // 1. 插入元素
                insert(x);
            } else {
                // 2. 查找是否包含元素
                if (query(x)) System.out.println("Yes");
                else System.out.println("No");
            }
        }
    }
}
```

</details>

### 字符串哈希

就是**将一个字符串唯一的映射为一个数值**，其目的在于比较字符串时可以以 O(1) 的时候比较它们是否相等。

字符串哈希的方法很多，这里仅介绍一种。形象化地说，就是将一个字符串看作为一个P进制，然后将其转为10进制从而变成我们熟悉的数值。

如一个字符串 ABC ，其哈希函数就应该为 $$ h[ABC] = A*P^2 + B*P^1 + C*P^0 $$

一般地，为了防止字符串映射后的值过大，会再对其哈希值模上一个数 Q ，一般 **Q 取 $2^{64}$**，正好是 java 中 long 的取值范围，**P 取 133 或 13331**，这样可以尽可能地避免冲突。

<details>
<summary>模板题</summary>


### 题目：
> 给定一个长度为n的字符串，再给定m个询问，每个询问包含四个整数l1,r1,l2,r2，请你判断[l1,r1]和[l2,r2]这两个区间所包含的字符串子串是否完全相同。<br>
字符串中只包含大小写英文字母和数字。
### 输入格式
> 第一行包含整数n和m，表示字符串长度和询问次数。<br>
第二行包含一个长度为n的字符串，字符串中只包含大小写英文字母和数字。<br>
接下来m行，每行包含四个整数l1,r1,l2,r2，表示一次询问所涉及的两个区间。<br>
注意，字符串的位置从1开始编号。
### 输出格式
> 对于每个询问输出一个结果，如果两个字符串子串完全相同则输出“Yes”，否则输出“No”。<br>
每个结果占一行。
### 数据范围
> 1≤n,m≤105

### 输入样例：
> 8 3<br>
aabbaabb<br>
1 3 5 7<br>
1 3 6 8<br>
1 2 1 2

### 输出样例：
> Yes<br>
No<br>
Yes

### 解：
```java
import java.util.Scanner;

class Main {
    
    static int N = 100010, P = 131;
    static long[] h = new long[N], p = new long[N];
    
    public static long get(int l, int r) {
        return h[r] - h[l-1]*p[r-l+1];
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        int n = sc.nextInt(), m = sc.nextInt();
        sc.nextLine();
        String s = sc.nextLine();
        
        p[0] = 1;
        for (int i = 1; i <= n; i++) {
            char c = s.charAt(i-1);
            p[i] = p[i-1] * P;
            h[i] = h[i-1] * P + c;
        }
        
        while ((m--) > 0) {
            int l1 = sc.nextInt(), r1 = sc.nextInt();
            int l2 = sc.nextInt(), r2 = sc.nextInt();

            if (get(l1, r1) == get(l2, r2)) System.out.println("Yes");
            else System.out.println("No");
        }
    }
}
```

</details>

一些例题：
- [LC 1316. 不同的循环子字符串](https://leetcode-cn.com/problems/distinct-echo-substrings/)