# 排序相关

## 归并排序

### [493. Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)
```
Given an array nums, we call (i, j) an important reverse pair if i < j and nums[i] > 2*nums[j].

You need to return the number of important reverse pairs in the given array.

Example1:

Input: [1,3,2,3,1]
Output: 2
Example2:

Input: [2,4,3,5,1]
Output: 3
```
- 归并排序思想。此题实现了归并排序代码与求逆序对的解法
- 注意边界条件，当前指针指到哪里，算区间长度是否需要+1等等细节
```
class Solution {
public:
    int merge(vector<int>& nums, int left, int mid, int right){
        int length = right - left + 1;
        vector<int> helper(length, 0);
        int ll=left, rr=mid+1, result=0;
        // 求解代码
        for ( int i=left; i <= mid; i++){
            while ( rr <= right && nums[i]/2.0 > nums[rr]){
                rr++;
            }
            result += rr-(mid+1) ; // 此处注意，rr已经后移了一位出来，求中间有几个元素不需要+1
        }
        // 归并操作代码
        int index=0;
        ll = left;
        rr = mid+1;
        while(ll <= mid && rr <= right){
            if ( nums[ll] <= nums[rr]){
                helper[index++] = nums[ll++];
            }
            else{
                helper[index++] = nums[rr++];
            }
        }
        while(ll <= mid){
            helper[index++] = nums[ll++];
        }
        while(rr <= right){
            helper[index++] = nums[rr++];
        }
        for ( int i=left; i <= right; i++){
            nums[i] = helper[i-left];
        }
        return result;
        
        
    }
    
    int mergeSort(vector<int>& nums, int left, int right){
        if (left == right){
            return 0;
        }
        int mid = left + (right - left)/2;
        //if ( left < right){
            int aaa = mergeSort(nums, left, mid);
            int bbb = mergeSort(nums, mid+1, right);
            int ccc = merge(nums, left, mid, right);
            return aaa+bbb+ccc;
        //}
    }
    
    
    int reversePairs(vector<int>& nums) {
        if (nums.size() <= 1){
            return 0;
        }
        return mergeSort(nums, 0, nums.size()-1);
    }
};
```
## 快速排序
 
 [参考资料](https://blog.csdn.net/Handsome2013/article/details/82858209)

### [75. Sort Colors](https://leetcode.com/problems/sort-colors/)
```
Given an array nums with n objects colored red, white, or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

Example 1:

Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```
- 快排的思路
- “小于区域推着等于区域往前走”
- 注意：0移到小于区域，left和index指针都++；2移到大于区域，index指针不加（可能把0换过来，还得检查）。
```
class Solution {
public:
    void sortColors(vector<int>& nums) {
        if (nums.size() <= 1){
            return;
        }
        int left=0, right=nums.size()-1, index=0;
        while ( index <= right){
            if (nums[index] == 0 ){
                swap(nums[index++], nums[left++]);
            }
            else if (nums[index] == 1){
                index++;
            }
            else{
                swap(nums[index], nums[right--]);
            }
        }
        return;
    }
};
```

### [324. Wiggle Sort II](https://leetcode.com/problems/wiggle-sort-ii/)

```
Given an unsorted array nums, reorder it such that nums[0] < nums[1] > nums[2] < nums[3]....

Example 1:

Input: nums = [1, 5, 1, 1, 6, 4]
Output: One possible answer is [1, 4, 1, 5, 1, 6].
Example 2:

Input: nums = [1, 3, 2, 2, 3, 1]
Output: One possible answer is [2, 3, 1, 3, 1, 2].
```
- 原数组**先排序**，然后二分，较大/较小部分各取一个
- follow-up解法不会
```
class Solution {
public:
    void wiggleSort(vector<int>& nums) {
        if (nums.size() <= 1){
            return;
        }
        vector<int> tmp(nums);
        sort(tmp.begin(), tmp.end()); // 先对原数组排序
        int smallIndex = ((nums.size()+1) / 2);
        int largeIndex = nums.size();
        for ( int i=0; i < nums.size(); i++){
            if (i % 2 != 0){
                nums[i] = tmp[--largeIndex];
            }
            else{
                nums[i] = tmp[--smallIndex];
            }
        }
    }
};
```