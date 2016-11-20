---
title: 【树】
tags: []
notebook: 0. 精华整理贴
---

<div markdown="1" style=";">

该文档对于整理面试中常见的树的题目进行了整理，主要是从两个部分，一个是leetcode+《剑指offer》，一个是牛客网bat直通车的教程中的总结，都是一些琐碎的整理，希望有帮助。

# 1\. leetcode + 《剑指offer》

leetcode中关于树的题目有：

  * Binary Tree Level Order Traversal && Binary Tree Level Order Traversal II
  * Symmetric Tree
  * Binary Tree Paths
  * Lowest Common Ancestor of a Binary Search Tree
  * Invert Binary Tree
  * Same Tree
  * Minimum Depth of Binary Tree
  * Maximum Depth of Binary Tree
  * Path Sum
  * Balanced Binary Tree

`queue.front()` // not head() nor top() 注意区分一下

## leetcode题目解析

### Binary Tree Level Order Traversal I && II
    
    
    
    Given a binary tree, return the *level order* traversal of its nodes' values. (ie, from left to right, level by level).
    
    <div markdown="1" style="box-sizing: border-box; margin: 0px 0px 10px; color: rgb(51, 51, 51); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 1; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); font-size: 14px;">
    
    For example: 
    
    Given binary tree `[3,9,20,null,null,15,7]`,
    
    </div>
    
    
    
<en-media hash="aedb7953d7d7d93416a47755d50a7d2e" style="height: auto;" type="image/png"/>

首先，树的题目都会想到递归和非递归的思路，递归写法简洁，但是效率不如非递归的好，因为过程中会不断的调用栈空间，但是非递归也需要自己开辟新的数据结构，一般是栈、队列、数组等，而且写法不简洁。

如果是前序中序后序遍历，一般会首选递归，但是按层遍历，还是非递归比较方便，因为跟递归的思想不是太符合。

II中要求只是基于I中的结果做一次vector的反转，因此直接使用reverse就行。

思路一：非递归

使用queue，先root压入，然后queue非空，则把当前的所有放在一个vector


    
```    
    class Solution {
    
    public:
    
    vector<vector<int>> levelOrder(TreeNode* root) {
    
    vector<vector<int>> res;
    
    if(!root) return res;
    
    
    queue<TreeNode*> q;
    
    q.push(root);
    
    
    
    
    while(!q.empty()) {
    
    vector<int> tmp;
    
    int size = q.size();
    
    
    
    
    for(int i=0; i < size; i++) {
    
    TreeNode* head = q.front();q.pop();
    
    tmp.push_back(head->val);
    
    
    
    
    if(head->left) q.push(head->left);
    
    if(head->right) q.push(head->right);
    
    }
    
    
    
    
    res.push_back(tmp);
    
    }
    
    return res;
    
    }
    
    };
   ``` 
    
    

key：1. 注意，for循环之前，一定要锁存住 size，因为在for循环中会push进一些元素，这个时候下一次for循环的时候就会改变执行的次数，导致每一层元素个数增加的情况；2. reverse函数是`std::reverse(vec.begin(), vec.end());`，但是不要写成 `vec.reverse(...)`；
<en-media hash="512e041e129105577a7aa80a98fc3d37" style="height: auto;" type="image/jpeg"/>

### Symmetric Tree
<en-media hash="6c8858de1c9d2f82304c1a91944fc651" style="height: auto;" type="image/png"/>

参考（c++递归+非递归）：http://blog.csdn.net/kenden23/article/details/19249357

树的问题还是想到递归和非递归，但是这个题的递归，子树不一定是对称的，因此子问题好像不成立，想不到怎么用递归；非递归，随着树的深入，需要判断某一个节点的左子树和对称节点的右子树，不知道如何记录这两个值。

直接参考：

方法一：递归

key：1. 递归问题是可以解的，只是要判断两次，`left->left和right->right`，`left->right和right->left`，可以遍历完整棵树；2. true或false的条件判断很重要，递归和非递归都要用到同样的逻辑判断，就是那种情况下是true，哪种是false，这个也是逻辑的一个关键点；

