# 二分查找

> [二分查找参考资料](https://github.com/labuladong/fucking-algorithm/blob/master/%E7%AE%97%E6%B3%95%E6%80%9D%E7%BB%B4%E7%B3%BB%E5%88%97/%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE%E8%AF%A6%E8%A7%A3.md)

常用解题思路
- 按照索引进行搜索（适用于排序数组，在一个维度上呈现某种规律的情况）
- 按照值进行搜索（数组并未排序，想要寻找一个特定的值）
- 注意区间条件的写法（有没有等于号，left/right的更新条件）

---

### [378. Kth Smallest Element in a Sorted Matrix](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/)
```
Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

Example:

matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
],
k = 8,

return 13.
```
- 此题matrix不是完全有序，按照值进行排序
- > 我们遍历完所有的行可以找出**中间数是第几小的数**，然后k比较，进行二分查找
```c++
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        int n = matrix.size();
        int left = matrix[0][0], right = matrix[n-1][n-1];
        // 按照值搜索。左上/右下为两个最值
        // 核心思想：先求mid，然后看matrix里面<=mid的数字有多少个(中间数是第几小的数字)，根据count和k的大小关系更新左右值
        // 不懂的点：最后不会返回一个不在matrix里面的元素吗？
        while ( left <= right){
            int mid = left + (right - left)/2;
            int count=0, j = n-1;
            for ( int i=0; i < n; i++){
                while ( j >= 0 && matrix[i][j] > mid){
                    j--;
                }
                count += (j+1);
            }
            if ( count < k){
                left =  mid+1;
            }
            else{
                right = mid-1;
            }
        }
        return left;
    }
};
```

---

### [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
```
Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.

If target is not found in the array, return [-1, -1].

Follow up: Could you write an algorithm with O(log n) runtime complexity?
(当谁没读过书呢？log(n)基本就是二分查找)
Example 1:

Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```
- 此题先找左边界，再找右边界
- 有时候别想复杂了，一遍不行就两遍，反正$O(2* log n)$也是$O(log n)$
- 你tm千万记得手别滑，现在面试都是OJ，一定要ac，别查了半天发现是打错字了。
```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        // 二分查找，找左右边界
        if(nums.size() <= 0){
            return vector<int>(2,-1);
        } 
        if (nums.size() == 1){
            if(nums[0] == target){
                return vector<int>(2,0);
            }
            else{
                return vector<int>(2,-1);
            } 
        }
        // 先找左边界 再找右边界
        vector<int> res(2, -1);
        int left_bound = -1, left=0, right=nums.size()-1;
        while ( left <= right){
            int mid = left + (right - left)/2;
            if (nums[mid] >= target){
                right = mid-1;
            }
            else{
                left = mid + 1;
            }
        }
        left_bound = left;
        
        int right_bound = -1;
        left=0, right=nums.size()-1;
        while ( left <= right){
            int mid = left + (right - left)/2;
            if (nums[mid] <= target){
                left = mid+1;
            }
            else{
                right = mid -1;
            }
        }
        right_bound = right;
        if (right_bound >= left_bound){
            res[0] = left_bound;
            res[1] = right_bound;
        }
        return res;
        
    }
};
```
---
## 原数组带旋转的二分查找

ps: 此类问题原数组会分有没有重复元素。有重复元素则判断是哪半边有序的时候，`==`条件单独判断，右边界`--`.

### [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

>You are given an integer array nums sorted in ascending order, and an integer target.
>
> Suppose that nums is rotated at some pivot unknown to you beforehand (i.e., $[0,1,2,4,5,6,7]$ might become $[4,5,6,7,0,1,2]$).
>
> If target is found in the array return its index, otherwise, return -1.
- 数组被rotate过，则总有半边是有序，符合二分查找条件
- 左边有序，`nums[mid] > nums[right]`，即：$[4,5,6,7,0,1,2]$
- 右边有序，`nums[mid] < nums[right]`，即：$[7,0,1,2,4,5,6]$
- 然后判断二分查找边界更新条件
```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size()-1;
        int len = nums.size();
        while ( left <= right){
            int mid = left + (right-left)/2;
            if (nums[mid] == target){
                return mid;
            }
            // 数组左边有序
            else if (nums[mid] > nums[right]){
                if (nums[mid] > target && target >= nums[left]){
                    right = mid-1;
                }
                else{
                    left = mid+1;
                }
            }
            // 数组右边有序
            else{
                if (nums[mid] < target && target <= nums[right]){
                    left = mid+1;
                }
                else{
                    right = mid-1;
                }
            }
        }
        return -1;
    }
};
```

### [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

> Suppose an array of length n sorted in ascending order is rotated between 1 and n times. For example, the array nums = $[0,1,2,4,5,6,7]$ might become:
> 
> $[4,5,6,7,0,1,2]$ if it was rotated 4 times.
> 
> $[0,1,2,4,5,6,7]$ if it was rotated 7 times.
> 
> Notice that rotating an array $[a[0], a[1], a[2], ..., a[n-1]]$ 1 time results in the array $[a[n-1], a[0], a[1], a[2], ..., a[n-2]]$.
> 
>Given the sorted rotated array nums, return the **minimum** element of this array.
- 同上一题
```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int res = -1;
        int left = 0, right = nums.size()-1;
        while(left < right){
            int mid = left + (right - left)/2;
            if (nums[mid] < nums[right]){ 
                // 右半边有序（递增），直接right=mid，不要想复杂
                right = mid;
            }
            else{
                // 左半边有序（递增），则最小值（原数组起点）被rotate到右半边
                left = mid+1;
            }
        }
        return nums[left];
    }
};
```
