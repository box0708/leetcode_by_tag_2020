# 链表题

### [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
> Merge two sorted linked lists and return it as a new sorted list. The new list should be made by splicing together the nodes of the first two lists.
- 链表/指针基本操作
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if ( l1==NULL && l2==NULL){
            return NULL;
        }
        if ( l1==NULL){
            return l2;
        }
        if ( l2==NULL){
            return l1;
        }
        
        
        ListNode* newHead = new ListNode(-1);
        ListNode* cur = newHead;
        while( l1 != NULL && l2 != NULL){
            if ( l1->val <= l2->val){
                cur->next = l1;
                l1 = l1->next;
            }
            else{
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        if (l1 == NULL){
            cur->next = l2;
            return newHead->next;
        }
        else{
            cur->next = l1;
            return newHead->next;
        }
        
    }
};
```

### [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
```
You are given an array of k linked-lists lists, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

Example 1:

Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```
- [参考资料](https://www.cnblogs.com/grandyang/p/4606710.html)
- 解法1:分治思路
  - > 简单来说就是不停的对半划分，比如k个链表先划分为合并两个 k/2 个链表的任务，再不停的往下划分，直到划分成只有一个或两个链表的任务，开始合并。举个例子来说比如合并6个链表，那么按照分治法，首先分别合并0和3，1和4，2和5。这样下一次只需合并3个链表，再合并1和3，最后和2合并就可以了
- `k=(N+1)/2`，避免`lists`长度为奇偶产生的影响
- 直观解释：每次把`lists`长度缩减一半。`k`用意为在后半段选一个链表出来
```
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.size() < 1){
            return NULL;
        }
        int n = lists.size();
        while ( n > 1){
            int k = (n+1)/2;
            for ( int i=0; i < n/2; i++){
                lists[i] = mergeTwo(lists[i], lists[i+k]);
            }
            n = k;
        }
        return lists[0];
    }
    ListNode* mergeTwo(ListNode* p1, ListNode* p2){
        ListNode* newHead = new ListNode(-1);
        ListNode* cur = newHead;
        while ( p1 && p2){
            if (p1->val <= p2->val){
                cur->next = p1;
                p1 = p1->next;
            }
            else{
                cur->next = p2;
                p2 = p2->next;
            }
            cur = cur->next;
        }
        if (p1){
            cur->next = p1;
            return newHead->next;
        }
        else{
            cur->next = p2;
            return newHead->next;
        }
    }
};
```
- 解法2:堆排序(todo)