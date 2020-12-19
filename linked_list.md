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

### [61. Rotate List](https://leetcode.com/problems/rotate-list/submissions/)

```
Given the head of a linked list, rotate the list to the right by k places.

Example 1:

Input: head = [1,2,3,4,5], k = 2
Output: [4,5,1,2,3]
```
- 链表指针操作军训
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
    ListNode* rotateRight(ListNode* head, int k) {
        if (head == NULL){
            return NULL;
        }
        // get length of linked list
        int n=0;
        ListNode* cur = head;
        while(cur){
            n++;
            cur = cur->next;
        }
        k = k % n;
        ListNode* fast = head;
        ListNode* slow = head;
        for ( int i=0; i < k; i++){
            if (fast->next != NULL){
                fast = fast->next;
            }
        }
        if (fast == NULL){
            return NULL;
        }
        while(fast->next){
            fast = fast->next;
            slow = slow->next;
        }
        fast->next = head;
        ListNode* newHead = slow->next;
        slow->next = NULL;
        return newHead;
    }
};
```

### [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)
```
Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes, in the end, should remain as it is.

Follow up:

Could you solve the problem in O(1) extra memory space?
You may not alter the values in the list's nodes, only nodes itself may be changed.
```
- 每隔n个节点反转一下，注意链表操作
- `for( int i=1; i < k; i++)` 这段循环意思是把接下来的`k`个节点反转之后接到`pre->next`上面
```
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if ( k==1 || head == NULL || head->next == NULL){
            return head;
        }
        ListNode* fakeHead = new ListNode(-1);
        fakeHead->next = head;
        ListNode* pre = fakeHead, *cur = pre;
        int num=0; // 链表长度
        while(cur->next){
            num += 1;
            cur = cur->next;
        }
        while( num >= k){
            cur = pre->next;
            for( int i=1; i < k; i++){
                // 链表原地反转操作，需要牢记
                ListNode* t = cur->next;
                cur->next = t->next;
                t->next = pre->next;
                pre->next = t;
            }
            pre = cur;
            num -= k;
        }
        return fakeHead->next;
    }
};
```

### [143. Reorder List](https://leetcode.com/problems/reorder-list/)

> Given a singly linked list L: `L0→L1→…→Ln-1→Ln`,
> 
> reorder it to: `L0→Ln→L1→Ln-1→L2→Ln-2→…`
> 
> You may not modify the values in the list's nodes, only nodes itself may be changed.

```c++
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
    void reorderList(ListNode* head) {
        if (head == NULL || !head->next || !head->next->next ){
            return; // 特殊输入判断
        }
        // step1: 快慢指针找链表中点
        ListNode *fast = head, *slow = head;
        while(fast->next && fast->next->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode* mid = slow->next;
        // step2: 从mid断开，后半部分链表反转
        slow->next = NULL;
        ListNode* last = mid, *pre = NULL;
        while(last){
            ListNode* next = last->next;
            last->next = pre;
            pre = last; // pre最后是后半部分链表的头
            last = next;
        }
        // step3: 把后半部分穿插到前半部分链表
        while(head && pre){
            ListNode* next = head->next;
            head->next = pre;
            pre = pre->next;
            head->next->next = next;
            head = next;
        }
        
    }
};
```
