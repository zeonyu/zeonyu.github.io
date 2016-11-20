---
title: 【链表】
tags: []
notebook: 0. 精华整理贴
---

<div markdown="1" style=";">

前言： 主要内容包括：数组、链表、双指针、哈希表

# 2\. LinkedList

leetcode上有10个easy难度的题：

  * Palindrome Linked List
  * Remove Nth Node From End of List
  * Merge Two Sorted Lists
  * Linked List Cycle
  * Remove Duplicates from Sorted List
  * Remove Linked List Elements
  * Reverse Linked List
  * Intersection of Two Linked Lists
  * Swap Nodes in Pairs
  * Delete Node in a Linked List

类型比较单一，因为链表是非常简单的数据结构，只有next和val的参数。

## 2.1 题目解题思路+code

### Palindrome Linked List - *LinkedList TwoPointers* \- !!!!!!!!!!!!!!不懂
    
    
    Given a singly linked list, determine if it is a palindrome.
    
    Follow up:
    Could you do it in O(n) time and O(1) space?
    

思路一： 判断回文，涉及到链表的反转，拆分，要找到中点，然后比较前后两个链表是否一样，很复杂。里面有两个重要的操作，链表反转，拆分，链表的比较，好好学习。

code：
    
    
    public class Solution {
        /**
         * @param head a ListNode
         * @return a boolean
         */
        public boolean isPalindrome(ListNode head) {
            if (head == null) {
                return true;
            }
    
            ListNode middle = findMiddle(head);
            middle.next = reverse(middle.next);
    
            ListNode p1 = head, p2 = middle.next;
            while (p1 != null && p2 != null && p1.val == p2.val) {
                p1 = p1.next;
                p2 = p2.next;
            }
    
            return p2 == null;
        }
    
        ListNode findMiddle(ListNode head) {
            if (head == null) return null;
            ListNode slow = head, fast = head.next;
            while (fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
            }
            return slow;
        }
    
        private ListNode reverse(ListNode head) {
            ListNode prev = null;
    
            while (head != null) {
                ListNode temp = head.next;
                head.next = prev;
                prev = head;
                head = temp;
            }
            return prev;
        }
    }
    

思路二： O(n) time and O(1) space

将这个链表转化为数组，然后判断数组回文。
    
    
    class Solution {
    public:
        /**
         * @param head a ListNode
         * @return a boolean
         */
        bool isPalindrome(ListNode* head) {
            // Write your code here
            vector<int> v;
            while(head) {
                v.push_back(head->val);
                head = head->next;
            }
            for(int i = 0, j = v.size()-1; i < j; i ++, j --) {
                if(v[i] != v[j])
                    return false;
            }
            return true;
        }
    };
    

思路三：

参考：http://www.cnblogs.com/grandyang/p/4635425.html

这道题让我们判断一个链表是否为回文链表，LeetCode中关于回文串的题共有六道，除了这道，其他的五道为 Palindrome Number 验证回文数字， Validate Palindrome 验证回文字符串， Palindrome Partitioning 拆分回文串，Palindrome Partitioning II 拆分回文串之二 和 Longest Palindromic Substring 最长回文串.链表比字符串难的地方就在于不能通过坐标来直接访问，而只能从头开始遍历到某个位置。那么根据回文串的特点，我们需要比较对应位置的值是否相等，那么我们首先需要找到链表的中点，这个可以用快慢指针来实现，使用方法可以参见之前的两篇Convert Sorted List to Binary Search Tree 将有序链表转为二叉搜索树 和 Reorder List 链表重排序，我们使用快慢指针找中点的原理是fast和slow两个指针，每次快指针走两步，慢指针走一步，等快指针走完时，慢指针的位置就是中点。我们还需要用栈，每次慢指针走一步，都把值存入栈中，等到达中点时，链表的前半段都存入栈中了，由于栈的后进先出的性质，就可以和后半段链表按照回文对应的顺序比较了。代码如下：
    
    
    class Solution {
    public:
        bool isPalindrome(ListNode* head) {
            if (!head || !head->next) return true;
            ListNode *slow = head, *fast = head;
            stack<int> s;
            s.push(head->val);
            while (fast->next && fast->next->next) {
                slow = slow->next;
                fast = fast->next->next;
                s.push(slow->val);
            }
            if (!fast->next) s.pop();
            while (slow->next) {
                slow = slow->next;
                int tmp = s.top(); s.pop();
                if (tmp != slow->val) return false;
            }
            return true;
        }
    };
    

