# 두 정렬 리스트의 병합

LeetCode 21. Merge Two Sorted Lists

## 재귀 구조로 연결

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        
        if (l1 == null || l2 !=null && l1.val > l2.val) {
            ListNode temp = l1;
            l1 = l2;
            l2 = temp;
        }
        
        if (l1 != null) {
            l1.next = mergeTwoLists(l1.next, l2);
        }
        return l1;
    }
}
```
