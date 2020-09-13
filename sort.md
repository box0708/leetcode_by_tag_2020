# [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

>Given a collection of intervals, merge all overlapping intervals.
>
>Example 1:
>
>Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
>Output: [[1,6],[8,10],[15,18]]
>Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
>
>Example 2:
>
>Input: intervals = [[1,4],[4,5]]
>Output: [[1,5]]
>Explanation: Intervals [1,4] and [4,5] are considered overlapping.

- 先对输入排序
- vector最后一个元素携带信息

```
class Solution {
public:
    
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> result;
        if ( intervals.size() == 0){
            return result;
        }
        
        stable_sort(intervals.begin(), intervals.end());
        
        for ( int i=0; i<intervals.size(); i++){
            if (result.empty()){
                result.push_back(intervals[i]);
                continue;
            }
            
            int cur_left = intervals[i][0];
            int cur_right = intervals[i][1];
            int last_right = result.back()[1];
            
            if (cur_left > last_right){
                result.push_back(intervals[i]);
            }
            else{
                result.back()[1] = max(last_right, cur_right);
            }
            
        }
        return result;
        
    }
};
```
