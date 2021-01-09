# 数组相关题目

### [15. 3Sum](https://leetcode-cn.com/problems/3sum/)
> Given an array `nums` of n integers, are there elements $a, b, c$ in nums such that $a + b + c = 0$? Find all unique triplets in the array which gives the sum of zero. Notice that the solution set must not contain duplicate triplets.
- 双指针解法
```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> tmp;
        int len = nums.size();
        if (len == 0){
            return res;
        }
        sort(nums.begin(), nums.end()); // 去重的前提
        for (int i=0; i < len; i++){
            if (i && nums[i] == nums[i-1]){
                continue; // 去重
            }
            int left = i+1, right = len-1;
            while(left < right){
                // 类似二分查找的思想
                if (nums[i] + nums[left] + nums[right] == 0){
                    res.push_back({nums[i], nums[left], nums[right]});
                    left++;
                    right--;
                    while (left < right && nums[left] == nums[left-1]){
                        left++;
                    }
                    while (left < right && nums[right] == nums[right+1]){
                        right--;
                    }
                }
                else if (nums[i] + nums[left] + nums[right] > 0){
                    right--;
                }
                else{
                    left++;
                }
            }
        }
        return res;
    }
};
```
---
### [41. First Missing Positive](https://leetcode-cn.com/problems/first-missing-positive/)
> Given an unsorted integer array nums, find the smallest missing positive integer.
> ```
> Example:
> Input: nums = [1,2,0]
> Output: 3
> ```
- 把1放在`nums[0]`，2放在`nums[1]`，...（即如果`nums[i] > 0`, `nums[i]` 在`nums[nums[i]-1]`）
```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for (int i=0; i < n; i++){
            while (nums[i] > 0 && nums[i] <= n && nums[i] != nums[nums[i]-1]){
            //while (nums[i] > 0 && nums[i] <= n && i != nums[i]-1){
            // 上面写法会死循环
                swap(nums[i], nums[nums[i]-1]);
            }
        }
        for (int i=0; i < n; i++){
            if (nums[i] != i+1){
                return i+1;
            }
        }
        return n+1;
    }
};
```