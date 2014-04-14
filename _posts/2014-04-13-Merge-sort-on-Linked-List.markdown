---
layout: post
title:  "Merge sort on Linked List using O(1) space"
date:   2014-04-13 20:33:17
categories: algorithm
---
For online judge, see [leetcode-sort-list].

{% highlight cpp linenos %}
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *sortList(ListNode *head) {
        int len = listLen(head);
        if(len < 2) return head;
        
        ListNode dummyHead(0);
        auto p = &dummyHead;
        p->next = head;
        for(int subLen = 1; subLen < len; subLen *= 2){
            p = &dummyHead;
            ListNode *nextElem = p->next;
            while(nextElem){
                auto sub1 = cutSubList(nextElem, subLen, nextElem);
                auto sub2 = cutSubList(nextElem, subLen, nextElem);
                ListNode *lastNode;
                p->next = mergeList(sub1, sub2, lastNode);
                p = lastNode;
            }
        }
        return dummyHead.next;
    }
private:
    int listLen(ListNode *head){
        int len = 0;
        while(head){
            ++len;
            head = head->next;
        }
        return len;
    }
    
    ListNode *cutSubList(ListNode *list, int len, ListNode* &nextElem){
        if(len == 0) return NULL;
        
        auto head = list;
        auto p = list;
        while(--len && p){
            p = p->next;
        }
        
        if(p) {
            nextElem = p->next;
            p->next = NULL;
        }
        else{
            nextElem = NULL;
        }
        return head;
    }
    
    ListNode *mergeList(ListNode *la, ListNode *lb, ListNode* &lastNode){
        ListNode dummyHead(0);
        auto p = &dummyHead;
        while(la || lb){
            if(la && lb){
                if(la->val <= lb->val){
                    p->next = la;
                    la = la->next;
                }
                else{
                    p->next = lb;
                    lb = lb->next;
                }
            }
            else if(la){
                p->next = la;
                la = la->next;
            }
            else{
                p->next = lb;
                lb = lb->next;
            }
            p = p->next;
        }
        lastNode = p;
        return dummyHead.next;
    }
};
{% endhighlight %}


[leetcode-sort-list]: http://oj.leetcode.com/problems/sort-list/