<div markdown="1" style="color: #000000; font-family: monospace,monospace; font-size: 0.9em; border: 1px solid #cccccc; border-radius: 3px; overflow: auto; padding: 6px 10px; margin-bottom: 10px;">

`class Solution {  
  
public:  
  
`

bool isSymmetric(TreeNode* root) {

`  
`

if(!root) return true;

`  
`

return isSymmetric(root->left, root->right);

`  
}  
bool isSymmetric(TreeNode* left, TreeNode* right) {  
  
`

if(!left && !right) return true;

`  
`

if((!left && right) || (left && !right) || (left->val != right->val)) return false;

`  
`

return isSymmetric(left->left, right->right) && isSymmetric(left->right, right->left);

`  
}  
  
};  
  
`</div>

方法二：非递归

key：1. 使用两个queue或者stack，分别存储左子树和右子树的节点，q1先左后右，q2先右后左，然后对这个节点进行判断，是否满足我们的要求（省略一万字），如果处理的当前节点非空，那么如果左右孩子都为空，则不需要再放入q1，如果一空一非空或者两个都非空，那么还是两个都放进来，因为下一次判断还是需要；
    
    
    
    class Solution {
    
    public:
    
    bool isSymmetric(TreeNode* root) {
    
    if(nullptr == root) return true;
    
    queue<TreeNode*> q1,q2;
    
    q1.push(root->left);
    
    q2.push(root->right);
    
    TreeNode* left, *right;
    
    while(!q1.empty() && !q2.empty()) {
    
    left = q1.front(); q1.pop();
    
    right = q2.front(); q2.pop();
    
    // true logic or false logic: usually false
    
    // exception positions: 1.empty to non-empty; 2. !val;
    
    if(nullptr == left && nullptr == right) {
    
    continue;
    
    }
    
    if(nullptr == left || nullptr == right) {
    
    // empty to non-empty
    
    return false;
    
    }
    
    if(left->val != right->val) {
    
    return false;
    
    }
    
    // push left->left left->right and right->left right->right
    
    q1.push(left->left);
    
    q1.push(left->right);
    
    q2.push(right->right);
    
    q2.push(right->left);
    
    }
    
    return true;
    
    }
    
    };
    
    
    
<en-media hash="35ce59d7a13f0ebf773ac4d5849d9e8c" style="height: auto;" type="image/png"/>

### Binary Tree Paths
<en-media hash="bd9a9322f6c36c5d9c0decc8967f4464" style="height: auto;" type="image/png"/>

结合树的遍历，难点在于如何存储有公共头的不同序列，看看人家是如何解的：

方法一：递归

逻辑关系如图：
<en-media hash="0f3fcfe954b5bb743207431e8aa69202" style="height: auto;" type="image/jpeg"/>

key：1. 构造了一个新的函数，参数表是`（vector<string> result, TreeNode* root, string t）`，这个有什么好处？2. 易错点：参数表中的`vector<string>& result`，没有写&，导致出错，为什么？
    
    
    
    class Solution {
    
    public:
    
    void binaryTreePaths(vector<string>& result, TreeNode* root, string t) {//wrongpoint1: vector<string> result就报错了，结果为空
    
    if(nullptr == root->left && nullptr == root->right) {
    
    result.push_back(t);
    
    return;
    
    }
    
    if(root->left) binaryTreePaths(result, root->left, t + "->" \+ to_string(root->left->val));
    
    if(root->right) binaryTreePaths(result, root->right, t + "->" \+ to_string(root->right->val));
    
    }
    
    
    
    
    
    
    
    vector<string> binaryTreePaths(TreeNode* root) {
    
    vector<string> result;
    
    if(!root) return result;
    
    
    
    
    binaryTreePaths(result, root, to_string(root->val));
    
    return result;
    
    }
    
    };
    
    
    
    
    
    
<en-media hash="6a9632a123e0aee8dc90369126563726" style="height: auto;" type="image/png"/>

方法二：非递归

没想到，可以参考：http://blog.csdn.net/crazy1235/article/details/51474128

### Lowest Common Ancestor of a Binary Search Tree
<en-media hash="0d80b9b42c75f02ca7b73c9c17c7d83c" style="height: auto;" type="image/png"/>

思路一：递归

参考：非常牛逼的代码，简洁
    
    
    
    /**
    
    * Definition for a binary tree node.
    
    * struct TreeNode {
    
    * int val;
    
    * TreeNode *left;
    
    * TreeNode *right;
    
    * TreeNode(int x) : val(x), left(NULL), right(NULL) {}
    
    * };
    
    */
    
    class Solution {
    
    public:
    
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    
    if (!root || root == p || root == q) return root;
    
    TreeNode* left = lowestCommonAncestor(root->left, p, q);
    
    TreeNode* right = lowestCommonAncestor(root->right, p, q);
    
    return !left ? right : !right ? left : root;
    
    }
    
    };
    
    
    