### Remove Remove Nth Node From End of - *LinkedList TwoPointers*
    
    
    Given a linked list, remove the nth node from the end of list and return its head.
    
    For example,
    
       Given linked list: 1->2->3->4->5, and n = 2.
    
       After removing the second node from the end, the linked list becomes 1->2->3->5.
    

思路一：暴力算法 先写一个函数，是当前节点向后n步的节点，然后当n-1非空，而n为nullptr的时候，这个节点就是要删除的节点。

思路二：参考 双指针，一个指针先移动n步，当其为nullptr的时候，前面的指针就是要删除的节点。但是由于想要删除一个节点，我们应该返回的是他的前一个节点，因此把prev和post的间距为n+1

出错了，原因是如果删除的元素是head，那么没有办法进行，runtime error。解决办法：加入dummy head。

code：
    
    
    class Solution {
    public:
        ListNode* removeNthFromEnd(ListNode* head, int n) {
            //if(n <= 0 ) return head;
    
            ListNode* dummy = new ListNode(0);
            dummy->next = head;
    
            ListNode* post = head;
            ListNode* prev = dummy;
            for(int i = 0; i < n; i ++) { // wrong point1: 是n而不是n-1，因为在n次操作过程中，都没有为空
                if(post == nullptr) return nullptr; // wrong point2: 如果n大于链表的长度，则post会有剩余空位的nullptr，这样下一步直接删除了头，符合题目要求，不需要考虑n越界的情况
                post = post->next;
            }
            while(post) {
                prev = prev->next;
                post = post->next;
            }
            prev->next = prev->next->next;
            return dummy->next;
        }
    };
    

key： 1. wrong1中，是n而不是n-1；2. wrong2，如果n的大小超过链表的长度，则在post的空指针数量堆积，结果还是删除的是头结点，符合题目测试样例的要求；3. 可以跟面试官提到n的思考过程；4. 链表的操作很多时候可以在头前面加一个dummy，非常实用；5. 双指针法，不仅仅在数组，在这里也是一样的，融会贯通；6. 对于new的理解：如果我们定义一个指针指向一个节点，那么首先这个对象是存在的，而如果dummy并不存在，我们就需要在内存中申明一个，使用 ListNode dummy = new ListNode(0) 来建立一个对象；而如果我写的是 ListNode* dummy = new ListNode(0) *则代表在new出一个对象后，再分配一个指向其的指针；7. 链表的操作很少有 node->next->next 跨越两个的操作，一般都是一个next；

### Merge Two Sorted Lists
    
    
    Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.
    

思路一：

设置一个dummy head 比如 cur，cury->next 指向L1和L2中较小的那个，同时移动cur和L指针。一旦有一个链表为空，则cur->next指向没有结束的那个。

</div>

  


code:

<div markdown="1" style="-en-codeblock: true; box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, "Courier New", monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.14902); background-position: initial initial; background-repeat: initial initial;">

class Solution {

public:

ListNode* mergeTwoLists(ListNode* l1, ListNode* l2 ) {

if(!l1) return l2;

if(!l2) return l1;

ListNode dummy(0);// need to new an object, otherwise it's null and can't find next

ListNode* cur = &dummy;

while(l1 != nullptr && l2 != nullptr) {

cur->next = l1->val > l2->val ? l2 : l1;

cur = cur->next;

if(cur == l1) l1 = l1->next;

else if(cur == l2) l2 = l2->next;

}

cur->next = (l1==nullptr) ? l2 : l1;

return dummy.next;

}

};

</div>

  


  


可以看出，效率并不高，但是主流的算法都使用这个，没有找到更好的：

打败了9.37%

<div markdown="1" style=";">

