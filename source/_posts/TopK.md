---
title: 【LeetCode】215. Kth Largest Element in an Array
date: 2018-10-19 23:56:57
tags:
- Heapsort
categories:
- 算法
---

算法经典问题TopK

<!-- more -->

> * 类型：PriorityQueue + MinHeap
> * Time Complexity O(NlogK) 因为每次heap插入时间是log(1), 插入k个就是logk
> * Space Complexity O(N)小顶堆

通常来说就是两种解决方案，这里就是其中之一用小顶堆解决的实现。

Python通过调用第三方的module heapq内部就实现了小顶堆的功能(实际自己实现下也不难),这里直接调用就行了。主要是思想很重要~


这里的实现是来自[Leetcode Discuss Python | Priority Queue - 公瑾™](https://leetcode.com/problems/kth-largest-element-in-an-array/discuss/167837/Python-or-Priority-Queue-tm)

```Python
import heapq
class Solution(object):
    def findKthLargest(self, nums, k):
        min_heap = [-float('inf')] * k
        heapq.heapify(min_heap)
        for num in nums:
            if num > min_heap[0]:
                heapq.heappoppush(min_heap, num)
        return min_heap[0]
```
实际上这里熟悉heapq的话,这里只需要:

< = = >

```Python
class Solution(object):
	def findKthLargest(self, nums, k):
		return heapq.nlargest(k,nums)[-1]
```

ps: 关于heapq的使用方法和内部实现可以多下,有助于理解

另外一种是用快排思想实现的Quick Select。与快排不太相同的是,算法进行中不是对每个子数组都递归判断之后归并，会依据k为长度判断子数组进行递归，这里的实现是返回第k个最大元素,如果要使用需要改动一下，可以保存中间的结果项作为最后的返回值。

以下是该方法的Java实现:

```Java
public static int partition(int[] arr, int low, int high){
    int pivotKey = arr[low];
    while(low < high){
        while(low < high && arr[high] >= pivotKey)
            high--;
        swap(arr, low, high);

        while(low < high && arr[low] <= pivotKey)
            low++;
        swap(arr, low, high);
    }
    return low;
}

public int findKthLargest(int[] nums, int k) {
    return quickSelect(nums, k, 0, nums.length - 1);
}

// quick select to find the kth-largest element
public int quickSelect(int[] arr, int k, int left, int right) {
    if (left == right)
        return arr[right];

    //patition方法还是用的 快排中的 partition
    int index = partition(arr, left, right);

    if (index - left + 1 > k)
        return quickSelect(arr, k, left, index - 1);
    else if (index - left + 1 == k)
        return arr[index];
    else
        return quickSelect(arr, k - index + left - 1, index + 1, right);

}
```
另: 排序后直接取也能AC,感叹下这感人的性能= =

3 参考文献

[zxca368的博客](https://blog.csdn.net/xidiancoder/article/details/77781379?utm_source=copy)

James Aspnes, QuickSelect.

