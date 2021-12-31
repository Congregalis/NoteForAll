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