犯错反思：1. 没有考虑到链表为空的情况；2. dummy的是否只是需要一个指针还是需要对象的本体，如果只是需要指针，那么只要定义 ListNode* cur = new ListNode(0) *就可以，那么后面return的时候就没法return cur.next ，因为cur是会移动的，这个时候早就不知道到什么后面的地方去了；而如果我到时候需要return这个对象的下一个对象的指针，就可以定义 ListNode dummy(0)， ListNode* cur = &dummy *就可以即使用指针，又使用对象；3. 判断条件的 while(l1 != nullptr && l2 != nullptr) 也是很容易出错的，这么写会出现其中一个链表结束后，另一个链表的后面部分就都丢失了，只有同时为空才是终止条件，应该改为：while(l1 != nullptr && l2 != nullptr)，后面再续上； 4. 注意当某个指针变成空指针，对其val和next操作，就会报内存错误，Runtime Error大部分是内存错误；5. merge sort模板，对于数组同样适用；5. 该过程模拟归并排序，一个一个比大小则为归并排序。

思路二：递归

比较两个链表的头，较小的一个作为返回值，比如如果 l1->val 较小，return l1，l1->next 指向后面递归的结果，后面的递归就是 l1->next 和 l2 的mergeTwoLists。
    
    
    class Solution {
    public:
        ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
            if(l1 == nullptr) return l2;
            if(l2 == nullptr) return l1;
    
            if(l1->val < l2->val) {
                l1->next = mergeTwoLists(l1->next, l2);
                return l1;
            } else {
                l2->next = mergeTwoLists(l2->next, l1);
                return l2;
            }
        }
    };
    

可以看出，效率仍然不高，主流的算法都是这个，不必过于追求效率细节：

http://i4.piimg.com/567571/896342fd70795a57.png

### Linked List Cycle
    
    
    Given a linked list, determine if it has a cycle in it.
    
    Follow up:
    Can you solve it without using extra space?
    
    

思路一： 循环链表的特点是头尾连接，所以的元素都可以遍历到，那么不断的循环，总可以回到原点，所以一个指针一直++，直到回到head，但是超时，错误的原因是，循环链表不是一定要从head循环的啊，如果不是从head开始循环，那么就会在后面的循环中一直重复，但是却找不到与head相等的，这就是这题的陷阱！如果设计的是动态的slow，那么不管是在哪里循环，总会动态的移动到那个循环中，然后才能用判断。

code： `class Solution { public: bool hasCycle(ListNode *head) { if(!head) return false; ListNode* cur = head->next; while(cur != nullptr && cur->next != nullptr) { if(head == cur) return true; cur = cur->next; } return false; } };`

思路二：参考

如何遍历所以元素，那就是一个fast，一个slow，fast一次走两步，slow一次走一步，总会相遇。

### Remove Duplicates from Sorted List
    
    
    Given a sorted linked list, delete all duplicates such that each element appear only once.
    
    For example,
    Given 1->1->2, return 1->2.
    Given 1->1->2->3->3, return 1->2->3
    

思路一：

跳过所有重复的元素。

code：
    
    
    class Solution {
    public:
        ListNode* deleteDuplicates(ListNode* head) {
            if(head == nullptr || head->next == nullptr) return head;
            ListNode* cur = head;
            while(cur->next != nullptr) {
                if(cur->val == cur->next->val) {
                    cur->next = cur->next->next;
                } else cur = cur->next;
            }
            return head;
        }
    };
    

key：1. 思维定势，线性思维，按照人的思路，有相同的，往后跳，然而每一步的判断条件却很混乱，比如，看到1 1 2 3 3，第二个1跳过，第二个3跳过，甚至考虑的是，如果有多个，比如1 1 1 1 2，那么要一次性跳过所有的1，直到不是1为止，这样的trick并不明智，你以为你在省时间，其实把程序的整个思路都打乱了，不如一步一步的来，注意每一步的判断条件：如果cur->next->val== cur->val，cur->next = cur->next->next 实现往后移动一格，注意是一格！执行一个循环，然后下一循环，如果还是相同，同样跳一格，如果不相同，则cur=cur->next 完成cur的移动，以便后面的判断和操作。反例：
    
    
    class Solution {
    public:
        ListNode* deleteDuplicates(ListNode* head) {
            if(head == nullptr || head->next == nullptr) return head;
            else {
                ListNode* cur = head;
                while(cur->next != nullptr) {
                    while(cur->val == cur->next->val) {
                       cur->next = cur->next->next; //试图一次完成多步的操作，逻辑上没有发现错误（一定是有的），但是至少显得很不直观
                    }
                }
                cur = cur->next; //完全是线性的思维，条件判断呢？直接就来cur后移，依据是什么?
            }
    
            return head;
        }
    };
    