思路二：非递归-二分

由于平衡二叉树的性质，使用二分的思想，如果一个节点大于两个节点，那么ancestor只可能在左子树中，反之则在右子树。

参考九章算法：
    
    
    
    /**
    
    * Definition of TreeNode:
    
    * class TreeNode {
    
    * public:
    
    * int val;
    
    * TreeNode *left, *right;
    
    * TreeNode(int val) {
    
    * this->val = val;
    
    * this->left = this->right = NULL;
    
    * }
    
    * }
    
    */
    
    class Solution {
    
    public:
    
    /**
    
    * @param root: The root of the binary search tree.
    
    * @param A and B: two nodes in a Binary.
    
    * @return: Return the least common ancestor(LCA) of the two nodes.
    
    */
    
    int Atop, Btop, top;
    
    TreeNode *a[100000], *b[100000], *ans[100000];
    
    bool find;
    
    void inorder(TreeNode *node, TreeNode *A, int flag) {
    
    if (find==true)
    
    return;
    
    if (node == NULL)
    
    return;
    
    ans[++top] = node;
    
    if (A == node) {
    
    find = true;
    
    if (flag == 0) {
    
    Atop = top;
    
    for (int i = 1; i <= top; ++i)
    
    a[i] = ans[i];
    
    } else {
    
    Btop = top;
    
    for (int i = 1; i <= top; ++i)
    
    b[i] = ans[i];
    
    }
    
    return;
    
    }
    
    
    
    
    inorder(node->left, A, flag);
    
    if (find) return;
    
    
    
    
    inorder(node->right, A, flag);
    
    if (find) return;
    
    
    
    
    top --;
    
    
    
    
    }
    
    TreeNode *leastCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
    
    // write your code here
    
    top = 0; find = false;
    
    inorder(root, A, 0);
    
    
    
    
    top = 0; find = false;
    
    inorder(root, B, 1);
    
    
    
    
    Atop = min(Atop, Btop);
    
    Btop = Atop;
    
    
    
    
    while (a[Atop] != b[Btop]) {
    
    Atop --;
    
    Btop --;
    
    }
    
    return a[Atop];
    
    }
    
    };
    
    
    

### Invert Binary Tree
<en-media hash="7fafaddcc08861d5bfbefc23cf1d0c25" style="height: auto;" type="image/png"/>

思路一：递归

考虑到之前的递归的考虑，可以return left = Right。

难点在于：1. 左右子树的交换需要调用递归函数；2. 结束条件的判断，就是如果是nullptr也可以返回nullptr，但是如果左右子树一个有一个没有，这个时候也可以完成交换的操作，因此可以完成递归的二叉树交换。
    
    
    
    /**
    
    * Definition for a binary tree node.
    
    * struct TreeNode {
    
    * int val;
    
    * TreeNode *left;
    
    * TreeNode *right;
    
    * TreeNode(int x) : val(x), left(NULL), right(NULL) {}
    
    * };
    
    */
    
    class Solution {
    
    public:
    
    TreeNode* invertTree(TreeNode* root) {
    
    if(root == nullptr) return nullptr;
    
    TreeNode* tmp = root->left;
    
    root->left = invertTree(node->right);
    
    root->right = invertTree(tmp);
    
    return root;
    
    }
    
    };
    
    
    
<en-media hash="79b1f1a1d8f7fc00f42efad69e5ac7b3" style="height: auto;" type="image/jpeg"/>

可以看出，递归的算法的效率是比较低的

思路二：非递归

