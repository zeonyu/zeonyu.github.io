
# 3. HashTable

leetcode相关题目有12题：

  * ~~Two Sum~~
  * Count Primes
  * Isomorphic Strings
  * Valid Sudoku
  * ~~Contains Duplicate I && II~~
  * Intersection of Two Arrays I && II
  * Valid Anagram
  * Happy Number
  * Bulls and Cows
  * Word Pattern

哈希表最重要的是 key-value 的思想，unordered_map不允许重复的key值

## 3.1 题目解题思路+code

### Count Primes
    
    ```
    Description:
    
    Count the number of prime numbers less than a non-negative number, n.
    ```
    

思路一：

质数的计算方法：除了本身和1，不能被其他任何数整除。判断条件就是用比他小的所有数整除。因此可以得到一个isPrime的函数用来判断是否为质数。然后主函数里循环，如果是质数，count++。 code：
    
```    
    class Solution {
    public:
        int countPrimes(int n) {
            int count = 0;
            for(int i = 0; i < n; i ++) {
                if(isPrime(i)) count++;
            }
            return count;
        }
    private:
        bool isPrime(int num) {
            if (num<=1) return false;
            for(int i=2; i*i <= num; i++) {
                if(num % i == 0) return false;
            }
            return true;
        }
    
    };
``` 

算法的结果是对的，但是对于 n 很大的情况，会超时：time limit exceeded，没有满足题目的要求。

思路二：[厄拉多塞筛法](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)


定义一个数组来储存哪个是质数，初始化为true。然后对每个元素，如果该数是质数，则其所有倍数都是非质数。对n个数都遍历一遍后，计算质数的个数。 code：
    
```
    class Solution {
    public:
        int countPrimes(int n) {
            vector<bool> isPrime(n, true);
    
            for(int i=2; i*i < n; i++) {
                if(isPrime[i] == false) continue;
                for(int j=i*2; j < n; j+=i) { // j = i * i ：or j = i * 2
                    isPrime[j] = false;
                }
            }
            int count = 0;
            for(int i=2; i < n; i++) {
                if (isPrime[i] == true) count++;
            }
            return count;
        }
    };
```



效率比较靠后,修改条件：j = i * i 

思考：

  * 为什么只考虑`i*i<n的情况：因为如果11*13>n`，而最大的质数只可能出现在i^2处，如果这个都没有，那么剩余的就都是质数了

  * 需要从i=2开始计算，因为1不参与讨论，而2 3全部都是质数，因此可以只从i=2处开始

思路三：

对于一个i，如果有两个数的乘积`<n`，则为非质数。
    
```
    class Solution {
    public:
        int countPrimes(int n) {
            vector<bool> num(n,true);
            int i = 2;
            while (i * i < n){
                for (int j = 2; j*i < n; j++){
                    num[j*i] = false;
                }
    
                i++;
    
                while (num[i] == false && i*i < n){
                    i++;
                }
            }
            int res=0;
            for (int i=2;i<n;i++){
                if (num[i] == true){
                    res ++;
                }
            }
            return res;
        }
    };
 ```


由于使用了很多的循环，因此效率还是不高： 
<en-media hash="0dc95d75918ee39a2edc800fb955ea30" style="height: auto;" type="image/jpeg"/>

### Isomorphic Strings 同形字符串
    
 ```   
    Given two strings s and t, determine if they are isomorphic.
    
    Two strings are isomorphic if the characters in s can be replaced to get t.
    
    All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.
    
    For example,
    
    Given "egg", "add", return true.
    
    Given "foo", "bar", return false.
    
    Given "paper", "title", return true.
    
    Note:
    You may assume both s and t have the same length.
  ```  