### Remove Linked List Elements
    
    
    Remove all elements from a linked list of integers that have value val.
    
    Example
    Given: 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6
    Return: 1 --> 2 --> 3 --> 4 --> 5
    

竟然一次过！

思路一： dummy节点，如果next存在，并且值为val则跳过，否则后延。不会出现nullptr->next的问题。重点在于dummy head
    
    
    class Solution {
    public:
        ListNode* removeElements(ListNode* head, int val) {
            if(!head) return nullptr;
            ListNode dummy(0);
            ListNode* cur = &dummy;
            cur->next = head;
            while(cur->next) {
                if(cur->next->val == val) cur->next = cur->next->next;
                else cur = cur->next;
            }
            return dummy.next;
        }
    };
    

### Reverse Linked List

题有两个思路，一个是我的迭代，还有一个是递归。

</div>

  


迭代的方法比较直接，是我所掌握的，但是细节上总是出一些问题，比如这里第一次写的就出现了runtime error，没有得出答案；而另一个方法 递归，是我没有想到的，递归的思想我还没有理解透，总是想不到。今天不想写，先放一下。

  


post的功能是锁存住后面一个节点，因为cur指向前面后，next就找不到了。而且每个循环的最后两步移动都只是cur和pre向后移，没有移动post，post是在while循环开始的时候进行的

<div markdown="1" style=";">

参考答案：http://www.jiuzhang.com/solutions/reverse-linked-list/
    
    
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            if(!head) return head;
            ListNode* cur = head;
            ListNode* pre = nullptr;
            ListNode* post = head->next;
            while(cur != nullptr) {
                post = cur->next;
                cur->next = pre;
                pre = cur;
                cur = post;
            }
            return pre;
        }
    };
    
    

心得：1. 第二遍已经搞定了，但还是犯了一点小错误，那就是post = cur->next 放在了最后，这样会导致post指向prev，从而出错；即使改为 post = post->next 还是不行（Q：不理解，因为post没有改变过 A：其实并不是，这个时候post虽然没有改变，但是post有可能是nullptr，并没有next）。重点：：：链表的next的next的两次next操作是非常错误的方式，记住，如果要对while判断条件满足的那个条件，就是cur非空，这个时候一个循环中只能对cur的next进行操作，作用域不同。

### Intersection of Two Linked Lists - ？？？？？？？？？？？？
    
    
    Write a program to find the node at which the intersection of two singly linked lists begins.
    
    
    For example, the following two linked lists:
    
    A:          a1 → a2
                       ↘
                         c1 → c2 → c3
                       ↗            
    B:     b1 → b2 → b3
    begin to intersect at node c1.
    
    Notes:
    
    If the two linked lists have no intersection at all, return null.
    The linked lists must retain their original structure after the function returns.
    You may assume there are no cycles anywhere in the entire linked structure.
    Your code should preferably run in O(n) time and use only O(1) memory.
    
    

题目意思是一旦找到一个元素相等，则退出，这个操作可能存在纰漏，比如：
    
    
    A:          a1 → a2
                       ↘
                         c1 → c2 → c3
                       ↗            
    B:     b1 → a1 → b3
    

如何解释题意，我还没有想明白。

思路一：暴力算法

遍历 A 中的元素，每一个元素都在 B 中查找，一旦找到第一个相等的节点则退出？

思路二：哈希表/数组

转换思路，链表在对元素的操作上存在局限（只能操作后一个），因此可以把元素转存在哈希表或者数组中，然后对其中的元素进行操作。这样的好处在于解决了链表操作元素不方便的问题，但是引入了新的数据结构，空间复杂度太高，所以一般不会使用。

此处以哈希表为例，使用有顺序的map，将 A 的元素存在哈希表，然后对 B 的元素逐一在 A 中查找，找到之后再比较后面的是否相等，一直到 B 的结束，那么返回true。

空间开销更大的方法，两个链表都转为数组，那就直接比较数据的元素是否有重叠就可以了。

