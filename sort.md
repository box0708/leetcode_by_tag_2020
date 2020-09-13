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
---
# [56. Merge Intervals](https://leetcode.com/problems/insert-interval/)

> Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).
>
> You may assume that the intervals were initially sorted according to their start times.
>
> Example 1:
>
> Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
> Output: [[1,5],[6,9]]
> Example 2:
> 
> Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
> Output: [[1,2],[3,10],[12,16]]
> Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

- coding细节
- newInterval始终指向要处理的下一个元素

···
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        //if (intervals == NULL || newInterval == NULL){
        //    return intervals;
        //}
        
        vector<vector<int>> result;
        if (intervals.size() == 0){
            result.push_back(newInterval);
            return result;
        }
        
        
        for ( int i=0; i<intervals.size(); i++){
            vector<int> cur = intervals[i];
            if ( cur[1] < newInterval[0]){
                result.push_back(cur);
            }
            else if ( cur[0] > newInterval[1]){
                result.push_back(newInterval);
                newInterval = cur;
            }
            else{
                newInterval[0] = min(cur[0], newInterval[0]);
                newInterval[1] = max(cur[1], newInterval[1]);
            }
            
        }
        result.push_back(newInterval);
        return result;
    }
};
···


