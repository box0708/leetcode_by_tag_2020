# 贪心算法
## [55. Jump Game ???](https://leetcode.com/problems/jump-game/)
> Given an array of non-negative integers, you are initially positioned at the first index of the array.
> 
> Each element in the array represents your maximum jump length at that position.
> 
> Determine if you are able to reach the last index.
```
Example 1:

Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
Example 2:

Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.
```
- 只关心能不能到最右边，考虑贪心
- 边界条件
```
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int reach = 0; // 当前能跳到的最远距离
        for ( int i=0; i < nums.size(); i++){
            if ( i > reach || reach >= nums.size()-1){
                // i > reach 后，后面不需要考虑了（肯定到不了）
                // 边界条件控制
                break;
            }
            reach = max( reach, i + nums[i]);
        }
        return reach >= nums.size()-1;
    }
};
```
---
## [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)

```
Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

Example:

Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2.
    Jump 1 step from index 0 to 1, then 3 steps to the last index.
```
- 整个贪心循环其实是在挨个贪当前位置的下一个，和其能跳到的最远距离。所以cur_begin和cur_end更新语句不可以调换顺序
```
class Solution {
public:
    int jump(vector<int>& nums) {
        int res=0;
        int cur_begin=0, cur_end=0, reach=0;
        while ( cur_end < nums.size()-1){
            for ( int i = cur_begin; i <= cur_end; i++){
                reach = max(reach, i + nums[i]);
            }
            res += 1;
            cur_begin = cur_end+1;
            cur_end = reach;
            
        }
        return res;
        
    }
};
```
---
### [134. Gas Station 加油站问题](https://leetcode-cn.com/problems/gas-station/)
> here are `n` gas stations along a circular route, where the amount of gas at the ith station is `gas[i]`.
> 
> You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from the $i^{th}$ station to its next $(i + 1)^{th}$ station. You begin the journey with an empty tank at one of the gas stations.
> 
> Given two integer arrays gas and cost, return the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return -1. If there exists a solution, it is guaranteed to be unique
- 初始思路想复杂了
- > 这道转圈加油问题不算很难，只要想通其中的原理就很简单。**我们首先要知道能走完整个环的前提是gas的总量要大于cost的总量**，这样才会有起点的存在。假设开始设置起点start = 0, 并从这里出发，如果当前的gas值大于cost值，就可以继续前进，此时到下一个站点，剩余的gas加上当前的gas再减去cost，看是否大于0，若大于0，则继续前进。当到达某一站点时，若这个值小于0了，则说明从起点到这个点中间的任何一个点都不能作为起点，则把起点设为下一个点，继续遍历。当遍历完整个环时，当前保存的起点即为所求。
```c++
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int total=0, tank=0, start=0;
        for (int i=0; i < gas.size(); i++){
            // 我们首先要知道能走完整个环的前提是gas的总量要大于cost的总量，全局
            total = total + gas[i] - cost[i];
            // 当前油箱余量
            tank = tank + gas[i] - cost[i];
            if (tank < 0){
                // 余量<0，之前所有点都不能作为起点
                start = i+1;
                tank=0;
            }
        }
        if (total < 0){
            return -1;
        }
        return start;
    }
};
```