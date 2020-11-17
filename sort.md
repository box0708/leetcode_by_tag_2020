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