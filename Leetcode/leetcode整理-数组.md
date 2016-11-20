---
title: 【数组】
tags: []
notebook: 0. 精华整理贴
---

前言： 主要内容包括：数组、链表、双指针、哈希表

# 1\. Array + TwoPointers

leetcode上Array的easy题有13道：

  * Remove Element
  * Two Sum
  * Contain Duplicate I && II
  * Rotate Array
  * Merge Sorted Array
  * Move Zeroes
  * Majority Element
  * Best Time to Buy and Sell Stock
  * Pascal's Triangle I && II
  * Plus One
  * Remove Duplicates from Sorted Array

主要包括以下两类：

1.元素操作

移除某个元素，两个元素和，是否存在重复元素，翻转数组，两个有序数组的融合；

2.特殊算法

翻转数组（逆波兰序），Plus One用数位表示的数字+1（主要考的是进位的处理），帕斯卡三角（纯数学），买卖股票最好时间（动态规划）；

\-----------------------下面是我的笔记-------------------------

## 1.1 题目解题思路+code

### Remove Element - *Array TwoPointers*

题目要求：
    
    
    Given an array and a value, remove all instances of that value in place and return the new length.
    
    Do not allocate extra space for another array, you must do this in place with constant memory.
    
    The order of elements can be changed. It doesn't matter what you leave beyond the new length.
    
    Example:
    Given input array nums = [3,2,2,3], val = 3
    
    Your function should return length = 2, with the first two elements of nums being 2.
    
    

算法一：暴力算法，使用新的数组

code：
    
    
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int new_length = 0;
            int length = nums.size();
            for(int i = 0;i < length;i++){
                if(nums[i] != val) nums[new_length++] = nums[i];
            }
            return new_length;
        }
    };
    
    

算法二：双指针法

code:
    
    
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int length = nums.size();
            int num = 0;
            for(int i = 0;i < length;i++){
                if(nums[i] != val) nums[num++] = nums[i];
            }
            return num;
        }
    };
    
    

key：双指针，in-place操作，只管数组的length个元素，后面的不管

### Two Sum - *Array HashTable TwoPointers*
    
    
    Given an array of integers, return indices of the two numbers such that they add up to a specific target.
    
    You may assume that each input would have exactly one solution.
    
    Example:
    Given nums = [2, 7, 11, 15], target = 9,
    
    Because nums[0] + nums[1] = 2 + 7 = 9,
    return [0, 1].
    
    
    

算法一：暴力算法

嵌套两个循环，遍历数组，找和为target，则记录这两个下标。但是开销太大，不是最好的方法。

code：
    
    
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            int length = nums.size();
            vector<int> indices;
            for(int i=0; i<length; i++){
                for(int j=i+1; j<length; j++){
                    if(nums[i]+nums[j] == target) {
                        indices.push_back(i);
                        indices.push_back(j);
                    }
                }
            }
            return indices;
        }
    };
    
    

可以看到，效率很低：

思路二：双指针 — made 是错的

排好序的前提下，left和right指针，如果sum *<* target，left++,否则Right--；但是如果没有排好序，就比较麻烦，需要记录下各个元素的下标。这里启发，只有排序过的，才比较好用双指针来判断大小问题。所以，涉及到既要对元素操作，又要对下标操作的，还是首选哈希表。
    
    
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            vector<int> result;
            int i = 0, j = nums.size() - 1;
            std::sort(nums.begin(), nums.end());
            while(i < j) {
                int sum = nums[i] + nums[j];
                if(sum == target) {
                    result.push_back(i);
                    result.push_back(j);
                    return result;
                } else if (sum < target) {
                    i++;
                } else {
                    j--;
                }
            }
            return result;
        }
    };
    
    