思路三：双指针法：时间复杂度O(lengthA+lengthB)，空间复杂度O(1)

长的那个链表头后延至两个链表的长度一致，然后逐一比较元素，一旦出现相同，则返回。这个思路的一个缺陷就是如果链表是 [1,2,3,4] and [6,2,6,7]，这里检测到2之后就返回。第一次出现的相同的元素不代表后面也相同，只有检测到后面所有的元素都相同才可以。

算了，这种逻辑的代码不会写。
    
    
    class Solution {
    public:
        ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
            //step0: pre-processing
            if(headA == nullptr || headB == nullptr) return nullptr;
    
            //step1: get the length of two linked list
            int len1=1, len2=1;
            ListNode *l1 = headA, *l2 = headB;
            while(l1->next != nullptr) {
                l1 = l1->next;
                len1++;
            }
            while(l2->next != nullptr) { // wrong1: l2 != nullptr
                l2 = l2->next;
                len2++;
            }
            if(l1 != l2) return nullptr;
    
            //step2: move l2-l1 steps for l1 or on contrary
            if(len1 > len2) {
                for(int i = 0; i < (len1-len2); i++) {
                    headA = headA->next;
                }
            }
            if(len2 > len1) {
                for(int i = 0; i < (len2-len1); i++) {
                    headB = headB->next;
                }
            }
            while(headA != headB) {
                headA = headA->next;
                headB = headB->next;
            }
            return headA;
        }
    };
    

key: 1. 竟然把len1写成了l1，注意区分清楚那个变量代表什么意思，都是一些stupid的错误；2. 题目意思是一旦找到一个元素相等，则退出，这个操作可能存在纰漏

非常假，这次的效率竟然这么高，超出想象。。。。
<en-media hash="f9163b4f053b3db34c9bf45ee64a8be8" style="height: auto;" type="image/png"/>

### Swap Nodes in Pairs
    
    
    Given a linked list, swap every two adjacent nodes and return its head.
    
    For example,
    Given 1->2->3->4, you should return the list as 2->1->4->3.
    
    Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.
    

思路一： 操作节点

code： `class Solution { public: ListNode* swapPairs(ListNode* head) { if(!head || head->next == nullptr) return head; ListNode dummy(0); ListNode* cur = &dummy; cur->next = head; while(cur->next != nullptr && cur->next->next != nullptr) { // swap two nodes ListNode* pre = cur->next; ListNode* post = cur->next->next; pre->next = post->next; cur->next = post; post->next = pre; // move pointer cur = pre; } return dummy.next; } };`

效率不高，打败了3.64%，但是大部分都在这个水平。
<en-media hash="5558750a70cebd404b1c0502933a0607" style="height: auto;" type="image/png"/>

思路二：

操作val，直接交换两个 node 的 val，而不是交换节点。

code:
    
    
    class Solution {
    public:
        ListNode* swapPairs(ListNode* head) {
            if(!head || head->next == nullptr) return head;
            ListNode dummy(0);
            ListNode* cur = &dummy;
            cur->next = head;
            while(cur->next != nullptr && cur->next->next != nullptr) {
                // swap two vals
                int tmp = cur->next->val;
                cur->next->val = cur->next->next->val;
                cur->next->next->val = tmp;
                // move pointer
                cur = cur->next->next;
            }
            return dummy.next;
        }
    };
    

效率不变：
<en-media hash="5558750a70cebd404b1c0502933a0607" style="height: auto;" type="image/png"/>

BTW，九章算法的解法也是这个效率，所以不用过于追求，这个效率已经够用。

### Delete Node in a Linked List
    
    
    Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.
    
    Supposed the linked list is 1 -> 2 -> 3 -> 4 and you are given the third node with value 3, the linked list should become 1 -> 2 -> 4 after calling your function.
    

思路：

该题目太简单，就是已经给定了一个 node，但不是跳过后一个节点，而是把后一个节点的 val 复制过来，然后在跳过下一个 node。这个的操作是对本节点进行操作，而我们普遍使用的是对下一个节点进行操作。

code： `class Solution { public: void deleteNode(ListNode* node) { if(node == NULL) return; node->val = node->next->val; node->next = node->next->next; return; } };`

## 2.2 题目解题思路+code

</div>