类似二叉树遍历一样，使用queue来辅助，一旦检测到的节点的左右子树非空，则加入queue，然后pop出节点后，如果左右子树还是非空，还是加入queue。思路还是不够清晰，有几个点：1. root（即当前需要处理的节点需不需要加入queue）；2. 是否只需简单的交换左右节点就可以；3. 是先交换子节点，然后在把非空的左右子树加入queue；4. 每个循环处理树的结点还是处理queue的结点，即循环的条件；

参考了参考答案后，结论如下：1. root需要先加入queue作为初始，然后后面就不用加入当前处理的节点，因为这个节点出队列的意思就是这个节点已经处理过了；2. 是的，只需要简单的交换，因为后面的左右子树还是加入到queue，等待处理；3. 先交换，再加入左右子树，或者先加入子树在交换都是可以的；因为先交换之后其左右子树还是在queue中；4. 每个循环对queue的头结点处理；5. queue只有front和back，切记
    
    
    
    /**
    
    * Definition for a binary tree node.
    
    * struct TreeNode {
    
    * int val;
    
    * TreeNode *left;
    
    * TreeNode *right;
    
    * TreeNode(int x) : val(x), left(NULL), right(NULL) {}
    
    * };
    
    */
    
    class Solution {
    
    public:
    
    TreeNode* invertTree(TreeNode* root) {
    
    if(root == nullptr) return nullptr;
    
    queue<TreeNode*> q;
    
    q.push(root);
    
    
    
    
    while(!q.empty()) {
    
    TreeNode* node = q.front(); q.pop();
    
    TreeNode* tmp = node->left;
    
    node->left = node->right;
    
    node->right = tmp;
    
    if(node->left) q.push(node->left);
    
    if(node->right) q.push(node->right);
    
    }
    
    return root;
    
    }
    
    };
    
    
    
<en-media hash="82b035d54a686d6a6cdaf244565636c6" style="height: auto;" type="image/jpeg"/>

可见，递归的效率比较低，但是如果要用非递归则需要额外的空间

### Same Tree
    
    
    
    Given two binary trees, write a function to check if they are equal or not.
    
    <div markdown="1" style="box-sizing: border-box; margin: 0px 0px 10px; color: rgb(51, 51, 51); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 1; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); font-size: 14px;">
    
    Two binary trees are considered equal if they are structurally identical and the nodes have the same value.
    
    
    

思路一：非递归

逻辑判断，双指针，如果每个结点的left相等，right也相等，那么就进行下去，同时left=node->left；但是问题在于遍历的数据量很大，需要用queue记录下各个结点，然后再判断left和right是否相同；

思路二：递归

如果相等，那么root得相等，root->left和root->right也得是same。所以返回条件是：`return ((p->val == q->val) && isSameTree(p->left, q->left) && isSameTree(p->right, q->right));`，但是难点在于如何确定终止条件。递归的终止条件在于考虑单节点的情况，单节点有四种情况：（0,0）= true，（0,1）= false，（1,0） = false，（1,1）则其left和right都是true，那么就看val是否相等了，也就是return的部分。
    
    
    
    /**
    
    * Definition for a binary tree node.
    
    * struct TreeNode {
    
    * int val;
    
    * TreeNode *left;
    
    * TreeNode *right;
    
    * TreeNode(int x) : val(x), left(NULL), right(NULL) {}
    
    * };
    
    */
    
    class Solution {
    
    public:
    
    bool isSameTree(TreeNode* p, TreeNode* q) {
    
    if(!p && !q) return true;
    
    if((!q && p) || (q && !p)) return false;
    
    return ((p->val == q->val)
    
    && isSameTree(p->left, q->left)
    
    && isSameTree(p->right, q->right));
    
    }
    
    };
    
    
    
<en-media hash="615c348b26455040df088a7a1a01bcf4" style="height: auto;" type="image/jpeg"/>

这么写也可以：
    
    
    class Solution {
    
    public:
    
    bool isSameTree(TreeNode* p, TreeNode* q) {
    
    if (!p && !q) return true; // 终止条件
    
    else if (!p || !q) return false; // 剪枝
    
    return p->val == q->val
    
    && isSameTree(p->left, q->left)
    
    && isSameTree(p->right, q->right);
    
    }
    
    };
    
    
    

