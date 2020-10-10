此处记录一些能想出思路但是第一遍写不出来的题

---

# [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

> Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.
>Example:
>
>Input: [0,1,0,2,1,0,1,3,2,1,2,1]
>Output: 6

## 解法1 双指针
```
class Solution {
public:
    int trap(vector<int>& height) {
    // 双指针，从两边往中间遍历
    // 发现比最大值低的，那么会存水；更新最大值
        if (height.empty()){
            return 0;
        }
        
        int n = height.size();
        int left_max = height[0];
        int right_max = height[n-1];
        int l=0, r=n-1;
        int res = 0;
        while (l <= r){
            if (left_max <= right_max){
                res += max(0, left_max - height[l]);
                left_max = max(left_max, height[l]);
                l++;
            }
            else{
                res += max(0, right_max - height[r]);
                right_max = max(right_max, height[r]);
                r--;
            }
                
        }
        return res;
    }
};
```

## 解法2 单调栈
todo

---
# [769. Max Chunks To Make Sorted](https://leetcode.com/problems/max-chunks-to-make-sorted/)
> Given an array arr that is a permutation of [0, 1, ..., arr.length - 1], we split the array into some number of "chunks" (partitions), and individually sort each chunk.  After concatenating them, the result equals the sorted array.
> 
> What is the most number of chunks we could have made?
> 
> Example 2:
> 
> Input: arr = [1,0,2,3,4]
> 
> Output: 4
> 
> Explanation:
> We can split into two chunks, such as [1, 0], [2, 3, 4].
> However, splitting into [1, 0], [2], [3], [4] is the highest number of chunks possible.
- 由于题意说了，`arr`里数字就是 0 - i，能分块，代表当前块内最大数字要在相应下标框出范围内
- 例 ： 【3，2，1，0】，最大值3，最大下标为3，则可以分块
```
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        int cur_max = 0, res = 0;
        for ( int i=0; i < arr.size(); i++){
            cur_max = max( cur_max, arr[i]);
            if ( cur_max == i){
                res += 1; 
            }
        }
        return res;
    }
};
```
