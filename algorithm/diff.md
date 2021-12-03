# 差分

差分即前缀和的逆运算

## 一维

区间 [l, r] 上每个数加 c ,即对应拆分数组的 b[l] 加 c 和 b[r+1] 减 c

<details>
<summary>核心代码 (java)</summary>


```java
public static void insert(int l, int r, int c) {
    b[l] += c;
    b[r+1] -= c;
}

```

</details>

## 二维

以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵中的所有元素加上 c

<details>
<summary>核心代码 (java)</summary>

```java
public static void insert(int x1, int y1, int x2, int y2, int c) {
    b[x1][y1] +=c ;
    b[x1][y2+1] -= c;
    b[x2+1][y1] -= c;
    b[x2+1][y2+1] += c;
}

```

</details>

## 相关练手题目

- [LeetCode 1109. 航班预订统计](https://leetcode-cn.com/problems/corporate-flight-bookings/)
- [LeetCode 1094. 拼车](https://leetcode-cn.com/problems/car-pooling/)
- [LeetCode 1893. 检查是否区域内所有整数都被覆盖](https://leetcode-cn.com/problems/check-if-all-the-integers-in-a-range-are-covered/)
- [LeetCode 1854. 人口最多的年份](https://leetcode-cn.com/problems/maximum-population-year/)
- [LeetCode 370. 区间加法（带锁题）]()
- [LeetCode 995. K 连续位的最小翻转次数](https://leetcode-cn.com/problems/minimum-number-of-k-consecutive-bit-flips/)
- [LeetCode 732. 我的日程安排表 III](https://leetcode-cn.com/problems/my-calendar-iii/)
- [LeetCode 1674. 使数组互补的最少操作次数](https://leetcode-cn.com/problems/minimum-moves-to-make-array-complementary/)

- [AcWing 101. 最高的牛](https://www.acwing.com/problem/content/description/103/)