---
title: 【LeetCode】312.Burst Balloons
date: 2019-01-31 20:56:11
tags:
- DP
- 分治
categories:
- 算法
---

Given `n` balloons, indexed from `0` to `n-1`. Each balloon is painted with a number on it represented by array `nums`. You are asked to burst all the balloons. If the you burst balloon `i`you will get `nums[left] * nums[i] * nums[right]`coins. Here `left` and `right` are adjacent indices of `i`. After the burst, the `left` and `right` then becomes adjacent.

Find the maximum coins you can collect by bursting the balloons wisely.

<!-- more -->

**Note:**

- You may imagine `nums[-1] = nums[n] = 1`. They are not real therefore you can not burst them.
- 0 ≤ `n` ≤ 500, 0 ≤ `nums[i]` ≤ 100

**Example:**

```
Input: [3,1,5,8]
Output: 167 
Explanation: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
             coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
```



解题思路:

[Burst Balloons-书影博客](http://bookshadow.com/weblog/2015/11/30/leetcode-burst-balloons/)

[Burst Balloons DP分析](https://blog.csdn.net/swartz2015/article/details/50561199)

根据前面的分析，该问题可以分解为多个子问题，并逐一解决。于是，我们是否可以尝试可以用分治方法来解决呢？

*这里需要明确可以用分治方法解决的问题以及可以用DP解决的问题的异同：*

*分治和DP都需要将原问题分解成小问题，然后逐一解决；不过分治方法的每个小问题都是不相关的，而DP的子问题则是重叠的(overlapping)。可以参见wiki百科上面的解释：dynamic programming。*

​        但是通过前面的分析也知道，之前描述的子问题都是重叠的 (*比如你在计算踩K个气球时的maxcoin，肯定会涉及到踩K-1个气球时的结果，这也是可以用bottom up 的意义*)，因此根本不能用分治方法来求解。自然的一个想法是，我们可不可以先把整体分割，再分别在被分割的各个子整体中用bottom up。这显然是可行的。不过问题在于怎么分割整体，因为整体的分割需要保证各个整体在后面的计算中要保持相互独立性。比如对于[a1,a2,a3,a4,a5,a6,......,an]，将分割成两个子整体，分割点为k，则得到 N1 = [a1,a2,a3,....,a(k-1)], N2 = [a(k+1),a(k+2),.....,an]。这里分割点k的意义是踩破了第k个气球。于是把整体分成了两部分，问题在于，根据计算规则，k气球破了之后，a(k-1)和a(k+1)会变成相邻的，如果此时踩a(k-1)或者a(k+1)，则都会收到另一个子整体的影响，这样的话，两个子问题就不独立，也就不能用分治了。所以关键的问题在于确定k。

可以发现：

​        *N1和N2相互独立    <=>  k点为对于整体N的游戏时，最后一个被踩破的气球。*

也就是k点被踩破之前，N1和N2重点的气球都不会相互影响。于是我们就成功构造了子问题。因此分治加dp就可以对问题进行求解了。

已知的状态转移方程:

```c++
dp[left][right] = max{dp[left][right] , nums[left] * nums[i] * nums[right]  +  nums[left] * nums[i]  +  nums[i] * nums[right]}
```

其中 `left<i<right , dp[left][right]`即为当前子问题：第left和第right之间位置的气球的maxcoin

C ++ 版本:

```C++
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int arr[nums.size()+2];
        
        for(int i=1;i<nums.size()+1;++i)arr[i] = nums[i-1];
        arr[0] = arr[nums.size()+1] = 1;
        
        int dp[nums.size()+2][nums.size()+2]={};
        int n = nums.size()+2;
        
        for(int k=2;k<n;++k)
        {
            for(int left = 0;left<n-k;++left){
                int right = left + k;
                for(int i=left+1;i< right; ++i)
                {
                    dp[left][right] = max(dp[left][right],arr[left]*arr[i]*arr[right] + dp[left][i] + dp[i][right]);
                }
            }    
        }
        return dp[0][n-1];
    }
};

```

Python版本:

样例来自[LeetCode DIscuss](https://leetcode.com/problems/burst-balloons/discuss/76257/Python-solution%3A-beats-98.31)

```python
class Solution(object):
    def maxCoins(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # Good Solution 
        # https://leetcode.com/problems/burst-balloons/discuss/76257/Python-solution%3A-beats-98.31
        nums = [1] + nums + [1] 
        table = [[0 for x in range(len(nums))] for y in range(len(nums))]
        # initialize solutions (table) for 3 consecutive elements
        k = 0
        while k + 2 < len(nums):
            left, right = k, k + 2
            table[left][right] = nums[k] * nums[k + 1] * nums[k + 2]
            k += 1
        for l in range(3, len(nums)):
            k = 0
            while k + l < len(nums):
                left, right = k, k + l
                solutions = []
                for i in range(left + 1, right):
                    ans = table[left][i] + nums[left] * nums[i] * nums[right] + table[i][right]
                    solutions.append(ans)
                solution = max(solutions)
                table[left][right] = solution
                k += 1
        return table[0][-1]
```