hashmap心得：

  1. hashmap其实就是个python里的dic，支持key/value对，一般都与查找有关。 这里的重点在于要找的是一一对应，而不仅仅是多对一或者一对多，因此考虑两个map同时检测。 首先是一个for循环，对每个char进行判断。对每个元素，如果key在map中找不到，则建立一个相应的对应关系；如果找到了，还需要都是相互对应，即一一对应，如果没有一一对应，即其中一个不对应，那就设置为 false。

  2. 时刻牢记两个条件，找到没，对应没。这也是为什么hashmap中经常出现两个if嵌套的原因。比如：
  ```  
    else if(m1.find(s[i]) != m1.end() && m2.find(t[i]) != m2.end()){
                    if(m1[s[i]] != t[i] || m2[t[i]] != s[i]){
                        flag = false;
                        //break;
                    }
                }
   ``` 

  3. 一一对应，维护两个map
    
```
    class Solution {
    public:
        bool isIsomorphic(string s, string t) {
            bool flag = true;
            map<char, char> m1,m2;
            for(int i=0; i < s.size(); i++){
                if(m1.find(s[i]) == m1.end() && m2.find(t[i]) == m2.end()){
                    m1[s[i]] = t[i];
                    m2[t[i]] = s[i];
                }else if(m1.find(s[i]) != m1.end() && m2.find(t[i]) != m2.end()){
                    if(m1[s[i]] != t[i] || m2[t[i]] != s[i]){
                        flag = false;
                        //break;
                    }
                }else{
                    flag = false;
                }
            }
            return flag;
        }
    };
```

### Valid Sudoku

直接跳过，面试不会考这么复杂的数学推导。

### Intersection of Two Arrays
    
```
    Given two arrays, write a function to compute their intersection.
    
    Example:
    Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2].
    
    Note:
    Each element in the result must be unique.
    The result can be in any order.
```

很明显，这是一道开放题，可以有很多种解法。

算法一：set 先把nums1和nums2都转化为set，如果set2的元素在set1中，则加入result。但有一个问题，不知道如何检测set2是否在set1中。

wrong code：
    
```
    public:
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
            unordered_set<int> set1;
            unordered_set<int> set2;
            vector<int> result;
            for(int i=0; i < nums1.size(); i++) {
                if(set1.find(nums1[i]) == set1.end()) set1.insert(nums1[i]);
            }
            for(int i=0; i < nums2.size(); i++) {
                if(set2.find(nums2[i]) == set2.end()) set2.insert(nums2[i]);
            }
            for(int i=0; i < set2.size(); i++) {
                if(set1.find(set2[i])) {
                    result.push_back(set2[i]);
                    set2.erase(set2[i]);
                }
            }
            return result;
        }
```

set的方法：****
    
```
    class Solution {
    public:
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
            unordered_set<int> m(nums1.begin(),nums1.end());
            vector<int> ans;
            for(auto i:nums2){
                if(m.erase(i)){
                    ans.push_back(i);
                }
            }
            return ans;
        }
    };
```

效率非常好

思路同样是使用set，但是用法非常巧妙，对于nums1直接初始化一个 set，免去了后面还需要手动实现nums1加入set的操作，这个操作值得学习。对于nums2中的元素，如果set中可以erase，那么首先表明这个元素是在nums1中的，而且同时删除了这个元素，其实我觉得即使不用删除，直接检测在不在里面也是可以的，这个时候就可以放在res中。

方法二：双重条件

首先分析是两个数组，检测相同的元素，但是还不能有重复。两个条件。

如果nums1中 的元素在nums2中找到，说明有相同的元素，第一个条件满足；第二个条件，不能相同，那么有两个思路，一个是对数组（source）去重，另一个是对结果去重，可以检测如果在res就不加入，或者全部添加后再移除多余的。我们的这个方法使用第二个，如果相同的元素在res中已经有了，那么我们就不再加入。全部加入再移除多余的方法用在高层次语言，比如python，一行代码解决，但是c++的话需要多余的操作。

nums1中的元素，在nums2中，并且不再result中，则push_back。（该方法没有用到hashmap）

code：
    