### Minimum Depth of Binary Tree
    
    
    Given a binary tree, find its minimum depth.
    
    
    The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.
    
    
    

思路一：非递归

初始条件：如果root为nullptr，或者只有一个节点，return 0

使用queue，先将root压入，然后检测是否左右孩子都是空，如果是，则return count；如果不全为空，则将左右孩子压入queue；但是cnt什么时候+1？感觉需要把每一层的节点单独存在一个queue中，每次处理一个queue，如果这一层只要有一个节点有左右孩子，那就+1，然后再下一层。

这种的按照层数来操作很不适用。下面参考一下人家的方法。

改进：实现每层的方法，压入root，当queue非空，cnt++，然后对当前的队列中的所有元素for循环处理，压入其左右节点，同时pop，实现一层一层的处理
    
    
    class Solution {
    
    public:
    
    int minDepth(TreeNode* root) {
    
    if(!root) return 0;
    
    queue<TreeNode*> q;
    
    q.push(root);
    
    int cnt = 0;
    
    while(!q.empty()) {
    
    cnt++;
    
    int n = q.size();
    
    for(int i=0; i < n; i++) {
    
    TreeNode* node = q.front();
    
    q.pop();
    
    if(!node->left && !node->right) return cnt;
    
    if(node->left) q.push(node->left);
    
    if(node->right) q.push(node->right);
    
    }
    
    }
    
    return 0;
    
    }
    
    };
    
    
    
<en-media hash="8958306c4e1c75b994af443b206faac8" style="height: auto;" type="image/jpeg"/>

思路二：递归

参考九章算法：
    
    
    
    class Solution {
    
    public:
    
    /**
    
    * @param root: The root of binary tree.
    
    * @return: An integer
    
    */
    
    int ans;
    
    int solve_dp(TreeNode *root) {
    
    if(root == NULL)
    
    return 0;
    
    
    
    
    if (root->left == NULL && root->right == NULL)
    
    return 1;
    
    
    
    
    int lf = 0x7fffffff, rt = 0x7fffffff;
    
    if(root->left)
    
    lf = solve_dp(root->left);
    
    
    
    
    if(root->right)
    
    rt = solve_dp(root->right);
    
    
    
    
    return min(lf, rt) + 1;
    
    }
    
    int minDepth(TreeNode *root) {
    
    // write your code here
    
    if (!root) return 0;
    
    return solve_dp(root);
    
    }
    
    };
    
    
    

我的算法：（错的）
    
    
    class Solution {
    
    public:
    
    int minDepth(TreeNode* root) {
    
    if(!root) return 0;
    
    if(!root->left && !root->right) return 1;
    
    int left = minDepth(root->left);
    
    int right = minDepth(root->right);
    
    return left < right ? left+1 : right+1;
    
    }
    
    };
    
    
    

Q1:有一个问题，就是如果层数多了，那么其中的中间的一个节点，返回的minDepth是取最小的，有可能左子树有10层，右孩子nullptr，那么这个节点会返回1，而不是11。这个问题怎么解决？

Q2: 为什么要重新写一个solve_dp函数？

修改第一版：
    
    
    class Solution {
    
    public:
    
    int minDepth(TreeNode* root) {
    
    if(!root) return 0;
    
    if(!root->left && !root->right) return 1;
    
    if(!root->left && root->right) {
    
    return minDepth(root->right)+1;
    
    }
    
    if(root->left && !root->right) {
    
    return minDepth(root->left)+1;
    
    }
    
    if(root->left && root->right) {
    
    int left = minDepth(root->left);
    
    int right = minDepth(root->right);
    
    return left < right ? left+1 : right+1;
    
    }
    
    return 0;
    
    }
    
    };
    
    
    

更简介的写法，把（0，1）（1，0）(1,1)问题简化了：
    
    
    class Solution {
    
    public:
    
    int minDepth(TreeNode* root) {
    
    if(!root) return 0;
    
    if(!root->left && !root->right) return 1;
    
    int left =INT_MAX, right = INT_MAX
    
    if(root->right)
    
    right = minDepth(root->right);
    
    
    
    
    if(root->left)
    
    left = minDepth(root->left);
    
    
    
    
    return min(left,right)+1;
    
    }
    
    };
    
    
    

