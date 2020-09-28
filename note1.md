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
