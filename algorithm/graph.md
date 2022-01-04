# 图论

图论很重要

## 构图方式

若图比较**稠密**，或者**点个数不多**，可以使用**邻接矩阵**存储图。

若图比较**稀疏**，或者**点个数达到 1e5 以上** ，可以使用**邻接表**存储图。

### 邻接表

```java
static int[] h = new int[N], e = new int[N], ne = new int[N];
static int[] dist = new int[N], w = new int[N];

// 添加一条 a 到 b 的边，c 为该边的权重
public static void add(int a, int b, int c) {
    e[idx] = b;
    ne[idx] = h[a];
    w[idx] = c;
    h[a] = idx++;
}
```

### 邻接矩阵

```java
static int[][] g = new int[N][N];

// 添加一条 a 到 b 的边，c 为该边的权重，取最小值是为了防止重边，若非求最短路径，可以使用其他策略
g[a][b] = Math.min(g[a][b], c);
```

## 拓扑排序

使用 BFS 的思想，入度为 0 的结点先入队，然后依次删除队列中的点所连接的边，若此时有结点的入度变为 0，则使其入队。

参考代码：
```java
static int N = 100010;
// h, e, ne 数组是以邻接表形式存图，若不了解可以看数据结构章节
// d 记录结点的入度
static int[] h = new int[N], e = new int[N], ne = new int[N], d = new int[N];
// 存储拓扑排序的结果
static List<Integer> ans = new ArrayList<>();
static int idx, n, m;

public static void bfs() {
        Queue<Integer> q = new LinkedList<>();
        
        for (int i = 1; i <= n; i++) {
            if (d[i] == 0) {
                q.add(i);
                ans.add(i);
            }
        }
        
        while (!q.isEmpty()) {
            int cur = q.poll();
            

            // 遍历该结点所有的出边
            for (int i = h[cur]; i != -1; i = ne[i]) {
                int j = e[i];
                
                d[j]--; // 删除该边，使对应结点入度减一
                // 对应结点若入度为 0 ，则入队，并加入答案中
                if (d[j] == 0) {
                    q.add(j);
                    ans.add(j);
                }
            }
        }
    }
```

