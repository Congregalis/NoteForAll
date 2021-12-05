# 位运算
主要记一些位运算常用操作

- 求 n 的第 k 位数字

    `n >> k & 1`
- 求 n 的最后一位 1

    `n & -n`

- 移除 n 的最低位 1

    `n & (n - 1)`

- 异或的性质

    `x ^ 0 = x` 任何数异或 0 都是该数本身
    `x ^ x = 0` 相同的数异或为 0

## 相关练手题目

- [LeetCode 231. 2 的幂](https://leetcode-cn.com/problems/power-of-two/)
- [LeetCode 剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)
- [LeetCode 136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)
- [LeetCode 137. 只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)
- [LeetCode 260. 只出现一次的数字 III](https://leetcode-cn.com/problems/single-number-iii/)
- [LeetCode 剑指 Offer 53 - II. 0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)
- [LeetCode 389. 找不同]()
- [LeetCode 1442. 形成两个异或相等数组的三元组数目]()
- [LeetCode 461. 汉明距离]()
- [LeetCode 1356. 根据数字二进制下 1 的数目排序]()
- [LeetCode 693. 交替位二进制数]()