```
    class Solution {
    public:
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
            vector<int> res;
            if(!nums1.empty() || !nums2.empty()) {
                for(int i=0;i<nums1.size();i++)
                    {
                        if(find(nums2.begin(),nums2.end(),nums1[i])!=nums2.end())//if nums1[i] is in nums2
                            if(find(res.begin(),res.end(),nums1[i])==res.end())// if nums[i] not in res
                                res.push_back(nums1[i]);
                    }
            }
                return res;
    
        }
    };
```

效率很低：
<en-media hash="1b328191f46fb76e2611b0966e880355" style="height: auto;" type="image/jpeg"/>

方法三：排序后对数组进行比较

先排序，移动直到两个数相等，如果不在res里，就push_back。这里的难点在于，如何检测是否在res中。这里的方法是如果res中上一个加入的元素与现在的元素相等，那就是多余，就不加，反之，则加入。
    
```
    class Solution {  
    public:  
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {  
            sort(nums1.begin(), nums1.end());  
            sort(nums2.begin(), nums2.end());  
            vector<int> res;  
            int i = 0, j = 0;  
            while (i < nums1.size() && j < nums2.size())  
            {  
                if (nums1[i] < nums2[j])  
                    i++;  
                else if (nums1[i] > nums2[j])  
                    j++;  
                else  
                {
                    if (res.size() == 0 || res.back() != nums1[i])  
                        res.push_back(nums1[i]);  
                    i++;  
                    j++;  
                }  
            }  
            return res;  
        }  
    };
```

注意这个条件：`res.size() == 0 || res.back() != nums1[i]`，缺一不可。因为res.back的前提是res不为空

效果很赞

方法四：hashmap

求两个数组的交集（其结果是一系列数字）。只要数组2中的数字出现在数组1中，他就是交集。
    
```
    class Solution {  
    public:  
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {  
            vector<int> result;  
            unordered_map<int,int> mapping;  
            for(int i=0;i < nums1.size();i++)  
                mapping.insert(make_pair(nums1[i],i+1));  
            for(int i=0;i < nums2.size();i++)  
            {  
                if(mapping[nums2[i]]>0)//找到了交集的数字，获取结果  
                {  
                    mapping[nums2[i]]=0;//清零  
                    result.push_back(nums2[i]);  
                }  
            }  
            return result;     
        }  
    };  
```

效率还不错

方法五：

nums1加入map，value为true，意思是nums1中的元素存在，且没有被使用过。对于nums2中的元素，如果在map中找到了，并且值为true，就是还没有使用过，那么加入res，同时设置value为false；如果值为false，则意味着已经加入过了，就不再加入。这属于对数组（source）也操作。
    
```
    class Solution {
    public:
        vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
    
            if(nums1.size() == 0 || nums2.size() == 0){
                return vector<int>();
            }
    
            map<int,bool> map;
            vector<int> res;
    
            for(int val:nums1){
                map[val] = true;
            }
    
            for(int val:nums2){
                if(map.count(val) && map[val] == true){ //找到了，并且值为true
                    res.push_back(val);
                    map[val] = false;
                }
            }
            return res;
        }
    };
```

效率真心不咋的

### Intersection of Two Arrays II
    
```
    Given two arrays, write a function to compute their intersection.
    
    Example:
    Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2, 2].
    
    Note:
    Each element in the result should appear as many times as it shows in both arrays.
    The result can be in any order.
    Follow up:
    What if the given array is already sorted? How would you optimize your algorithm?
    What if nums1's size is small compared to nums2's size? Which algorithm is better?
    What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?
```

思路一：九章算法 双指针

对两个数组排序，然后双指针，分别指向头，如果其中一个指针指向的元素小，则该指针后移一个，否则他们相等，那就push_back，然后两个指针都++
    
```
    class Solution {
    public:
        vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
            // Write your code here
            sort(nums1.begin(), nums1.end());
            sort(nums2.begin(), nums2.end());
    
            vector<int> intersect;
            vector<int>::iterator it1 = nums1.begin(), it2 = nums2.begin();
            while ((it1 != nums1.end()) && (it2 != nums2.end()))
            {
                if (*it1 < *it2) it1++;
                else if (*it1 > *it2) it2++;
                else 
                {
                    intersect.push_back(*it1); 
                    it1++; it2++;
                }
            }
            return intersect;
        }
    };
```