key：1. 递归的方法，有两个要点，一个是递归的逻辑，一个是末端点的结束条件；末端点的情况有空，非空但没有孩子节点（0，0），有孩子节点（1，0）（0，1）（1，1），递归逻辑就是父节点的min=孩子的min+1；2. 有一个陷阱，就是一旦其中有一个孩子为空，那么不管另一个孩子的最短路径是多少，当前父节点的min都会是1，但是显然我们不会对一个空的节点作为最短路径，因此需要注意这个问题；该问题的解答，有版本一中的各种if条件，很繁琐，另一个就是版本二中的，只判断左孩子存在则计算left，右孩子存在则计算right，即如果有一个孩子不存在，就不在计算，由于return的时候返回的是min，因此我们的left和Right初始化的时候要初始化为INT_MAX，同理，如果return是max的时候，则需要初始化为INT_MIN；3. return可以是min函数，也可以用 a > b ? b : a 的语法；

### Maximum Depth of Binary Tree
    
    
    
    Given a binary tree, find its maximum depth.
    
    <div markdown="1" style="box-sizing: border-box; margin: 0px 0px 10px; color: rgb(51, 51, 51); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 1; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); font-size: 14px;">
    
    The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.
    
    </div>
    
    
    

思路一：

我的思路是直观的，每个节点如果有child则+1，然后对于所有的child，只要有一个还有child那就+1，同理，循环。

难点在于：如何存储所有的child，复杂度是遍历所有的节点，

思路二：递归

层数=child的层数+1
<en-media hash="343997b99964c906226cb458aead430f" style="height: auto;" type="image/jpeg"/>

### Path Sum
<en-media hash="94caf9c9f56f9c5f8e24f54d6092e13d" style="height: auto;" type="image/png"/>

思路一：递归

sum=22，root=5，那么就找左右子树，是否有sum=17的，以此类推。

但是问题在于，sum需要不断的更新，这样迭代的时候就没有办法，所以还是没有办法。需要参考。这个问题的解决办法，就是参数已经给出了，使用参数传递就可以，所以不需要再考虑。

接下来，递归两个要素，终止结束条件，递归逻辑。

<div markdown="1" style="color: #000000; font-family: monospace,monospace; font-size: 0.9em; border: 1px solid #cccccc; border-radius: 3px; overflow: auto; padding: 6px 10px; margin-bottom: 10px;">

`class Solution {`

`public:`

`bool hasPathSum(TreeNode* root, int sum) {`

` if(!root) return false;`

` if(!root->left && !root->right && root->val == sum) return true;`

` return hasPathSum(root->left, sum - root->val) || hasPathSum(root->right, sum - root->val);`

` }`

`};`

</div>

效率很高，top

思路二：非递归

遍历，计算每一条路径上的sum，直到叶子节点，判断是否为sum。如果单纯按照遍历的思路来说，前序中序后序，好像都不是很直观。其实，应该是按层遍历的思想，不断的更新累计的节点的权重，（其实也就是前缀的和一直累加的过程），直到叶子节点判断是否满足条件而已。

写代码：

边界条件：root==nullptr
    
    
    
    class Solution {
    
    public:
    
    bool hasPathSum(TreeNode* root, int sum) {
    
    if(root == nullptr) return false;
    
    vector<TreeNode*> vec;
    
    vec.push_back(root);
    
    while(!vec.empty()) {
    
    vector<TreeNode*> tmp;
    
    for(int i=0; i < vec.size(); i++) {
    
    if(vec[i]->left == nullptr && vec[i]->right == nullptr && vec[i]->val == sum) {
    
    // leaf node
    
    return true;
    
    }
    
    if(vec[i]->left) {
    
    vec[i]->left->val += vec[i]->val;
    
    tmp.push_back(vec[i]->left);
    
    }
    
    if(vec[i]->right) {
    
    vec[i]->right->val += vec[i]->val;
    
    tmp.push_back(vec[i]->right);
    
    }
    
    }
    
    vec = tmp;
    
    }
    
    return false;
    
    }
    
    };
    
    
    

