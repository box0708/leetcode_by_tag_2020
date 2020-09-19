# 滑动窗口类题目

## [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
> You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position.
>
> Return the max sliding window.
> Example 1:
> 
> Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
> Output: [3,3,5,5,6,7]

返回滑动窗口最大值数组
- 注意res数组大小
- 中心思想：双端队列头始终是当前窗口最大值
- 遍历过程中，当前数比队尾大就一直弹（保存小的没意义）
- 边界
- trick：双端队列存数组下标

···
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        if (nums.size() == 0 || k==0 || k > nums.size()){
            vector<int> res; // 异常保护
            return res;
        }
        vector<int> res(nums.size()-k+1, 0); // res数组，自定义好size
        deque<int> helper; // 辅助：双端队列
        
        int cur_index=0;
        for ( int i=0; i < nums.size(); i++){
            while(!helper.empty() && nums[helper.back()] <= nums[i]){
            /*
              双端队列不为空，且当前遍历到的数>=队尾，弹出队尾
            */
                helper.pop_back();
            }
            helper.push_back(i); // 进入双端队列
            if (helper.front() == i-k){
                helper.pop_front(); // 队列头过期，弹出队列头
            }
            if (i >= k-1){
            // 已经形成窗口，队列首的值进入res
                res[cur_index] = nums[helper.front()];
                cur_index++;
            }
        }
        return res;
    }
};
···