效率可观： 
<en-media hash="67fa56003e36c58817e3908c7aa598e7" style="height: auto;" type="image/jpeg"/>

思路二：哈希表 single hashmap

其实这里说的也是一一对应的关系，但是不能所有的元素都加入哈希表后才搜索，这样就会出现重复搜索的情况，所以应该是先检测，再加入的原则。与之前的 TwoSum 很像。

单一hashmap，就是nums1的元素计数+1，nums2元素计数-1。对于nums1中的元素，检测到`<0`，则表示nums2中有一个原则与之对应，因此加入result，对于nums2中的元素，检测到`>0`，则表示nums1中有一个原则与之对应，因此加入result。

code：
    
```
    class Solution {
    public:
        vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
            map<int, int> map;
            vector<int> result;
            if(!nums1.size() || !nums2.size()) return result;
            int n = nums1.size() > nums2.size() ? nums1.size() : nums2.size();
    
            for(int i = 0; i < n; i++) {
                if(i < nums1.size()) {
                    if(map[nums1[i]] < 0) {
                        result.push_back(nums1[i]);
                    }
                    map[nums1[i]]++;
                }
                if(i < nums2.size()) { // wrong point： 可能出现数组越界 if(nums2[i]) 是错的
                    if(map[nums2[i]] > 0) {
                        result.push_back(nums2[i]);
                    }
                    map[nums2[i]]--;
                }
            }
            return result;
        }
    };
```

效率非常低： 
<en-media hash="1d9e075defe38f234f0e5f8b61d4c8cb" style="height: auto;" type="image/jpeg"/>

思路三：single array 计数

这里提一下方法，使用一个10位的数组，分别代表0-9，然后对nums1中的元素对应位置+1，nums2中-1。同时检测这个数组，对于nums1，如果`<0`则代表nums2中有一个与之对应，对于nums2，如果`>0`则代表nums1中有一个与之对应,那么push_back进result。

这个方法与思路二非常类似，但是我想用另一个例题来讲。这里暂时略过。

思路四：nums1 建立hashmap，nums2 查找
    
```
    class Solution {  
    public:  
        vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {  
            if(nums1.size()==0 || nums2.size()==0) return vector<int>();  
            vector<int> result;  
            unordered_map<int, int> hash;  
            for(auto val: nums1) hash[val]++;  
            for(auto val: nums2)  
            {  
                if(hash.count(val) && hash[val]>0) result.push_back(val);  
                hash[val]--;  
            }  
            return result;  
        }  
    };  
```

效率一般： 
<en-media hash="a540b2d179ed6d543b294a35d8d9d9c5" style="height: auto;" type="image/jpeg"/>

### Valid Anagram 由颠倒字母顺序而构成的字 -*HashTable Sort*
    
```
    Given two strings s and t, write a function to determine if t is an anagram of s.
    
    For example,
    s = "anagram", t = "nagaram", return true.
    s = "rat", t = "car", return false.
    
    Note:
    You may assume the string contains only lowercase alphabets.
```

思路：很明显就是计算一个字符串中每个元素出现过的次数。hashmap。

方法一：

暴力算法：用两个map统计每个string中每个字母出现的次数，然后比较两个map是否相同。 第一次写代码的心得： * 统计次数的代码很繁琐
    
```
    for(int i=0; i < s.size(); i++) {
                if(map.find(s[i]) == s.end()) {
                    // not in map
                    map.insert(make_pair(s[i],1));      
                } else {
                    // in map and count++
                    map[s[i]]++;
                }
            }
```

人家的方法，学习参考一下：
    
```
    for (int i = 0; i < s.size(); i++) {  
                countsmap[s[i]]++;  
                countsmap[t[i]]--;  
            }  
```

