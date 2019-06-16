---
title: 【LeetCode】837. New 21 Game
date: 2018-08-11 11:42:55
tags:
- DP
categories:
- 算法
---

### Problems:
Alice plays the following game, loosely based on the card game "21".

Alice starts with 0 points, and draws numbers while she has less than K points.  During each draw, she gains an integer number of points randomly from the range [1, W], where W is an integer.  Each draw is independent and the outcomes have equal probabilities.

Alice stops drawing numbers when she gets K or more points.  What is the probability that she has N or less points?

<!-- more -->

**Example 1:**

```
Input: N = 10, K = 1, W = 10
Output: 1.00000
Explanation:  Alice gets a single card, then stops.
```

**Example 2:**

```
Input: N = 6, K = 1, W = 10
Output: 0.60000
Explanation:  Alice gets a single card, then stops.In 6 out of W = 10 possibilities, she is at or below N = 6 points.
```

**Example 3:**

```
Input: N = 21, K = 17, W = 10
Output: 0.73278
```

**Note:**

1. ==0 <= K <= N <= 10000==
2. ==1 <= W <= 10000==
3. Answers will be accepted as correct if they are within ==10^-5== of the correct answer.
4. The judging time limit has been reduced for this question.


---
DP动态规划。虽然知道需要DP规划来解决问题但是没有具体思路。看到一些大神的想法特别记录如下。

Solution1:

思路:
题目的意思是，在已有点数不超过K的情况下，可以从[1, W]中任选一个数，保证和不超过N。求最后当点数超过K时，这个点数小于N的概率。这个问题其实类似于爬楼梯，每次可跨上[1, W]阶楼梯。 
采用动态规划，dp[i]表示点数为i的概率，只有当已有点数小于K时，才能再取数，所以有： 
当i<=K时，dp[i] = (前W个dp之和) / W 
当K< i< K+W时，前一次最多只能是K-1点，最少为i-W点，所以dp[i] = (dp[K-1]+dp[K-2]+…+dp[i-W])/W 
当i >= K+W时，无论如何都无法取到，dp[i] = 0

```Python
class Solution(object):
    def new21Game(self, N, K, W):
        """
        :type N: int
        :type K: int
        :type W: int
        :rtype: float
        """
        if K == 0: return 1
        dp = [1.0] + [0.0] * N
        # Wsum表示(前W个dp之和)/W
        Wsum = 1.0000
        for i in range(1, N + 1):
            dp[i] = Wsum / W
            # 因为当i>=K时，不能再取数，因此后面的概率不能累加
            if i < K: Wsum += dp[i]
            # 因为只需要计算前w个dp之和，所以当i>=W时，减去最前面的dp。
            if 0 <= i - W < K: Wsum -= dp[i - W]
        return sum(dp[K:])
```
Solution2:

同样是动态规划，下面这样同时额外维护一个结果列表，可以减少复杂度。
思路类似上面，不过是从后往前逆推得到结果。当然由于增加了空间复杂度和维护列表的成本，速度也不是很快。

```Python
class Solution(object):
    def new21Game(self, N, K, W):
        """
        :type N: int
        :type K: int
        :type W: int
        :rtype: float
        """
        dp = [0.0]*(N+W+2)
        sum = [0.0]*(N+W+2)
        for i in range(N+W+1,-1,-1):
            if i>N: continue
            if i>=K: dp[i]=1.0
            else: dp[i]=(sum[i+1]-sum[i+1+W])/W
            
            sum[i] = sum[i+1]+dp[i]
        
        return dp[0]
```

Solution3:

社区高效答案。理论上同解题3，不过效率比使用额外列表维护要高的多，同时减少了判断的开销。

```Python
class Solution:
    def new21Game(self, N, K, W):
        """
        :type N: int
        :type K: int
        :type W: int
        :rtype: float
        """
        """
        he key recursion is f(x) = (1/W) * (f(x+1) + f(x+2) + ... + f(x+W))
        This is because there is a probability of \frac{1}{W} to draw each card from 1 to W
        """
        dp = [0.0] * (N + W + 1)
        # dp[x] = the answer when Alice has x points
        for k in range(K, N + 1):
            dp[k] = 1.0

        S = min(N - K + 1, W)
        # S = dp[k+1] + dp[k+2] + ... + dp[k+W]
        for k in range(K - 1, -1, -1):
            dp[k] = S / W
            S += dp[k] - dp[k + W]

        return dp[0]
```