思路三：哈希表 从左往右扫描一遍，然后将数及坐标，存到map中。然后再扫描一遍即可。时间复杂度O(n) !!!错的！！！ 会重复计数
    
    
    for(int i =0; i< numbers.size(); i++)  
          {  
            int searched = target - numbers[i];  
            if(mapping.find(searched) != mapping.end())  
            {  
              result.push_back(i+1);  
              result.push_back(mapping[searched]+1);  
              break;  
            }  
          }  
    
    

正确的做法：一边循环一边加入，避免了重复计数的问题
    
    
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            // hash[i]表示nums中数值为i的下标
            unordered_map<int, int> hash;
            vector<int> result;
    
            // 一边循环每个数，一边加入hash表。
            for (int i = 0; i < nums.size(); i++) {
                if (hash.find(target - nums[i]) != hash.end()) {
                    // target - nums[i]的下标更小，放在前面
                    result.push_back(hash[target - nums[i]]);
                    result.push_back(i);
                    return result;
                }
                hash[nums[i]] = i;
            }
            return result;
        }
    };
    
    

key：避免重复计数，一边判断一边加入，这个也是常常碰到的问题

###  Contains Duplicate II - *Array HashTable*
    
    
    Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that nums[i] = nums[j] and the difference between i and j is at most k.
    
    

思路一：暴力算法 遍历每个元素，找后面k个。但是超时了，time limit exceeded

code： `bool containsNearbyDuplicate(vector<int>& nums, int k) { int n = nums.size(); for(int i=0; i<n; i++){ for(int j=i+1; j<k && j<n; j++){ if(nums[i] == nums[j]) return true; } } return false; }`

思路二：参考答案 使用hashmap，>使用value:key的组织方式，输入map的是nums[i]，map[nums[i]] = i，如果不在hashmap中，则加入，如果在，且满足条件，则返回。 code：
    
    
        bool containsNearbyDuplicate(vector<int>& nums, int k) {
            int n = nums.size();
            map<int, int> map;
            for(int i=0; i < n; i++){
                if(map.find(nums[i]) != map.end() && (i - map[nums[i]]) <= k) return true;
                else map[nums[i]] = i;
            }
            return false;
    
        }
    
    

### Contains Duplicate - *Array HashTable*
    
    
    Given an array of integers, find if the array contains any duplicates. Your function should return true if any value appears at least twice in the array, and it should return false if every element is distinct.
    
    

算法一：暴力算法： 比较每一个元素与后面的所有元素相比是否相等，但是超时了

算法二：改进算法： 先进行排序，然后比较相互两个元素是否相等 用到了std::sort函数，注意比较从i=0到length-1
    
    
    class Solution {
    public:
        bool containsDuplicate(vector<int>& nums) {
            int length = nums.size();
            std::sort(nums.begin(), nums.end());
            for(int i =0; i < length-1; i++){
                if(nums[i] == nums[i+1]) return true; 
            }
            return false;
        }
    };
    
    

### Rotate Array - *Array ReversePolish*
    
    
    Rotate an array of n elements to the right by k steps.
    
    For example, with n = 7 and k = 3, the array [1,2,3,4,5,6,7] is rotated to [5,6,7,1,2,3,4].
    
    Note:
    Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.
    
    

思路一：暴力算法 

开辟一个新数组，将后k个元素先放入新数组的前几个，检测到结尾了，再跳转到原数组的开头，分别将剩余的元素放入剩余的数组，直到n-k个

思路二：

逆波兰序 写法一：

直接用

```
std::reverse void rotate(vector<int>& nums, int k) { int n = nums.size(); k = k%n; std::reverse(nums.begin(), nums.end()-k); std::reverse(nums.end()-k, nums.end()); std::reverse(nums.begin(), nums.end()); }
```

  
写法二：  
  
```  
void my_reverse(vector<int>& vec, int left, int right) {  
if(left<0 || right>vec.size() || left>=right) return;  
int tmp = 0;  
while(left<right){  
tmp = vec[left];  
vec[left] = vec[right-1];  
vec[right-1] = tmp;  
left++;  
right--;  
}  
}  
void rotate(vector<int>& nums, int k) {  
int n = nums.size();  
k = k%n;  
my_reverse(nums, 0, n-k);  
my_reverse(nums, n-k, n);  
my_reverse(nums, 0, n);  
}  
```  
  