通过这个改进后，代码accepted：
    
```
    class Solution {
    public:
        bool isAnagram(string s, string t) {
            if(s.size() != t.length()) return false;
            unordered_map<char, int> map;
            for(int i=0, j=0; i < s.size(); i++) {
                map[s[i]]++;
                map[t[i]]--;
            }
    
            for(unordered_map<char, int>::iterator ite=map.begin(); ite != map.end(); ite++) {
                if(ite->second) return false;
            }
            return true;
        }
    };
```

效率居中：
<en-media hash="1b7caf32eb6179f688b030b2389c4c66" style="height: auto;" type="image/jpeg"/>

key：
1. 检测长度是否一样，不一样的直接返回false；
2. 每个char的count，在s中则++，t中则--，非常巧妙；
3. map中的元素如果不存在，会自动调用构造函数（虽然我并没有查到构造函数中有这一项），插入一个key对应的pair，并且有对应的value（应该默认是0），这样在下次对map进行操作的时候就不用检测是不是在这么繁琐；
4. string有两个method：length和size都是返回大小，还有begin和end；


方法二：排序后一致

思路：如果只是字母顺序，排序后是一样的；就是说，字母也是可以排序的！！通过ACSII码值

code:
    
```    class Solution {
    public:
        bool isAnagram(string s, string t) {
            if(s.length() != t.length()) return false;
            sort(s.begin(), s.end());
            sort(t.begin(), t.end());
            return s==t;
        }
    };
```

排序的速度竟然这么慢，有一点震惊。
<en-media hash="d145ed48eeb69dc20a8a20103d308137" style="height: auto;" type="image/jpeg"/>

key：1. sort函数：对int型的sort默认是升序排列；2. char也是一个码，可以加减，可以排序！！

思路三： 也是计算的思想，与'a'相比，每个字母的差值一定，也对应每个字母出现的次数，同理，s+1，t-1，然后检测这个数组是否都是0。这是数组用来计数问题的经典操作。

code:
    
```
    class Solution {
    public:
        bool isAnagram(string s, string t) {
            if(s.length() != t.length()) return false;
            vector<int> cnt(26, 0);
            for(int i=0; i < s.length(); i++) {
                cnt[s[i]-'a']++;
                cnt[t[i]-'a']--;
            }
            for(int i=0; i < 26; i++) {
                if(cnt[i] != 0) return false;
            }
            return true;
        }
    };
```

效率超过67%，很不错：
<en-media hash="431300e4f3cd7f61808555b1b9e86cba" style="height: auto;" type="image/jpeg"/>

### Happy Number
    
```
    Write an algorithm to determine if a number is "happy".
    
    A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.
    
    Example: 19 is a happy number
    
    12 + 92 = 82
    82 + 22 = 68
    62 + 82 = 100
    12 + 02 + 02 = 1
```

思路一：（完全参考答案）（不甚理解） 首先考虑和为一，计算么个数位的字符和很简单，然后在循环，也很简单，这样框架就搭出来了。然后考虑不收敛一直循环的情况，设置一个set或map，每一次的和push进map，如果已经存在，则表示已经进入循环，直接返回false（但其实我觉得应该是true，因为题中说到循环的也是happy number）

key：
1. return很讲究，最后一定要加入return，而且while循环体中就需要包含true和false的情况；
2.

code:
    
```
    class Solution {
    public:
        bool isHappy(int n) {
            int total = 0;
            unordered_set<int> set;
            while(n) { 
                // cal once
                while(n) {
                    total += (n % 10) * (n % 10);
                    n = n / 10;
                }
                if(total == 1 /*|| */) return true;
                else if(set.find(total) != set.end()) return false; // 循环了也不是Happy
    
                n = total;
                set.insert(total);
                total = 0;
            }
            return false;
        }
    };
```



* * *

另一个代码，思路大同小异 code: 