例题：
- [LC 207. 课程表](https://leetcode-cn.com/problems/course-schedule/)

## Dijkstra

求**带权图**的**单源最短路径**，支持自环与重边，**不支持负边权**。

### 朴素版 Dijkstra

因这种方法只能处理点和边少的情况，所以使用邻接矩阵。

参考代码：
```java
public static int dijkstra(int start, int n) {
    Arrays.fill(dist, 0x3f3f3f3f);
    dist[start] = 0;
    
    for (int i = 1; i <= n; i++) {
        
        // 1. 寻找不在集合中的距离最小的点
        int t = -1;
        for (int j = 1; j <= n; j++) {
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;
        }
        
        // 2. 将该点加入集合
        st[t] = true;

        // 3. 利用该点更新其他点的最小距离
        for (int j = 1; j <= n; j++) {
            dist[j] = Math.min(dist[j], dist[t] + g[t][j]);
        }
    }
    
    return dist[n] == 0x3f3f3f3f ? -1 : dist[n];
}
```

### 优化版 Dijkstra

因这种方法一般处理点和边比较多的情况，所以使用邻接表。

使用**最小堆**来优化，使用 O(1) 的时间找到不在集合中的距离最近的点，使用 O(mlogn) 的时间更新最小距离吗，从而优化整体时间复杂度

参考代码：
```java
public static int dijkstra(int n) {
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> { return a[1] - b[1]; });
    
    Arrays.fill(dist, 0x3f3f3f3f);
    dist[1] = 0;
    pq.offer(new int[] {1, 0});
    while (!pq.isEmpty()) {
        // 1. 使用最小堆， O(1) 的时间拿到距离最小的点
        int[] t = pq.poll();
        
        int index = t[0], distance = t[1];
        
        // 2. 若该点已在集合中，则忽略
        if (st[index]) continue;
        st[index] = true;
        
        // 3. 利用该点更新其他点的最小距离
        for (int i = h[index]; i != -1; i = ne[i]) {
            int j = e[i];
            
            if (dist[j] > distance + w[i]) {
                dist[j] = distance + w[i];
                pq.offer(new int[] {j, dist[j]});
            }
        }
    }
    
    return dist[n] == 0x3f3f3f3f ? -1 : dist[n];
}
```

## bellman-ford

非常暴力的做法，但可以处理**负权边**，无法处理**负环**（负权回路）。最大的特点是可以可以找某点到某点**最多经过 k 条边**的最短距离，即处理**有边数限制的最短路**。

具体思想是遍历 n （n个点）或 k （最多经过 k 条边） 次，在其中再遍历每个边并更新最短距离。所以时间复杂度为 O(nm) (n为点数，m为边数)。

参考代码：
```java
static int N = 510, M = 10010;
static int[] dist = new int[N], backup = new int[N]; // 每个点到1号点的最短距离，backup存储上次迭代的结果
static Edge[] edges = new Edge[M]; // 存储所有边

static class Edge {
    // 每条边由 a-边起点、b-边终点，c-该边的权重 构成
    int a, b, c;
    
    Edge(int a, int b, int c) {
        this.a = a;
        this.b = b;
        this.c = c;
    }
}

public static void bellmanFord(int n, int m, int k) {
    Arrays.fill(dist, 0x3f3f3f3f);
    dist[1] = 0;
    
    // 遍历 k 次（边数限制为 k，若无边数限制，则遍历 n 次）
    for (int i = 0; i < k; i++) {
        System.arraycopy(dist, 1, backup, 1, n); // 拷贝数组，存储上次迭代结果
        
        // 遍历所有边
        for (int j = 0; j < m; j++) {
            int a = edges[j].a, b = edges[j].b, c = edges[j].c;
            
            dist[b] = Math.min(dist[b], backup[a] + c); // 更新最短距离
        }
    }
}

// 小细节：这里不能为 dist[n] == 0x3f3f3f3f ，因为存在负权边，有可能存在 dist[n] 被更新为 0x3f 减某个不大的数，所以这里要这样判断
if (dist[n] > 0x3f3f3f3f / 2) System.out.println("impossible");
else System.out.println(dist[n]);
```

## SPFA
本质上就是**对 bellman-ford 算法的优化**，在 bellman-ford 算法中，我们会遍历所有边来更新最短距离，但实际上不需要遍历这么多。当一个点的前驱结点更新的时候才需要更新这个点，因此我们可以使用一个**队列**来加入需要更新的点，减少一些时间开销。

注意，在最差情况下 spfa 算法就退化成 bellman-ford 了，也就是要遍历所有边。

参考代码：
```java
public static int spfa(int n) {
    Arrays.fill(dist, 0x3f3f3f3f);
    
    Queue<Integer> q = new LinkedList<>();
    q.offer(1);
    dist[1] = 0;
    st[1] = true; // st 用来记录某个点是否在队列中
    
    while (!q.isEmpty()) {
        int t = q.poll();
        st[t] = false;
        
        // 遍历所有出边，查看是否有点需要更新
        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (dist[j] > dist[t] + w[i]) {
                dist[j] = dist[t] + w[i];
                if (!st[j]) {
                    // 若该点 j 不在队列中，则将其入队，并更新 st 数组
                    q.offer(j);
                    st[j] = true;
                }
            }
        }
    }
    
    return dist[n];
}
```

SPFA 还可以用来**求是否存在负环**，参考代码如下：
```java
public static boolean spfa(int n) {
    Queue<Integer> q = new LinkedList<>();

    // 若是求是否存在负环，则每个点都要入队，因为不是求从 点1 到某个点是否存在负环，而是所有点为起点的情况下是否存在负环
    for (int i = 1; i <= n; i++) {
        q.offer(i);
        st[i] = true;
    }
    
    while (!q.isEmpty()) {
        int t = q.poll();
        st[t] = false;
        
        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            
            if (dist[j] > dist[t] + w[i]) {
                dist[j] = dist[t] + w[i];
                cnt[j] = cnt[t] + 1; // 更新 点j 的边数
                if (cnt[j] >= n) return true; // 如果 cnt[i] 中有超过 n - 1 条边，则说明存在负环
                if (!st[j]) {
                    q.offer(j);
                    st[j] = true;
                }
            }
        }
    }

    return false;
}
```
**cnt 数组存放某个点到源点最短距离上有多少条边**。根据**抽屉原理**，总共有 n 个点即 n - 1 条边，而如果 cnt[i] 中有超过 n - 1 条边，则说明存在负环。

## Floyd

解**多源最短路**算法，可以求任意两点间的最短距离，可以存在负权边，但不能存在负权回路。
思路很简单，就是暴力三重循环，本质上是基于 dp 的思想。

参考代码：
```java
public static void floyd(int n) {
    for (int k = 1; k <= n; k++)
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                d[i][j] = Math.min(d[i][j], d[i][k] + d[k][j]);
}
```