效率不如递归的好，为什么。。。一般递归的效率都要更低一些才对啊
<en-media hash="07401e60842cd0b634da518adb28eae3" style="height: auto;" type="image/png"/>

### Balanced Binary Tree
    
    
    Given a binary tree, determine if it is height-balanced.
    
    <div markdown="1" style="box-sizing: border-box; margin: 0px 0px 10px; color: rgb(51, 51, 51); font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 1; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); font-size: 14px;">
    
    For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of *every* node never differ by more than 1.
    
    

考点：

1，考察各种边界条件

2，考察递归以及对树的遍历

3，考察求解树的高度

思路一：递归

高度平衡树，每一个节点的左右子树的深度最大相差1，可以递归，左右子树是否是平衡的，以此类推；结束条件是，如果root空，return true，如果root左右子树都是空（0，0），则return true，如果左右

vertion1.0: 错的
    
    
    class Solution {
    
    public:
    
    bool isBalanced(TreeNode* root) {
    
    if(!root) return true;
    
    int max = heightOfTree(root->left) > heightOfTree(root->right) ? heightOfTree(root->left) : heightOfTree(root->right);
    
    int min = heightOfTree(root->left) < heightOfTree(root->right) ? heightOfTree(root->left) : heightOfTree(root->right);
    
    if(abs(heightOfTree(root->left) - heightOfTree(root->right)) <= 1) {
    
    if(isBalanced(root->left) && isBalanced(root->right)) {
    
    return true;
    
    }
    
    }
    
    return false;
    
    }
    
    
    
    
    int heightOfTree(TreeNode* root) {
    
    if(!root || (root->left == nullptr && root->right == nullptr)) return 0;
    
    return heightOfTree(root->left) > heightOfTree(root->right) ? heightOfTree(root->left)+1 : heightOfTree(root->right)+1;
    
    }
    
    };
    
    
    

[1,null,2,null,3]，输出应该是false，但是我的输出是true

version1.1:
    
    
    class Solution {
    
    public:
    
    bool isBalanced(TreeNode* root) {
    
    if(!root || (root->left == nullptr && root->right == nullptr)) return true;
    
    int max = heightOfTree(root->left) >= heightOfTree(root->right) ? heightOfTree(root->left) : heightOfTree(root->right);
    
    int min = heightOfTree(root->left) < heightOfTree(root->right) ? heightOfTree(root->left) : heightOfTree(root->right);
    
    if(abs(heightOfTree(root->left) - heightOfTree(root->right)) > 1) {
    
    return false;
    
    } else if(!isBalanced(root->left) || !isBalanced(root->right)) {
    
    return false;
    
    }
    
    return true;
    
    }
    
    
    
    
    int heightOfTree(TreeNode* root) {
    
    if(!root || (root->left == nullptr && root->right == nullptr)) return 0;
    
    return heightOfTree(root->left) > heightOfTree(root->right) ? heightOfTree(root->left)+1 : heightOfTree(root->right)+1;
    
    }
    
    };
    
    
    

还是没有通过，一样的样例：[1,null,2,null,3]，输出应该是false，但是我的输出是true

version2.0：抄的网上的答案，通过，但是还不懂
    
    
    class Solution {
    
    public:
    
    int getHeight(TreeNode* root) {
    
    int left = 0, right = 0;
    
    if (!root || (!root->left &&!root->right))
    
    return 0;
    
    if (root->left != NULL)
    
    left = 1 + getHeight(root->left);
    
    if (root->right != NULL)
    
    right = 1 + getHeight(root->right);
    
    return max(left, right);
    
    }
    
    bool isBalanced(TreeNode* root) {
    
    if (!root || (!root->left && !root->right)) return true;
    
    int left = root->left == NULL ? 0 : getHeight(root->left) + 1;
    
    int right = root->right == NULL ? 0 : getHeight(root->right) + 1;
    
    if (abs(left - right) > 1)
    
    return false;
    
    else if (!isBalanced(root->left) || !isBalanced(root->right))
    
    return false;
    
    return true;
    
    }
    
    };
    
    
    

</div>