```
bool isHappy(int n) { 
    unordered_set<int> visited; 
    while (n != 1) { 
        if (visited.find(n) != visited.end())  return false; 
        visited.insert(n); 
        int sum = 0; 
        while (n) { 
            sum += ((n % 10)*(n % 10)); 
            n /= 10; 
        } 
        n = sum; 
    } 
    return true; 
}
```

思路二： 完全不理解

code： 

```
class Solution { 
    public: 
    bool isHappy(int n) { 
        while(n>6){ 
            int next = 0; 
            while(n){ 
                next+=(n%10)*(n%10); n/=10; 
            } n = next; 
        } 
        return n==1; 
    } 
};
```

### Bulls and Cows
    
```
    You are playing the following Bulls and Cows game with your friend: You write down a number and ask your friend to guess what the number is. Each time your friend makes a guess, you provide a hint that indicates how many digits in said guess match your secret number exactly in both digit and position (called "bulls") and how many digits match the secret number but locate in the wrong position (called "cows"). Your friend will use successive guesses and hints to eventually derive the secret number.
    
    For example:
    
    Secret number:  "1807"
    Friend's guess: "7810"
    Hint: 1 bull and 3 cows. (The bull is 8, the cows are 0, 1 and 7.)
    Write a function to return a hint according to the secret number and friend's guess, use A to indicate the bulls and B to indicate the cows. In the above example, your function should return "1A3B".
    
    Please note that both secret number and friend's guess may contain duplicate digits, for example:
    
    Secret number:  "1123"
    Friend's guess: "0111"
    In this case, the 1st 1 in friend's guess is a bull, the 2nd or 3rd 1 is a cow, and your function should return "1A1B".
    You may assume that the secret number and your friend's guess only contain digits, and their lengths are always equal.
```

思路一：

注意：如果以`<char,int>`，则出现多个key重复，构建 map 的时候就覆盖了，所以用map的时候一定要注意key不能重复。

思路二：法哥 set 记录 secret 中的元素，对比字符串对应的元素，如果相等则是 A，如果不等但是在 set 中则为 B

写出来以后发现，出现一个问题，比如1234和0111，应该是0A1B，但是输出是0A3B，原因在于只有一个set不能反应guess中的重复出现的问题，因为每一个guess中的重复出现都会被计算一次，然后我尝试在找到之后删除了这个元素，结果引入了新的问题，比如1122和2211，答案应该是0A4B，但是由于我在搜索第一个2的时候把set中的2删除了，导致第二个2不能++。set貌似行不通。 由于考虑到对出现次数也有要求，考虑使用map。修改完后也不行，比如1122和1222，答案是3A0B，我的输出是3A1B，原因是构建map的时候应该是不相等的才构建。

code:
    
```
    class Solution {
    public:
        string getHint(string secret, string guess) {
            unordered_map<char, int> map;
            for(int i=0; i < secret.size(); i++) {
                if(secret[i] != guess[i]) map[secret[i]]++;
            }
            int a=0, b=0;
            for(int i=0; i < secret.size(); i++) {
                if(secret[i] == guess[i]) a++;
                else if(map.find(guess[i]) != map.end() && map[guess[i]] != 0) {
                    b++;
                    map[guess[i]]--;
                }
            }
            return to_string(a) + "A" + to_string(b) + "B";
        }
    };
```

思路三： 使用数组nums[n]来记录出现的次数。如果对应元素相等，则为A，如果不相等，此时第i个secret的+1，guess中的-1，即每一次都要标记；在这个作用域内，检测，如果secret中出现的位置为负，则表示之前已经guess中已经出现过，那么此刻就匹配上了，b++，对应位置++，如果guess的位置为正数，表示之前secret中已经出现过一次，那么此刻就匹配上了，b++，对应位置++；对于不在secret中的guess，则让他一直为负吧

code:
    