注意点：  
（1 k>n的时候，属于循环移位，应该要处理一下回到n的范围  
（2 自己写my_reverse的时候，注意交换要从right-1开始，遵循半开半闭的原则，与std::reverse的参数形式统一`

  

字符串的反转同理：

"Hello world abc"，逆波兰序，每一个单词反转，然后再总体反转。
  


思路三：http://blog.csdn.net/sunnyyoona/article/details/43941193 Cycle Sort（圈排序）思路 完全没看懂。。。

### Merge Sorted Array - *Array TwoPointers*
    
    
    Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.
    
    Note:
    You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2. The number of elements initialized in nums1 and nums2 are m and n respectively.
    
    

算法一：暴力算法： 对于每一个nums2中的元素，遍历去比较nums1，如果大于前一个而小于后一个则插入，但是插入的过程是很繁琐的，每次插入需要遍历nums1，并且后面的元素都得往后移动，开销非常大

另一个暴力算法：开辟一个新的数组，然后将两个数组分别比较并插入，从前往后，但是这样会造成大量的空间开销，而且题目说了nums1有充足的空间，没有必要重新开辟一个数组

考虑in-place的插入，但是如果从前往后，就编程思路一中的暴力算法，因此考虑双指针，从后往前插入，因为nums1的后面是空的，最坏的情况也就是剩余的n个元素被nums2占满，因此插入的过程并不会影响到nums1的还没有判断过的元素被覆盖的问题，因此可以用in-place的方法当做new一个新数组的方法

key：从后往前 in-place

coding record: while(j>=0){ nums1[p--] = i>=0 && nums1[i] > nums2[j] ? nums1[i--]: nums2[j--]; } 可以通过

但是while(j>=0 && i>=0){ nums1[p--] = nums1[i] > nums2[j] ? nums1[i--]: nums2[j--]; 却不行

解析：

  1. while条件 一定是nums2只要不为空，就需要进行比较和覆盖，比如当nums1的第一个元素比nums2中的某个大，这时候i小于0，因此下一步就不会跳入循环，nums2的剩余的最小的元素并没有继续写入nums1中的前面
  2. i>=0 && .... 作用是使得大小的判断为0，也就是说此时nums1[p] = nums2[j]，等价于 (i>=0 && nums1[i] > nums2[j]) 也可以看出，A>B?A:B 的机制是判断前面的结果，1则选冒号前面，0则选后面的

### Move Zeroes - *Array TwoPointers*
    
    
    Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.
    
    For example, given nums = [0, 1, 0, 3, 12], after calling your function, nums should be [1, 3, 12, 0, 0].
    
    
    

算法一：暴力算法

算法二：双指针法

一个指针指向0，一个指向下一个非零，如果i！=0，则i和j都++；如果i检测到了0，j没有，则交换；如果i检测到0，j也是0，j++；也是双指针的思想。
    
    
    class Solution {
    public:
        void moveZeroes(vector<int>& nums) {
            int n = nums.size();
            int i=0,j=1;
            while(n>=2 && j<n){
                if(nums[i]!=0){
                    i++;
                    j++;
                }else if(nums[i]==0 && nums[j]!=0){
                    int tmp = nums[i];
                    nums[i] = nums[j];
                    nums[j] = tmp;
                    j++;
                    i++;
                }else{
                    j++;
                    }
            }
        }
    
    };
    
    

### Majority Element - *Array HashTable Divide and Conquer*
    
    
    Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.
    
    You may assume that the array is non-empty and the majority element always exist in the array.
    
    
    

算法一：hashmap map进行统计，然后对每个map的元素进行判断。首先记录在map中，看哪个满足要求。
    
    
    class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            int n = nums.size();
            unordered_map<int, int> umap;
            for(int i = 0; i < n; i++){
                umap[nums[i]]++;
            }
            for(unordered_map<int, int>::iterator itr = umap.begin(); itr != umap.end(); itr++){
                if(itr->second > n/2) return itr->first;
            }
            return 0;
        }
    };
    
    

key：1. 对hashtable使用迭代器的时候，itr->first代表key，itr->second代表value；

### Remove Duplicates from Sorted Array - *Array TwoPointers*
    
    
    Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.
    
    Do not allocate extra space for another array, you must do this in place with constant memory.
    
    For example,
    Given input array nums = [1,1,2],
    
    Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.
    
    

思路一： 有序数组，只需要检测当前元素与后者是否相同，使用一个while循环，相同则移除后一个并保持，不同则向后处理一个元素。
    
    
        int removeDuplicates(vector<int>& nums) {
            if(nums.size()==0) return 0;
            int i = 0;
            while(i < (nums.size()-1)){
                if(nums[i] == nums[i+1]){
                    nums.erase(nums.begin()+i);
                } else{
                    i++;
                }
            }
            return nums.size();
        }
    
    

思路二： hash之后，取key，return nums.size()

思路三：（法哥）双指针。 两个思路：1. 跟slow比较，交换 2. 跟fast-1比较，覆盖 code1:
    
    
        int removeDuplicates(vector<int>& nums) {
            if(nums.size()==0) return 0;
            if(nums.size()==1) return 1;
            int slow = 0;
            int fast = 1;
            int n = nums.size();
            while(fast<n){
                if(nums[slow] == nums[fast]){
                    fast++;
                }else{
                    int tmp = nums[slow+1];
                    nums[slow+1] = nums[fast];
                    nums[fast] = nums[slow+1];
                    fast++;
                    slow++;
                }
            }
            return slow+1;
        }
    
    
    

key：1. 注意 int n = nums.size();一定不要忘记写int，已经出现过不下5次了; 2. 双指针法，典型操作；

### Best Time to Buy and Sell Stock - *Array DynamicProgramming*
    
    
    Say you have an array for which the ith element is the price of a given stock on day i.
    
    If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.
    
    Example 1:
    Input: [7, 1, 5, 3, 6, 4]
    Output: 5
    
    max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)
    Example 2:
    Input: [7, 6, 4, 3, 1]
    Output: 0
    
    In this case, no transaction is done, i.e. max profit = 0.
    
    

思路一：动态规划 f(0)=(0, 0), f(1)={(0, 0), (0, 1), (1, 1)}, f(2)={(0, 0), (0, 1), (0, 2), (1, 1), (1, 2), (2, 2)}={f(1), nums[n]-min(nums[0], ..., nums[i])}，然后就可以动态规划，使用一个for循环计算每一个的值，直到得到最后
    
    
    int maxProfit(vector<int>& prices) {
           int n = prices.size();
           vector<int> f(n);
           f[0] = 0;
           int minprice = prices[0];
           for(int i = 1; i < n+1; i++){
            minprice = min(prices[i-1], minprice)
               f[i] = std::max_element(f[i-1], (prices[i]-minprice));
           }
           return f[n+1];
       }
    
    

报错：`Line 8: invalid types ‘<unresolved overloaded function type>[__gnu_cxx::__alloc_traits<std::allocator<int> >::value_type {aka int}]’ for array subscript`

改进：

思路：dp[i+1] = max{dp[i], prices[i+1] - minprices} ,minprices是区间[0,1,2...,i]内的最低价格， 我们要求解的最大利润 = max{dp[0], dp[1], dp[2], ..., dp[n-1]}
    
    
    class Solution {
    public:
        int maxProfit(vector<int> &prices) {
            // IMPORTANT: Please reset any member data you declared, as
            // the same Solution instance will be reused for each test case.
            int len = prices.size();
            if(len <= 1)return 0;
            int res = prices[1] - prices[0], minprice = prices[0];
            for(int i = 2; i < len; i++)
            {
                minprice = min(prices[i-1], minprice);
                if(res < prices[i] - minprice)
                    res = prices[i] - minprice;
            }
            if(res < 0)return 0;
            else return res;
        }
    };
    
    

\------------------------------------------分割线---------------------------------------------

思路二： 记录当天之前的最低价，和当天之前的最大收益。比较当天-min>max? a:b
    
    
    class Solution {
    public:
        int maxProfit(vector<int>& prices) {
            if(prices.size()<=0) return 0;
            int n = prices.size();
            int min = INT_MAX;
            int max = INT_MIN;
            for(int i =0; i < n; i++){
                if(prices[i] < min){
                    min = prices[i];
                }
                if(prices[i]-min > max){
                    max = prices[i] - min;
                }
            }
            return max;
        }
    };
    
    

key: 1. 记录之前最小的单价，用当天最大-最小单价，是否为max利润，遍历一遍即可，没有用到动态规划；

### Pascal's Triangle I - *Array*
    
    
    Given numRows, generate the first numRows of Pascal's triangle.
    
    For example, given numRows = 5,
    Return
    
    [
         [1],
        [1,1],
       [1,2,1],
      [1,3,3,1],
     [1,4,6,4,1]
    ]
    
    

暴力算法： 第一行和第二行特殊，单独讨论。如果numRows>3，在前两行的基础上，填充中间的数字。 重点在于：for(int j = 1; j < i; j++) vec1.push_back(final[i-1][j-1] + final[i-1][j]); 这个关系式只要递推几个就可以知道。

代码如下：Accepted
    
    
        vector<vector<int>> generate(int numRows) {
            vector<vector<int>> final;
            // numRows == 0 && 1 && 2
            if(numRows == 0) return final;
            if(numRows == 1){  // change to: numRows >= 1
                vector<int> vec;
                vec.push_back(1);
                final.push_back(vec);
            }
            if(numRows == 2){ // change to: numRows >= 2
                vector<int> vec;
                vec.push_back(1);
                final.push_back(vec);
                vec.push_back(1);
                final.push_back(vec);
            }
            if(numRows >= 3){
                // first row /*
                vector<int> vec;
                vec.push_back(1);
                final.push_back(vec);
                // second row
                vec.push_back(1);
                final.push_back(vec);
                // later */
                for(int i = 2; i < numRows; i++){
                vector<int> vec1;
                vec1.push_back(1);
                for(int j = 1; j < i; j++){
                    // fill in the inner vector
                    vec1.push_back(final[i-1][j-1] + final[i-1][j]);
                }
                vec1.push_back(1);
                final.push_back(vec1);
            }
            }
    
            return final;
        }
    
    

思路二： 单独考虑numRows==0，其次，都是>0的之后，直接压入[1]，当numRows>=2时，再对每一个tmp增加一个元素（增元），然后对1到i的元素进行计算，等所有需要被计算的元素计算完毕之后，压入该tmp，再循环，增元，计算，因此是两个for循环的嵌套。 另一个重要的思路就是，先建立，后填值的思想。思路二中是对于每一个向量进行，思路三中就是对整个二元数组进行创建，然后在修改每个值，更加简便。

code：
    
    
        vector<vector<int>> generate(int numRows) {
            vector<vector<int>> final;
            if(numRows == 0) return final;
            vector<int> tmp(1,1);
            final.push_back(tmp);
            //if(numRows >= 2){
                for(int i = 2; i <= numRows; i++){
                    tmp.push_back(0);
                    vector<int> cur(tmp);
                    for(int j = 1; j < i; j++){
                        cur[j] = tmp[j-1] + tmp[j];
                    }
                    final.push_back(cur);
                    tmp = cur;
                }
            //}
            return final;
        }
    
    

思路三： 先填值，再修改。

code:
    
    
        vector<vector<int>> generate(int numRows) {
            vector<vector<int>> final;
            vector<int> tmp;
            if(numRows == 0) return final;
            // construct the whole structure
            for(int i=0; i < numRows; i++){
                tmp.resize(i+1);
                final.push_back(tmp);
            }
            // boundary conditions
            for(int i=0; i < numRows; i++) {
                final[i][0] = 1;
                final[i][i] = 1;
            }
            // fill in the numbers
            for(int i=2; i < numRows; i++){
                for(int j=1; j < i; j++){
                    final[i][j] = final[i-1][j-1] + final[i-1][j];
                }
            }
            return final;
        }
    
    

### Pascal's Triangle II - *Array*
    
    
    Given an index k, return the kth row of the Pascal's triangle.
    
    For example, given k = 3,
    Return [1,3,3,1].
    
    Note:
    Could you optimize your algorithm to use only O(k) extra space?
    
    

思路一：暴力算法 构建出帕斯卡三角，然后取出想要的行就可以，直观。
    
    
    class Solution {
    public:
        vector<int> getRow(int rowIndex) {
            vector<vector<int>> final;
            vector<int> tmp;
            if(rowIndex < 0) return tmp;
            // construct the whole structure
            for(int i=0; i <= rowIndex; i++){
                tmp.resize(i+1);
                final.push_back(tmp);
            }
            // boundary conditions
            for(int i=0; i <= rowIndex; i++) {
                final[i][0] = 1;
                final[i][i] = 1;
            }
            // fill in the numbers
            for(int i=2; i <= rowIndex; i++){
                for(int j=1; j < i; j++){
                    final[i][j] = final[i-1][j-1] + final[i-1][j];
                }
            }
            return final[rowIndex];
        }
    };
    
    

思路二：O(k) extra space

如果不构造全部的帕斯卡三角，则使用一个`vector<int>`进行覆盖，有一个问题，就是不断覆盖前一个元素，会导致计算后一个元素的时候，使用的是修改后的值，因此我们需要给两个vector，一个用来存储原来的那个，一个用来存储现在的。
    
    
    class Solution {
    public:
        vector<int> getRow(int rowIndex) {
            vector<int> tmp;
            vector<int> cur;
            if(rowIndex < 0) return cur;
    
            for(int i = 0; i <= rowIndex; i++) {
                cur.resize(i+2);
                tmp.resize(i+1);
                cur[0] = tmp[0] = 1;
                cur[i] = 1;
                cur[i+1] = 0;
                for(int j = 1; j < i; j++) {
                    cur[j] = tmp[j-1] + tmp[j];
                }
                tmp = cur;
            }
            cur.resize(rowIndex+1);
            return cur;
        }
    };
    
    

### Plus One - *Array Math*
    
    
    Given a non-negative number represented as an array of digits, plus one to the number.
    
    The digits are stored such that the most significant digit is at the head of the list.
    
    

思路一： 最初的想法：把一个数先变成十进制的表示方法，然后进行+1操作，之后再继续分解每一位，填入向量

问题在于：当number最后一位==9的时候，+1会进位，这时的n改变了，因此后面的分解就出现了错误 code：
    
    
        vector<int> plusOne(vector<int>& digits) {
            int n = digits.size();
            int number = 0;
            vector<int> vec;
            for(int i = 0; i < n; i++){
                number += digits[i]*pow(10,(n-1-i));
                if(digits[n-1]==9) n += 1;
            }
            number = number+1;
    
            for(int i = 0; i < n; i++){
                int p = pow(10,(n-1-i));
                vec.push_back(number/p);
                number = number % p;
            }
    
            return vec;
        }
    
    

\----------------------------------------分割线---------------------------------------------------- 思路二： 先对最后一位+1，然后分布操作，首先检测除了第一位的其他位，如果为10则当前为0，前位+1，然后再判断首位是否需要进位，然后申请新的vector，赋值。Accepted
    
    
    class Solution {
    public:
        vector<int> plusOne(vector<int>& digits) {
            int n = digits.size();
            digits[n-1] += 1;
            for(int i=n-1; i>0; i--){
                if(digits[i] == 10){
                    digits[i] = 0;
                    digits[i-1] += 1;
                }
            }
            if(digits[0] == 10){
                // new vector of n+1 to store
                vector<int> vec(n+1);
                vec[0] = 1;
                vec[1] = 0;
                for(int i=2; i<n+1; i++){
                    vec[i] = digits[i-1];
                }
                return vec;
            }else{
                return digits;
            }
        }
    };
    
    

key: 1. 进位的考虑比较麻烦；
















## 1.2 数组方法总结

对于数组，总结有两类的题型，一个是元素操作，一个特殊算法。元素操作包括：移除某个元素，两个元素和，是否存在重复元素，翻转数组，两个有序数组的融合；特殊算法包括：翻转数组（逆波兰序），Plus One用数位表示的数字+1（主要考的是进位的处理），帕斯卡三角（纯数学），买卖股票最好时间（动态规划）。

对于元素操作，我们通常使用双指针法；与计数有关的，考虑哈希表。下面聚两个例题。

### 1.2.1 数组 + 双指针

#### Remove Element - *Array TwoPointers*

题目要求：
    
    
    Given an array and a value, remove all instances of that value in place and return the new length.
    
    Do not allocate extra space for another array, you must do this in place with constant memory.
    
    The order of elements can be changed. It doesn't matter what you leave beyond the new length.
    
    Example:
    Given input array nums = [3,2,2,3], val = 3
    
    Your function should return length = 2, with the first two elements of nums being 2.
    
    

算法一：暴力算法，使用新的数组

code：
    
    
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int new_length = 0;
            int length = nums.size();
            for(int i = 0;i < length;i++){
                if(nums[i] != val) nums[new_length++] = nums[i];
            }
            return new_length;
        }
    };
    
    

算法二：双指针法

code:
    
    
    class Solution {
    public:
        int removeElement(vector<int>& nums, int val) {
            int length = nums.size();
            int num = 0;
            for(int i = 0;i < length;i++){
                if(nums[i] != val) nums[num++] = nums[i];
            }
            return num;
        }
    };
    
    

key：双指针，in-place操作，只管数组的length个元素，后面的不管

### 1.2.2 数组 + 哈希表

####  Contains Duplicate II - *Array HashTable*
    
    
    Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that nums[i] = nums[j] and the difference between i and j is at most k.
    
    

思路一：暴力算法 遍历每个元素，找后面k个。但是超时了，time limit exceeded

code：
    
    
        bool containsNearbyDuplicate(vector<int>& nums, int k) {
            int n = nums.size();
            for(int i=0; i<n; i++){
                for(int j=i+1; j<k && j<n; j++){
                    if(nums[i] == nums[j]) return true;
                }
            }
            return false;
        }
    
    

思路二：参考答案 使用hashmap，>使用value:key的组织方式，输入map的是nums[i]，map[nums[i]] = i，如果不在hashmap中，则加入，如果在，且满足条件，则返回。

code：
    
    
        bool containsNearbyDuplicate(vector<int>& nums, int k) {
            int n = nums.size();
            map<int, int> map;
            for(int i=0; i < n; i++){
                if(map.find(nums[i]) != map.end() && (i - map[nums[i]]) <= k) return true;
                else map[nums[i]] = i;
            }
            return false;
    
        }
    
    

### 1.2.1 数组 + 双指针 + 哈希表

#### Two Sum - *Array HashTable TwoPointers*
    
    
    Given an array of integers, return indices of the two numbers such that they add up to a specific target.
    
    You may assume that each input would have exactly one solution.
    
    Example:
    Given nums = [2, 7, 11, 15], target = 9,
    
    Because nums[0] + nums[1] = 2 + 7 = 9,
    return [0, 1].
    
    
    

算法一：暴力算法

嵌套两个循环，遍历数组，找和为target，则记录这两个下标。但是开销太大，不是最好的方法。

code：
    
    
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            int length = nums.size();
            vector<int> indices;
            for(int i=0; i<length; i++){
                for(int j=i+1; j<length; j++){
                    if(nums[i]+nums[j] == target) {
                        indices.push_back(i);
                        indices.push_back(j);
                    }
                }
            }
            return indices;
        }
    };
    
    

可以看到，效率很低：

思路二：双指针

排好序的前提下，left和right指针，如果sum *<* target，left++,否则Right--；但是如果没有排好序，就比较麻烦，需要记录下各个元素的下标。这里启发，只有排序过的，才比较好用双指针来判断大小问题。所以，涉及到既要对元素操作，又要对下标操作的，还是首选哈希表。
    
    
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            vector<int> result;
            int i = 0, j = nums.size() - 1;
            std::sort(nums.begin(), nums.end());
            while(i < j) {
                int sum = nums[i] + nums[j];
                if(sum == target) {
                    result.push_back(i);
                    result.push_back(j);
                    return result;
                } else if (sum < target) {
                    i++;
                } else {
                    j--;
                }
            }
            return result;
        }
    };
    
    
    

思路三：哈希表 从左往右扫描一遍，然后将数及坐标，存到map中。然后再扫描一遍即可。时间复杂度O(n) !!!错的！！！ 会重复计数
    
    
    for(int i =0; i< numbers.size(); i++)  
          {  
            int searched = target - numbers[i];  
            if(mapping.find(searched) != mapping.end())  
            {  
              result.push_back(i+1);  
              result.push_back(mapping[searched]+1);  
              break;  
            }  
          }  
    
    

正确的做法：一边循环一边加入，避免了重复计数的问题
    
    
    class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            // hash[i]表示nums中数值为i的下标
            unordered_map<int, int> hash;
            vector<int> result;
    
            // 一边循环每个数，一边加入hash表。
            for (int i = 0; i < nums.size(); i++) {
                if (hash.find(target - nums[i]) != hash.end()) {
                    // target - nums[i]的下标更小，放在前面
                    result.push_back(hash[target - nums[i]]);
                    result.push_back(i);
                    return result;
                }
                hash[nums[i]] = i;
            }
            return result;
        }
    };
    
    

key：避免重复计数，一边判断一边加入，这个也是常常碰到的问题

### 1.2.4 特殊算法 - 逆波兰序

#### Rotate Array - *Array ReversePolish*
    
    
    Rotate an array of n elements to the right by k steps.
    
    For example, with n = 7 and k = 3, the array [1,2,3,4,5,6,7] is rotated to [5,6,7,1,2,3,4].
    
    Note:
    Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.
    
    

思路一：暴力算法 开辟一个新数组，将后k个元素先放入新数组的前几个，检测到结尾了，再跳转到原数组的开头，分别将剩余的元素放入剩余的数组，直到n-k个

思路二：逆波兰序 写法一：直接用std::reverse `void rotate(vector<int>& nums, int k) { int n = nums.size(); k = k%n; std::reverse(nums.begin(), nums.end()-k); std::reverse(nums.end()-k, nums.end()); std::reverse(nums.begin(), nums.end()); }`

写法二：
    
    
        void my_reverse(vector<int>& vec, int left, int right) {
            if(left<0 || right>vec.size() || left>=right) return;
            int tmp = 0;
            while(left<right){
                tmp = vec[left];
                vec[left] = vec[right-1];
                vec[right-1] = tmp;
                left++;
                right--;
            }
        }
        void rotate(vector<int>& nums, int k) {
            int n = nums.size();
            k = k%n;
            my_reverse(nums, 0, n-k);
            my_reverse(nums, n-k, n);
            my_reverse(nums, 0, n);
        }
    
    

注意点：

（1 k>n的时候，属于循环移位，应该要处理一下回到n的范围

（2 自己写my_reverse的时候，注意交换要从right-1开始，遵循半开半闭的原则，与std::reverse的参数形式统一
