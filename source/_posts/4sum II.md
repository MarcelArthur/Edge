---
title: 【LeetCode】454. 4Sum II
date: 2019-04-28 14:55:56
categories:
- 算法
---

4 sum 问题拓展

<!-- more -->

Given four lists A, B, C, D of integer values, compute how many tuples `(i, j, k, l)` there are such that `A[i] + B[j] + C[k] + D[l]` is zero.

To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of -228 to 228 - 1 and the result is guaranteed to be at most 231 - 1.

**Example:**

```
Input:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

Output:
2

Explanation:
The two tuples are:
1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```

Solution

第一种解法: 简单粗暴。我们可以认为要使得和为0，任意两组数的任意数字的和产生的两个数互为相反数。 AC more than 26.38%

```python
class Solution:
    def fourSumCount(self, A: List[int], B: List[int], C: List[int], D: List[int]) -> int:
        sumber_dict = dict()
        
        for i in range(len(A)):
            for j in range(len(B)):
                if A[i] + B[j] in sumber_dict:
                    sumber_dict[A[i] + B[j]] += 1
                else:
                    sumber_dict[A[i] + B[j]] = 1
                    
        count = 0
        
        for i in range(len(C)):
            for j in range(len(D)):
                if 0 - (C[i] + D[j]) in sumber_dict:
                    count += sumber_dict[0 - (C[i] + D[j])]
        return count      
```



第二种解法: 查找表。建立元素频次映射表，根据互为相反数出现的频次，查找得到最终符合target值的结果，省去了部分计算操作效率更高。

```python
class Solution:
    def fourSumCount(self, A: List[int], B: List[int], C: List[int], D: List[int]) -> int:
        A_dict = {}
        
        for i in A:
            if i in A_dict:
                A_dict[i] += 1
            else:
                A_dict[i] = 1
                
        B_dict = {}
        
        for i in B:
            if i in B_dict:
                B_dict[i] += 1
            else:
                B_dict[i] = 1
                
        C_dict = {}
        
        for i in C:
            if i in C_dict:
                C_dict[i] += 1
            else:
                C_dict[i] = 1
                
        D_dict = {}
        
        for i in D:
            if i in D_dict:
                D_dict[i] += 1
            else:
                D_dict[i] = 1
                
        AB = {}
        
        for i in A_dict:
            for j in B_dict:
                if i+j in AB:
                    AB[i+j] += A_dict[i] * B_dict[j]
                else:
                    AB[i+j] = A_dict[i] * B_dict[j]
                    
        count = 0
        
        for i in C_dict:
            for j in D_dict:
                if -i-j in AB:
                    count += C_dict[i] * D_dict[j] * AB[-i-j]
                    
        return count
```



相关:

[4 sum](https://leetcode.com/problems/4sum/)