```
    class Solution {
    public:
        string getHint(string secret, string guess) {
            vector<int> nums(10, 0);// attention
            int a = 0, b = 0;
            for(int i=0; i < secret.length(); i++) {
                if(secret[i] == guess[i]) a++;
                else {
                    if(nums[secret[i]-'0'] < 0) b++;
                    if(nums[guess[i]-'0'] > 0) b++;
                    nums[secret[i]-'0']++;
                    nums[guess[i]-'0']--;
                }
            }
            return to_string(a) + "A" + to_string(b) + "B";
        }
    };
```

注意：两个易错点：
1. if(nums[secret[i]-'0']<0) 容易写成 if(nums[secret[i]]-'0'<0)； 
2. 一定要注意，开辟nums的是一定是要给10个空间，代表从0到9，而不是 2 * secret.length()，因为即使每个字符串都是4个字节，但是其中的数字可以超过8，而一旦超过8就出现了越界，不是我想的那种，完美的每个不同的字符占据一个空间，因为我们存储的时候是跟0比较的，所以应该是10的空间； 
3. 很多时候的小错误都是一些stupid的问题，细心检查，有时候不一定是算法的问题

### Word Pattern
    
```
    Given a pattern and a string str, find if str follows the same pattern.
    
    Here follow means a full match, such that there is a bijection between a letter in pattern and a non-empty word in str.
    
    Examples:
    pattern = "abba", str = "dog cat cat dog" should return true.
    pattern = "abba", str = "dog cat cat fish" should return false.
    pattern = "aaaa", str = "dog cat cat dog" should return false.
    pattern = "abba", str = "dog dog dog dog" should return false.
    Notes:
    You may assume pattern contains only lowercase letters, and str contains lowercase letters separated by a single space.
```

思路一：http://www.cnblogs.com/grandyang/p/4857022.html

istringstream 进行分割处理，双向hashmap
    
```
    class Solution {
    public:
        bool wordPattern(string pattern, string str) {
            unordered_map<char, string> m;
            istringstream in(str);
            int i = 0;
            for (string word; in >> word; ++i) {
                if (m.find(pattern[i]) != m.end()) {
                    if (m[pattern[i]] != word) return false;
                } else {
                    for (unordered_map<char, string>::iterator it = m.begin(); it != m.end(); ++it) {
                        if (it->second == word) return false;
                    }
                    m[pattern[i]] = word;
                }
            }
            return i == pattern.size();
        }
    };
```

解法二：
    
```
    class Solution {
    public:
        bool wordPattern(string pattern, string str) {
            unordered_map<char, int> m1;
            unordered_map<string, int> m2;
            istringstream in(str);
            int i = 0;
            for (string word; in >> word; ++i) {
                if (m1.find(pattern[i]) != m1.end() || m2.find(word) != m2.end()) {
                    if (m1[pattern[i]] != m2[word]) return false;
                } else {
                    m1[pattern[i]] = m2[word] = i + 1;
                }
            }
            return i == pattern.size();
        }
    };
```

思路二：

手动分割，取子字符串，双向hashmap
    
```
    class Solution {
    public:
        bool wordPattern(string pattern, string str) {  
        map<char,string> map1;  
        map<string,char> map2;  
        int left=0,right=0;  
        for(int i = 0 ;i<pattern.size(); i++){  
            while(right<str.size()&&str[right]!=' ') right++;  
            if(right==left) return false;  
            string tmpStr = str.substr(left,right-left);  
            if(map1.find(pattern[i])!=map1.end()){  
                if(map1[pattern[i]]!=tmpStr) return false;  
            }else if(map2.find(tmpStr)!=map2.end()){  
                if(map2[tmpStr]!=pattern[i]) return false;  
            }else{  
                map1[pattern[i]] = tmpStr;  
                map2[tmpStr] = pattern[i];  
            }  
            left = ++right;  
        }  
        if(right<str.size()) return false;  
        return true;  
    }  
    };
```

## 3.1 哈希表方法总结

哈希表常用的有：

  * `set <type>`
  * `unorder_set <type>`
  * `map <type, type>`
  * `unordered_map <type, type>`

哈希表主要与数组结合紧密，题目中都会相互用到。
